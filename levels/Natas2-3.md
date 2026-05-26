# Natas OverTheWire Level 2-3

## Objective

Retrieve the password for the `natas4` user.


## Vulnerability Analysis

- Inspecting the HTML source code, we see a comment: 

"No more information leaks!! Not even Google will find it this time..."

- This may suggest that the sensitive content exists somewhere on the server but it could be hidden from the search engine indexing

- Web applications commonly use a robots.txt file to prevent certain directories or resources from being indexed in search engines

- However, robots.txt does not restrict direct user access to the listed resources

## Exploitation Process

- The robots.txt file was inspected by navigating to: 

   view-source:http://natas3.natas.labs.overthewire.org/robots.txt

- This revealed the existence of a hidden directory named "s3cr3t"

    User-agent: *
    Disallow: /s3cr3t/

- Accessing to the directory revealed a directory listing containing a file named "users.txt" which contained the password for `natas4`.


## Observations and Findings

- robots.txt should not be used to hide sensitive resources

- Directories excluded from indexing may still be publicly accessible

- Directory listing may unintentionally expose confidential files



## Key Concepts Learned

- robots.txt enumeration

- Search engine indexing behavior

- Directory enumeration

