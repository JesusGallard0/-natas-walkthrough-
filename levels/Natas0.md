# Natas OverTheWire Level 0

## Objective

Retrieve the password for the 'natas1' user.


## Vulnerability analysis

- The application exposed the password in the HTML source code.

- Even though the password is not visible in the rendered webpage ,it was still accessible to anyone inspecting the page source


## Exploitation process

- By inspecting the html source code of the website using the "ctrl+u" i found the password in plain text ,ready to use it for the next level

## Observations and findings

- Anything sent to the browser can be inspected by the user

-Client-side hidden data should never be considered secure

## Key concepts learned

- HTML source inspection

- Basic web application reconnaissance
