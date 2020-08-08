# Apache / TLS / SSL

## SSL
* __Secure Socket Layer__ est un protocol utilisé pour sécurisé les traffics sur internet.
* Utilise une pair de clé privée/publique.
* Dernière version SSLV3
__Ce protocol est déprécié, il ne doit plus être utilisé !__

### SSL a été remplacé par TLS

## TLS
* __Transport Layer Security__ 
* Dernière version stable : 1.2
* La version 1.3 arrive

## Pour quelle utilité ?
* __SSL et TLS__ Ont pour but de sécurisé le traffic internet.
* Toutes les données qui transient lors d'une requête web basique passent en clair par tous les intermédiaires entre le client et le serveur (les switchs, routeurs, etc).
* Il faut donc chiffrer ces données => __SSL / TLS__

## TLS requirements
* Chiffrer les données échangées
* Authentifier au moins un interlocuteur (client/serveur) => assurer l'intégrité des données => Eviter les MITMs

## Eviter les MITM

### PKI
__Public Key Infrastructure__ : L'utilisation de clé publiques, certificats pour authentifier les intelrocuteurs.

### TLS
* Toujours utilisé la dernière version du protocol TLS, une ancienne version expose les données à des failles de sécurité.

## Apache HTTPD serveur
Nous allons voir les fondamentaux pour sécurisé un serveur web Apache.

