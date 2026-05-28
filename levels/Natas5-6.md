
# Natas OverTheWire Level 5-6


## Objective

Retrieve the password for the `natas7` level

---

## Vulnerability Analysis

- The first thing we see is 

- A line asking for a secret input 

- A link to a source code

- Inspecting the PHP source code revealed the following line

    include "includes/secret.inc";

- This indicates the application loaded the secret value from an external file stored on the server

- The application compared the user input against the value stored in the $secret variable

    if($secret == $_POST['secret'])

- The validation occurs in the server-side so directly bypassing the comparision from the client side was unlikely

- The file included suggested that the resource might be accesible directly through the web server

---

## Exploitation Process

- The source code was inspected through the provided view sourcecode option

- Identified an included path which might be on the server

     includes/secret.inc

- The resource was accessed through the browser 

- Opening the file revealed the secret value used by the application

- After submitting through the form, the password was revealed

---

## Observations and Findings

- Included server-side resources may still be accessible if improperly configured

- Source code disclosure can expose sensitive internal file paths


---

## Key Concepts Learned

- Server-side validation logic

- PHP include statements









