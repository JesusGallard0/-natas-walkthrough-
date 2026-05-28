# Natas OverTheWire Level 4-5

## Objective

Retrieve the password for the `natas6` user.

---

## Vulnerability Analysis

- The first thing the page shows us is this message:


    Access disallowed. You are not logged in.


- Inspecting the HTTP requests revealed a cookie named:

    loggedin=0

---


## Exploitation Process

- Using the browser devtools a cookie named loggedin=0 was identified,(using the firefox extension `Cookie-Editor` )an attempt was made to modify the cookie value from:

    loggedin=0
to:
    loggedin=1

The modification did not initially apply correctly

- So the original cookie was deleted and recreated manually with the value set to 1.

- After refreshing the page,access was granted and the password was revealed

---

## Observations and Findings

- Authentication decisions should never rely exclusively on client-side cookie values

- Cookies can be modified,deleted,or recreated by the user

---

## Key Concepts Learned

- Cookie inspection and manipulation

- Client-side authentication weaknesses



