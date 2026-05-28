# Natas OverTheWire Level 3-4

## Objective

Retrieve the password for the `natas5` user.


---

## Vulnerability Analysis

- The first thing the level show us is a message:

Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"

- Refreshing the page its clearer: 

Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"

---

## Exploitation Process

- Inspecting the HTML source code revealed a reference to index.php


    Access disallowed. You are visiting from "http://natas4.natas.labs.overt    hewire.org/" while authorized users should come only from "http://natas5    .natas.labs.overthewire.org/"
    <br/>
    <div id="viewsource"><a href="index.php">Refresh page</a></div>
    </div>
    </body>
    </html>


- Using the browser devtools and the Network tab the HTTP request headers were analyzed

- The request included the header:
    
    Referer: http://natas4.natas.labs.overthewire.org/
    
But the application expected requests coming from:
    
    Referer: http://natas5.natas.labs.overthewire.org/



The referer header was modified using BurpSuite

- In Burp Suite

    - Proxy interception was enabled.

    - The request was intercepted before reaching the server

    - We configured a new rule in Match and Replace to replace the referer:

    Referer: http://natas4.natas.labs.overthewire.org/

    with: 

    Referer: http://natas5.natas.labs.overthewire.org/

- After forwarding in Burp Suite access was granted and the password was revealed


---

## Observations and Findings

- HTTP headers can be manipulated by the client

- The Referer header should not be used for authentication or authorization purposes

- Burp Suite can be used to intercept and modify HTTP traffic

---

## Key Concepts Learned

- HTTP header manipulation

- Referer header validation weaknesses

- Burp Suite proxy interception



