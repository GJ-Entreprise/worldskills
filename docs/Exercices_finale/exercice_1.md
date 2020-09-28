# EXERCICE 1 – HARDENING LINUX - REMOTE LOGGING
## Prérequis : Distribution CentOS
### Tuto : https://www.golinuxcloud.com/secure-remote-logging-rsyslog-tls-certificate/

Configurer un serveur CentOS (s1) pour qu’il envoie ses logs à distance sur un second serveur (s2), en sécurisant les communications au moyen d’un certificat. Les messages en provenance de s1 doivent être placés dans un dossier tel que /var/log/s1.log

### Mots clé pouvant être utiles : certtool, rsyslog, elinks, chronyd Délai de réalisation attendu finales nationales : 1h15
### Délai de réalisation attendu finales internationales : 45 mins

## Definition des mots clés
### certtool - GnuTLS certificate tool
##### https://electronproton.com/certtool-command-examples/

### rsyslog
##### https://www.linuxtricks.fr/wiki/rsyslog-centralisation-des-logs-sous-linux

### elinks - navigateur en ligne de commande

### chronyd - Serveur de temps NTP
##### https://www.dsfc.net/infrastructure/reseau/chronyd-le-nouveau-serveur-de-temps-ntp-sous-linux/

## Ressources
* Centos 8 client
* Centos 8 serveur

## Procedure
### Configuration du serveur Rsyslog

1) Installation de Chrony, le serveur NTP
```
sudo yum update && sudo yum install -y chrony
```

```
sudo systemctl status chronyd
sudo systemctl enable chronyd
```

2) 