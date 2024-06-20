# Write-up: SQL injection UNION attack, determining the number of columns returned by the query
Lab-Link: <https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns>  
Difficulty: PRACTITIONER  
Python script: 

## Lab description

![alt text](<img/1.png>)

## Solution

To find the number of columns returned by the query, we have been using the `ORDER BY` method. But this time we need to use the `UNION` method. We will start with `UNION SELECT NULL` and increase the number until we get an error. This will give us the number of columns returned by the query. Using this we doesn't get error at `UNION SELECT NULL, NULL, NULL`, which means the number of columns is 3.

The link is `/filter?category=Lifestyle'+UNION+SELECT+NULL,NULL,NULL--`

sqli payload is
```sql
' UNION SELECT NULL, NULL, NULL--
```

Now, to list the database tables, we can use the `all_tables` table in oracle. The column `table_name` contains the table names. We can use the following link to list the tables.
`Lifestyle'+UNION+SELECT+table_name,'abc'+FROM+all_tables--`

Hence, the lab is sovled.

![alt text](img/2.png)