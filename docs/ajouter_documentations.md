# Ajout_documentations.md

### Sommaire
* Ecrire la documentation
* Pousser sur Github
* Déployer la documentation

### Requirements
* Clé publique sur votre compte github (éviter d'entrer le password à chaque commit)
* Pyhton
* pip
* Package mkdocs
```
pip install mkdocs
```

## Ecrire la documentation
* Il faut écrire la documentation sur la branche __master__, dans le dossier __docs__.

## Pousser sur Github
* Une fois la documentation écrite, il faut la pousser sur Github, __branche master__.
```
git pull
git add XXXX.md
git commit -m "ajout doc"
git push
```

* Attantion il existe 2 branches : __master__ et __gh-deploy__. Cette deuxième est utilisé par Github pages comme source pour le site web.

## Déployer la documentation sur le site.
* Sur la même branche __master__, executer la commande : 
```
mkdocs gh-deploy
```
* Fini.