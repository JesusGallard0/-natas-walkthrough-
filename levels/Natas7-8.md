# Natas OverTheWire Level7-8

## Objective

Retrieve the password for the natas9 level

---

## Vulnerability Analysis

- Inspecting the PHP source code revealed and econded secret : 

    $encodedSecret = "3d3d516343746d4d6d6c315669563362";

- The source code also exposed the function used to transform the original secret:

    function encodeSecret($secret) 
        { return bin2hex(strrev(base64_encode($se    cret))); 
    } 

- Now that we know the encoding process the original secret could be recovered by reversing each transformation step in the opposite order

---

## Exploitation Process

- The encoding chain applied to the original secret was:

    base64_encode - strrev - bin2hex

- To recover the original value,the operations were reversed in the opposite order:

    hex decode - reverse string - base64 decode

1. Hex decoding

    echo "3d3d516343746d4d6d6c315669563362" | xxd -r -p
Output:

    ==QcCtmMml1ViV3b

2. Reverse the string

    echo "==QcCtmMml1ViV3b" | rev

Output: 
    
    b3ViV1lmMmtCcQ==

3. Base64 decode

    echo "b3ViV1lmMmtCcQ==" | base64 -d


Output:

    oubWYf2kBq

- The recovered secret was submitted through the form, revealing the password

---

### Observations and Findings

- Multiple encoding layers can often be reversed when the applied operations are known 

- Encoding does not provide security when the the transformation process is publicly exposed

---

## Key Concepts Learned

- Reversing transformation chains

