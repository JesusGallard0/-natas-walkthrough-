# Natas OverTheWire Level9-10

## Objective

Retrieve the password for the natas11 level

---

## Vulnerability Analysis

-The source code revealed that user input was still being passed directly into a shell command:

    if($key != ""){ 
        if(preg_match('/[;|&]/',$key)) {
            print "Input contains an illegal character!";
        } else {
            passthru("grep -i $key dictionary.txt"); 

        } 

    }

- Unlike the previous level,a blacklist was introduced to block the characters:

    ;
    |
    &

- This prevented the command chaining technique used in the prevous level

- However, user input was still passed directly to the grep command without sanitization

---

## Exploitation Process

- The first approach was to look for alternative ways to chain commands,since the most common shell separators blocked

- After further analysis, i shifted my focus to understand how grep itself behaved

- To better understand the availabe options i reproduced myself the command locally and reviewed documentation

- This led me to experiment with recursive searches using the -r option

- By supplying additional arguments through the input field, it was possible to make grep search files outside of the intended dictionary.txt file

- During test the input used was 

    -r "s" /etc/natas_webpass/natas11

- This caused grep to search recursively and inspect the password file directly

- Since the password contained the letter "s",the line matched and was displayed in the output

    /etc/natas_webpass/natas11:UJdqkK1pTu6VLt...

- The displayed value was the password 

---

## Observations and Findings

- Blacklisting a small set of characters does not eliminate the underlying vulnerability

- User input can be abused even when direct command chaining is blocked

---

## Key Concepts Learned

- Argument injection

- Insecure blacklist filtering

- grep command behavior




  
