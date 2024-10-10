# Write-up: Blind SQL injection with time delays
Lab-Link: <https://portswigger.net/web-security/sql-injection/blind/lab-time-delays>  
Difficulty: PRACTITIONER  
 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

In tracking cookie, we see that the cookie contains a `TrackingId` parameter, which is vulnerable to blind sql injection according to problem statement.


### Exploitation

To exploit the time based blind SQL injection, and causing a delay of 10 seconds, we can use the following payload:

```sql
BQ9LrRqs2m64aZJ4' || pg_sleep(10)--
```

Hence, the lab is sovled.

![alt text](img/2.png)