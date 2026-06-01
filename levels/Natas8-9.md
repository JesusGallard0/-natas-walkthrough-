# Natas OverTheWire Level8-9

## Objective

Retrieve the password for the natas10 level

---

## Vulnerability Analysis

- Inspecting the PHP source code we that the user input was directly passed into a Linux shell command.

    if($Key != "") {
        passthru("grep -i $Key dictionary.txt");
    }

- The value of $Key is the user input

    $Key = $_REQUEST["needle"];

- Since the application inserted unsanitized user input directly into a shell command, the application was vulnerable to a command injection

- The structure of the command executed by the server was:

    grep -i <user_input> dictionary.txt

--- 


## Exploitation Process


- Initially, normal words were submitted through the search to observe the application behavior

- After analyzing the source code,it was identified that the input field was part of a Linux command executed by passthru()

- We used a command separator ; to chain commands in a linux environment to test the behavior...

  ;cat /etc/natas_webpass/natas10;

- The injected cat command executed succesfully and displayed the password 

---

## Observations and Findings

- shell metacharacters such as ; may allow attackers to chain additional commands

- PHP functions such as passthru() can become highly dangerous when combined with unvalidated input

- Applications should never concatenate user input into system commands

---

## key Concepts Learned

- Command injection

- PHP passthru behavior


