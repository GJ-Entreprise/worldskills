##### source : https://systemzone.net/user-management-in-linux-redhat-centos/

# Management d'utilisateurs et de groupes dans CentOS

## Types d'utilisateurs

Il existe 3 types d'utilisateurs : 

* __Super utilisateur__ : C'est le big boss, il a les pleins pouvoirs sur l'ensemble du système. Créer lors de l'installation du système.

* __Utilisateur système__ : Utilisateurs créer par le système pour le système. Ex : bin, games, ftp, name, mail, daemon, apache etc. C'est utilisateurs de peuvent pas se loger au système car par défaut leur "login shell" est sur  "nologin".

* __Utilisateur régulier__ : Créer par le super utilisateur, l'utilisateur régulier a des droits limités, accordés par le super, pour utiliser le système. L'utilisateur régulier peut être :  ftp user, a samba user or a mail user par exemple.

![](../images/management_utilisateur_&_groupes/list_users.png)

## Créer un utilisateur

L'ajout d'utilisateur se fait via la commande __useradd__.
```
Usage: useradd [options] LOGIN
       useradd -D
       useradd -D [options]

Options:
  -b, --base-dir BASE_DIR       base directory for the home directory of the
                                new account
  -c, --comment COMMENT         GECOS field of the new account
  -d, --home-dir HOME_DIR       home directory of the new account
  -D, --defaults                print or change default useradd configuration
  -e, --expiredate EXPIRE_DATE  expiration date of the new account
  -f, --inactive INACTIVE       password inactivity period of the new account
  -g, --gid GROUP               name or ID of the primary group of the new
                                account
  -G, --groups GROUPS           list of supplementary groups of the new
                                account
  -h, --help                    display this help message and exit
  -k, --skel SKEL_DIR           use this alternative skeleton directory
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -l, --no-log-init             do not add the user to the lastlog and
                                faillog databases
  -m, --create-home             create the user's home directory
  -M, --no-create-home          do not create the user's home directory
  -N, --no-user-group           do not create a group with the same name as
                                the user
  -o, --non-unique              allow to create users with duplicate
                                (non-unique) UID
  -p, --password PASSWORD       encrypted password of the new account
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
  -P, --prefix PREFIX_DIR       prefix directory where are located the /etc/* files
  -s, --shell SHELL             login shell of the new account
  -u, --uid UID                 user ID of the new account
  -U, --user-group              create a group with the same name as the user
  -Z, --selinux-user SEUSER     use a specific SEUSER for the SELinux user mapping
```

Lors ce qu'un utilisateur utilise cette commande, plusieurs actions sont réalisées :

* Ajoute une nouvelle entrée dans __/etc/passwd__ et __/etc/shadow__

* Ajoute une nouvelle entrée dans __/etc/group__ et __/etc/gshadow__

* Créer un repertoire __home__ pour le nouvelle utilisateur

* Ajoute les droits et permissions au repertoire __home__


### Utilisation
```
useradd aioli
```

Nous venons de créer l'utilisateur aioli, mais il faut lui configurer un mot de passe.

```
passwd aioli
```
```
# passwd aioli
Changement de mot de passe pour l'utilisateur aioli.
Nouveau mot de passe : 
```

## Que s'est-il passé !? 0_o

### 1. Nouvelle entrée dans /etc/passwd
Ce fichier est utilisé pour stocker les informations sur les utilisateurs. La commande __useradd__ ajoute une nouvelle ligne à ce fichier avec les informations entrées.

```
aioli:x:1001:1001::/home/aioli:/bin/bash
```

Chaque champs est séparé par des __":"__
```
aioli:x:1001:1001::/home/aioli:/bin/bash
1    :2: 3  : 4  :5:     6    :    7
```

| Champs N°        | Nom           | Valeure  | Description |
| ---------------- |:-------------:| :--------:| :-----------:|
| 1      | nom d'utilisateur | aioli | Compris entre 1 et 32 charactères, fournit lors de la création de l'utilisateur, il sert à  son authentification|
| 2      | mot de passe      |   x | Le charactère __X__ indique que le mot de passe est chiffré dans le fichier /etc/shadow. Si on remplace le __x__ par __*__ l'utilisateur ne pourra plus se connecter. Si on le retire il pourra se connecter sans mot de passe|
| 3      | ID utilisateur (UID)  |  1001 | User Identification Number, tout utilisateur en a un. 0 => ROOT; 1-99 => autres utilisateurs prédéfinis; 100-999 => Utilisateurs systèmes; 1000-XXXX => Utilisateurs réguliers|
| 4      | ID groupe (GID)  |  1001 | Groupe principale de l'utilisateur. Quand un utilisateur est créer, un groupe en son nom est généréet lui est affecté. Il peut avoir plusieurs groupes.|
| 5      | Commentaire (optionnel)  |   | Juste du commentaire |
| 6      | Repertoire __home__  |  /home/aioli | Chemin vers le repertoire __home__ de l'utilisateur. Si blanc, le repertoire racine __/__ est utilisé.|
| 7      | SHELL utilisateur  |  /bin/bash | Chemin vers le shell utilisé|


### 2. Nouvelle entrée dans /etc/shadow
Ce fichier sert à la gestion des mot de passes des utilisateurs. Le mot de passe chiffré vu précedement est stocké ici.

Juste après l'execution de la commande ```useradd aioli```, la ligne créer est comme tel : 
```
aioli:!!:18490:0:99999:7:::
```
L'utilisateur est en mode __Lock__ jusqu'à l'execution de la commande ```passwd aioli``` qui va initialiser son mot de passe.

Chaque champs est séparé par des __":"__
```
aioli:$6$stHFnKJOnqjUzHlH$K3QnPn0iam.3dlpe6BQpioMJBb4iMpapJzbX0.yYWoEVDCcESsj05hadC1QXgsuM4rnbITEYXyaq8/1Xh9naA0:18490:0:99999:7:::

1:2:3:4:5:6:7:8:9
```

| Champs N°        | Nom           | Valeur  | Description |
| ---------------- |:-------------:| :--------:| :-----------:|
| 1      | nom d'utilisateur | aioli | Compris entre 1 et 32 charactères, fournit lors de la création de l'utilisateur, il sert à  son authentification|
| 2      | mot de passe  | $6$stHFnKJOnqjUzHlH$K3QnPn0iam.3dlpe6BQpioMJBb4iMpapJzbX0.yYWoEVDCcESsj05hadC1QXgsuM4rnbITEYXyaq8/1Xh9naA0 | mot de passe chiffré SHA-512, avant initialisation avec la commande __passwd__ ce champ était à __!!__ => aucun mot de passe => utilisateur bloqué |
| 3      | age du mot de passe | 18490 | jours depuis le dernier changement de mot de passe, si valeure à 0, l'utilisateur doit changer son mot de passe lors de la prochaine connection |
| 4      | Minimum | 0 | Nombre de jour minimum entre deux changements de mot de passe |
| 5      | Maximum | 99999 | Nombre de jour maximum d'un mot de passe |
| 6      | Warn | 7 | Alerte l'utilisateur X jours avant l'expiration de son mot de passe |
| 7      | Innactif | | Nombre de jours avant désactivation du compte si mot de passe expiré. Seulement un super utilisateur peut ré-activer le compte|
| 8      | restriction urgence |  |  |
| 9      | future use | | pour des fonctionnalités futurs|

### 3. Nouvelle entrée dans /etc/group

A chaque fois qu'un nouvel utilisateur est créé via la commande __useradd__, un groupe privé à son nom l'est aussi.
L'entrée dans le fichier __/etc/group__ se présente comme suit :

```
aioli:x:1002:
  1  :2:  3 : 4
```

| Champs N°        | Nom           | Valeur  | Description |
| ---------------- |:-------------:| :--------:| :-----------:|
| 1      | Nom du groupe | aioli | nom du groupe privé de l'utilisateur|
| 2      | Mot de passe du groupe  | Le mot de passe peut être assigné via la commande __gpasswd__. Le __"x"__ indique que le mot de passe est stocké dans le fichier __/etc/gshadow__.|
| 3      | GID | 1002 | Identifiant du groupe |
| 4      | Membre(s) |  | Liste des membres du groupes (excepté l'utilisateur)|


### 4. Nouvelle entrée dans /etc/gshadow

```
systemzone:!::
      1   :2:3:4
```

| Champs N°        | Nom           | Valeur  | Description |
| ---------------- |:-------------:| :--------:| :-----------:|
| 1      | Nom du groupe | aioli | nom du groupe privé de l'utilisateur|
| 2      | Mot de passe du groupe  | Le mot de passe peut être assigné via la commande __gpasswd__. Le __"!"__ indique qu'aucun mot de passe n'est défini. Si ce fichier est supprimer, le mot passe chiffré est déplacé dans __/etc/group__|
| 3      | Administrateur |  | Liste des administrateurs du groupe, par default c'est vide => seul l'utilisateur avec le même nom est administrateur|
| 4      | Membre(s) |  | Liste des membres du groupes (excepté l'utilisateur)|


### 5. Création du repertoire HOME
Lors de la création d'un utilisateur, son espace personnel est créé dans le répertoire home comme suit : __/etc/home/aioli/__.
Ce chemin par défaut peut-être modifié en éditant le fichier __/etc/default/useradd__.

## /etc/login.defs & /etc/default/useradd

### /etc/login.defs
Ce fichier contient tout un tas d'informations par default sur les utilisateurs. Utiliser notament lors de la création d'utilisateurs.

| Nom parametre        | Valeur par defaut       | Description |
| ---------------- |:-------------:| :--------:|
| MAIL_DIR      | Nom du groupe | aioli | nom du groupe privé de l'utilisateur|
|MAIL_DIR	| /var/spool/mail	| This is the directory path where user’s mail will be stored.|
|PASS_MAX_DAYS	| 99999	| Maximum number of days for password validity.|
|PASS_MIN_DAYS	|0	|Minimum number of days allowed between password changes.|
|PASS_MIN_LEN	|5	|Minimum character length for a password.|
|PASS_WARN_AGE|	7	|How many days the password expiration message will be shown before expired.|
|UID_MIN	|1000	|Minimum number for the automatic user ID selection.|
|UID_MAX	|60000|	Maximum number for the automatic user ID selection.|
|SYS_UID_MIN|	201	|Minimum automatic UID for the system users.|
|SYS_UID_MAX	|999|	Maximum automatic UID for the system users.|
|GID_MIN	|1000	|It is the minimum numeric value for automatic group ID selection.|
|GID_MAX	|60000|	The maximum numeric value for automatic group ID selection.|
|SYS_GID_MIN	|201|	Minimum GID for the system accounts.|
|SYS_GID_MAX	|999|	Maximum GID for the system accounts.|
|CREATE_HOME	|yes|	This value tells useradd command whether it should create home directory for the user.|
|ENCRYPT_METHOD	|SHA512	|Password encryption method for any user.|
|USERGROUPS_ENAB	|yes|	This enables userdel to remove user groups if no members exist.|
|UMASK	|077|	Default umask that is permission mask for the new users. If not specified, the permission mask will be initialized to 022.|

### /etc/default/useradd
Ce fichier contient les valeurs par defaut utilisées lors de la création d'un nouvel utilisateur.

```
# useradd defaults fileGROUP=100

HOME=/home

INACTIVE=-1

EXPIRE=

SHELL=/bin/bash

SKEL=/etc/skel

CREATE_MAIL_SPOOL=yes
```

| Nom parametre        | Valeur par defaut       | Description |
| ---------------- |:-------------:| :--------:|
|GROUP	|100|	Maximum number of groups for which a user can be member of.|
|HOME	| /home |	Default directory path where home directory of any user will be created.|
|INACTIVE	|-1|	After account creation how many days the account remain inactive. Default value ‘-1’ indicates account is never inactive.|
|EXPIRE		|Account| expiration date and it should be YYYY-MM-DD format.|
|SHELL	|/bin/bash|	 Default login shell for new user.|
|SKEL	|/etc/skel|	The files that are kept in this directory will be copied to the user’s home directory at the time of user creation.|
|CREATE_MAIL_SPOOL	|yes|	This option ensures that every new user will have a mail directory followed by username in /var/mail directory where user’s mail will be stored.|

#### Répertoire SKEL
Skel pour skeleton (squelette), dans ce repertoire ce trouve l'état par défaut lors de la création du répertoire __HOME__.
C'est un dossier qui servira de modèle, un copier coller dans dans le nouveau repertoire.

* .bash_logout
* .bash_profile
* .bashrc



## Exemples

### Créer un utilisateur avec un repertoire home personalisé
```
useradd -d /var/ftp systemzone
```

### Créer un utilisateur sans repertoire home
```
useradd -M systemzone
```

### Créer un utilisateur avec un UID personnalisé
```
useradd -u 1050 systemzone
```

### Créer un utilisateur avec un UID & GID personnalisé
```
useradd -u 1050 -g 1050 systemzone
```

### Ajouter plusieurs utilisateurs à un groupe
```
useradd -G admins,developers systemzone
```

### Créer un utilisateur sans groupe
```
useradd -N systemzone
```

### Créer un utilisateur avec date d'expiration
```
useradd -e 2020-09-29 systemzone
```

* Possible de voir l'age d'un utilisateur  : 
```
chage -l systemzone
```
```
Last password change                                    : Sep 26, 2017

Password expires                                        : never

Password inactive                                       : never

Account expires                                         : Sep 29, 2017

Minimum number of days between password change          : 0

Maximum number of days between password change          : 99999

Number of days of warning before password expires       : 7
```









