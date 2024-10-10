# Write-up: SQL injection UNION attack, finding a column containing text
Lab-Link: <https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text>  
Difficulty: PRACTITIONER  
 

## Lab description

![alt text](<img/1.png>)

## Solution

To find the number of columns returned by the query, we can use the `UNION` method. We will start with `UNION SELECT NULL` and increase the number until we get an error. This will give us the number of columns returned by the query. Using this we doesn't get error at `UNION SELECT NULL, NULL, NULL`, which means the number of columns is 3.

The link is `/filter?category=Lifestyle'+UNION+SELECT+NULL,NULL,NULL--`

sqli payload is
```sql
' UNION SELECT NULL, NULL, NULL--
```

Now, we need to find which column returns string. We will replace the NULLs with given string and check which column returns the string. We can use the following link to find the column.
`Lifestyle'+UNION+SELECT+'yhFRqa',NULL,NULL--`, this gives error. So, the first column doesn't return string.
`Lifestyle'+UNION+SELECT+NULL,'yhFRqa',NULL--`, this responds successfully. So, the second column return string.

Hence, the lab is sovled.

![alt text](img/2.png)