# Natas OverTheWire level12-13


## Objective

Retrieve the password for the next level

---

## Vulnerability Analysis

In the source code we see the server-side PHP validation logic

- File size check... no more than 1000bytes

- Image type check...exif_imagetype()

- No extension validation after upload

The server uses exif_imagetype() to verify the file as a real image by checking its magic bytes but never revalidates the file extension after the upload is accepted

So we can pass a real JPEG during upload but reanme the extension to .php before submission and inject some code in the JPEG

JPEG files contain EXIF metadata segments that store information such as camera model,GPS,and comments. These field accept arbitrary text.

By injecting PHP code into the comment field using exiftool, the payload becomes part of a legitimate JPEG file, it passes the magic byte check but contains executable PHP

---

## Exploitation Process

We prepare the file

A small JPEG file 997 bytes was used as the carrier

Using exiftools we reduce the file size so the script doesnt overpass the limit...maybe we could have increased the limit using burpsuite too but i decided to do this
    
    exiftool -all= 1kb.jpeg

This reduced the file to 437bytes 

To inject the code we do:

    exiftool -Comment='<?php echo file_get_contents("/etc/natas_webpass/natas14"); ?>' 1kb.jpeg


Now to upload it we just modify the extension using burpsuite intercepting the POST request

we just change the .jpeg extension to .php

This bypasses the exif_imagetype() check ,while the server saves the file with a .php extension that its going to be executed by the server

After the upload the server returned a link to the uploaded file 

Navigating directly to the file the page rendered as the image, using ctrl+u  revealed the PHP had executed and the PASSWORD was embedded in the output among the binary JPEG data

---

## Observations and Findings


exif_imagetype() only reads the first few magic bytes of a file it does not parse the entire file

The server preserved the file extension supplied by the client with no revalidation

---


## Key Concepts Learned

- JPEG files contain metadata segments where arbitrary text can be stored. Tools like exiftool help to inject code into these fields

- The resulting file is a plyglot file, simultaneously a valid JPEG image and a valid PHP script. This class of attack works because different parsers read the same file diferently

- Server-side validation that only checks  file content but not the final stored extension is insufficient


