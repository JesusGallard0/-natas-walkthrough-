# Natas OverTheWire Level 6-7


## Objective 

Retrieve the password for the natas8 level

---

## Vulnerability Analysis

- Inspecting the page links:

    <a href="index.php?page=home">Home</a> 
    <a href="index.php?page=about">About</a>

The application dynamically loaded content through the page GET parameter

A comment in the HTML source revealed a hint:

    <!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->

Error messages generated during testing revealed the server-side path of the application

    /var/www/natas/natas7/index.php

- This confirmed that the application interacted directly with the linux filesystem

---

## Exploitation Process


- The page parameter was tested with different values to observe how the application handled the file paths

- Initially, invalid paths and directories were tested, generating PHP warnings and filesystem-related errors

- After analyzing the behavior the following payload was used


    index.php?page=/etc/natas_webpass/natas8

The application included the target file and revealed the password 


---

## Observations and Findings

- User controlled file inclusion may expose sensitive server-side files

- PHP warnings and error messages can disclouse useful internal information such as filesystem paths

- Linux paths can sometimes be abused when applications fail to validate user input properly

## Key Concepts Learned

- Local FIle Inclusion(LFI)

- GET parameter manipulation 

- PHP file inclusion behavior








