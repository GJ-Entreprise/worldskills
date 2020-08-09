# Sécuriser - Les mots de passe sur Cisco :

Ressource :
 * https://cisco.goffinet.org/ccna/gestion-securisee-peripheriques/chiffrement-des-mots-de-passes-locaux-cisco-ios/

## 0 Le laboratoire :
Voici le laboratoire que j'utilise pour ce sujet :
![img](../images/Password-Cisco/network.png)

Il est possible de distinguer deux accès aux équipements Cisco :
 * out-band ; accès par la console (qui ne nécessite pas de configuration réseau ; ex câble console etc.),
 * in-band ; accès par un service réseau (nécessite une configuration IP ; ex SSH etc.),

## 1 Accès out-band :
Pour définir un mot de passe pour l'accès à la console de manière sécurisé j'utilise l'algorithme scrypt.

Par exemple avec le mot de passe **test** :
````text
SW-1(config)# enable algorithm-type scrypt secret test
````

Le mot de passe est définis en type 9 tandis que le sha-256 est définis en type 8. Donc le scrypt apporte un gain de sécurité.

Lorsque l'on regarde la configartion :
````text
SW-1# sh run
````

![img](../images/Password-Cisco/enable_password.png)

Nous pouvons constater que le mot de passe est de type 9 et chiffré.

## 2 Accès in-band :
Pour avoir un accès in-band il est necessaire d'avoir une configuration IP.

Par exemple avec le mot de passe **test** :
````text
SW-1(config)# login local
````