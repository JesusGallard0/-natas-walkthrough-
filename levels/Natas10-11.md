# Natas OverTheWire Level10-11


## Objective

Retrieve the password for the next level

---

## vulnerability Analysis


The application stores user preferences inside a cookie named data

In the source code we see:

    $defaultdata = array(
        "showpassword"=>"no",
        "bgcolor"=>"#ffffff" 
    );

The page reveals the password only when:

    if($data["showpassword"] == "yes")

Cookie data is protected using a custom XOR-based encryption function:

    function xor_encrypt($in) {
        $key = '<censored>';

        for($i=0;$i<strlen($in);$i++) {
            $outText .= $in[$i] ^ $key[$i % strlen($key)];
        } 

        return $outText;

    }

The application saves cookies as:

    JSON → XOR Encryption → Base64 Encoding → Cookie


and loads them as:

    Cookie → Base64 Decode → XOR Decrypt → JSON Decode

The vulnerability is that the application uses a repeatung key XOR cipher to protect the client controlled data

- The encryption algorithm is known

- The cookie ciphertext is available to the user

- Part of the plaintext is known from the source code

We can recover the repeating XOR key and change the cookie content

---

## Exploitation Process

In the source code we see the target:


    {
      "showpassword":"yes",
      "bgcolor":"#ffffff" 

    }



Next, we obtain the cookie value 


    data=HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg=

As we can see the cookie contains XOR-encrypted JSON encoded with base64

    base64_encode(xor_encrypt(json_encode($d)))



The source code reveals the default state:

    {"showpassword":"no","bgcolor":"#ffffff"}

This is known plain text

Therefore knowing plaintext and ciphertext allows recovery of the XOR-key 


A PHP script was used to reproduce the XOR operation and compare known plaintext against the cookie ciphertext

    <?

    function xor_encrypt($in) {
        $key = json_encode(array(
            "showpassword"=>"no",
            "bgcolor"=>"#ffffff"
        ));

        $text = $in;
        $outText = '';

        for($i=0;$i<strlen($text);$i++) {
            $outText .= $text[$i] ^ $key[$i % strlen($key)];
        }

        return $outText;
    } 

    $cookie = "HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg%3D";

    echo xor_encrypt(base64_decode($cookie)); 

    ?>



The resulting output revealed a repeating pattern

    eDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWo...

From this pattern the XOR key was determined to be eDWo


Now we can forge the cookie 

Once the XOR key was recovered, a new JSON object was created

    {
      "showpassword":"yes",
      "bgcolor":"#ffffff"
    }

And we used another script to encrypt it using the recovered key

    <?

    function xor_encrypt($in) {
        $key = "eDWo";

        $text = $in;
        $outText = '';

        for($i=0;$i<strlen($text);$i++) {
            $outText .= $text[$i] ^ $key[$i % strlen($key)];
        }

        return $outText;

    } 

    echo base64_encode(
        xor_encrypt(
            json_encode(array(
                "showpassword"=>"yes",
                "bgcolor"=>"#ffffff"
            )) 
        ) 
    );

    ?>

    

And we got 
    
    HmYkBwozJw4WNyAAFyB1VUc9MhxHaHUNAic4Awo2dVVHZzEJAyIxCUc5


Then we just replace the cookie

and satisfied the condition:

    if($data["showpassword"] == "yes")

And refreshing the page revealed the password 


## Observations and Findings


Repeating key XOR is vulnerable to known-plaintext attacks


Because the plaintext was largely predictable the key could be recovered

The source code revealed 

- Cookie format

- Encryption algorithm

- Expected JSON structure

## Key concepts learned

A simple encryption method where a short key is reused is weak against known-plaintext attacks

A Known-Plaintext Attack is when an attacker knows both 

- Plaintext

- Ciphertext

and uses that information to recover the encryption key



