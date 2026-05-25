# Natas OverTheWire Level 1-2

## Objective 

Retrieve the password for the `natas3` user


## Vulnerability Analysis

The page told us that there was nothing but:

- Inspecting the HTML source code, a hidden image reference (`pixel.png`) was discovered 

- The image was stored inside a publicly accesible `/files` directory listing enabled.

- Improper server configuration allowed users to browse the content directory and access possible sensitive files directly 

## Exploitation Process

- The HTML source code was inspected to identify hidden resources.

- A reference to a file was found (`pixel.png`)

- Navigating to the image revealed that the /files directory had directory indexing enabled
- An exposed file called users.txt was found

-The file users.txt contained the natas3 password in plain text

## Observations and Findings

- Directory listing can expose sensitive internal files if improperly configured

- Hidden resources referenced in HTML source code may reveal additional attack surfaces

- Default web server configurations can introduce information disclosure vulnerabilities

## key Concepts Learned

- Apache directory indexing

- Information disclosure vulnerabilities

- Sensitive file exposure

