# Natas OverTheWire level 13-14 

## Objective

Retrieve the password for the next level

---

## Vulnerability Analysis

in this case the application has a login form requiring a username and password

in the source code we see:

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";


The user username and password are concatenated directly into an SQL query without any sanitization

This means an attacker can inject SQL syntax into either field and alter the logic of the query

we see the application checks:

    if(mysqli_num_rows(mysqli_query($link, $query)) > 0)

if at least one row is returned,authentication succeds

---

## Exploitation Process

In the source code we see:

    if(array_key_exists("debug", $_GET)) {
    echo "Executing query: $query<br>";
    }

i initially added ?debug=1 in the URL but the parameter was not added once submitted credentials

So i used burpsuite to intercept the POST request and change that parameter 

testing i tried just " in the username and the application responded with:

     Warning: mysqli_num_rows() expects parameter 1 to be mysqli_result, boolean given



So i tried the " OR 1=1 but still got an error

in the debug output we see the generated query

    SELECT * from users where username="" OR 1=1" and password="1"

After this i relized i needed a way to ignore the remainder of the query, so, what if we make the next thing unaccesible by commenting it

i looked for comments in SQL and realized those were made using # 

so i tried

    " OR 1=1 #

Everything after # is treated as a comment so MySQL effectively executed:

    SELECT * from users where username="" OR 1=1

The query matched all rows in the users table

As we know the application only checked whether at least one row was returned...


As a result authentication succeeded and got the password



---

## Observations and Findings

- The warning generated after entering a quotation mark proved that user input could break the SQL statement

- Every character added by the application must be considered when crafting an injection

- The application never verified which user was returned, it only checked whether any row matched


---

## Key Concepts Learned

- SQL inection

- Comments syntax can be used to ignore unwanted portions of a query

- Database errors often reveal useful information about how an application processes input





