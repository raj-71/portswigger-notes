# Write-up: SQL injection UNION attack, retrieving multiple values in a single column
Lab-Link: <https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column>  
Difficulty: PRACTITIONER  
Python script: 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

To find the number of columns returned by the query, we can use the `UNION` method. We will start with `UNION SELECT NULL` and increase the number until we get an error. This will give us the number of columns returned by the query. Using this we doesn't get error at `UNION SELECT NULL, NULL`, which means the number of columns is 2.

The link is `/filter?category=Lifestyle'+UNION+SELECT+NULL,NULL--`

sqli payload is
```sql
' UNION SELECT NULL, NULL--
```

Now, we need to check if both columns can return string or not. We can use the following link to find the column.
`Lifestyle'+UNION+SELECT+NULL,'abc'--` and `Lifestyle'+UNION+SELECT+'abc',NULL--`. The first link gives error and the second link responds successfully. So, the second column returns string.

### Solution

Now, we need to find the credentials of the administrator by concatenating both column values in single column. In Oracle, we can use `||` to concatenate the values. `~` is used to seperate the username and password.
`/filter?category=Lifestyle'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`

This gives the username and password for `administrator` of the user. And we can login as administrator.

Hence, the lab is sovled.

![alt text](img/2.png)