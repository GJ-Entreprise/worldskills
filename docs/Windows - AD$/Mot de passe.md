# Mot de passe - robustesse et rotation  :

## 1 Recomandantions :
Il est possible de définir ces paramètres via des GPO depuis Windows server sur l'ensemble d'un domaine.
Pour définir la robustesse et la rotation des mots de passe, je me suis appuyé sur les données de l'ANSSI qui préconise :

* 12 caractères,
* composées de chiffre, de lettre, de majuscule, et de carctères spéciaux,
* Une durée de rotation de 90 Jours,

Ressources :

* [https://www.supinfo.com/articles/single/2744-gpo-strategie-mot-passe-avec-active-directory](https://www.supinfo.com/articles/single/2744-gpo-strategie-mot-passe-avec-active-directory)

## 2 Application :

Création de la GPO **SECURE-PASSWORD** par défaut à la racine du domaine.

Voici le chemin des paramètres de sécurités :
**Configuration Ordinateur** -> **Stratégies** -> **Paramètres Windows** -> **Paramètres de sécurité** -> **Statégie de compte** -> **Stratégie de mot de passe**.

![img](../images/Windows/Password/GPO.png)
<div align="center">
	<i><b>Illustration 1 :</b> Création d'une GPO.</i>
</div>

Les paramètres définis sont les suivants :

* 5 mot de passe conservé dans l'historique,
* 90 jours de conservation des mots de passe,
* Le mot de passe doit respecter les éxigences de complexité,
* Le mot de passe doit être composés de 12 carctères au minimum,