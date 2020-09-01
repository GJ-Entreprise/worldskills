# Port mirroring :

L'objectif du port mirroring est de rediriger toutes les trames sur un port spécifique.
On distingue deux type de port mirroring :

* SPAN (le port mirroir ce toruve sur le même switch),
* RSPAN (le port de mirroir ce trouve sur un switch distant),

Lors de l'utilisation d'un NSM obtenir tous les échanges réseaux d'un SI est gain considérable d'information pour la détection d'intrusion.

Ressource :

* [https://blog.ine.com/utilizing-span-and-rspan](https://blog.ine.com/utilizing-span-and-rspan)

---

## 1 SPAN :
Le trafic d'un ou des ports est redirigé sur un port du switch.

---

### 1.1 Le laboratoire :
![img](../images/SO/Port-mirroring/lab-1.png)
<div align="center">
	<i><b>Illustration 1 :</b> Laboratoire 1</i>
</div>

Script de configuration initial de SW-1 :
````text
Switch# configuration terminal
Switch(config)# hostname SW-1
SW-1(config)# do wr mem
````

---

### 1.2 Mise en oeuvre :

---

### 1.3 Recopier les protocoles couche 2 :

---

## 2 RSPAN :
Le trafic d'un ou des ports est redirigé sur une lisason trun d'un autre switch. Le port de l'autre switch récupère tous le traffic pour le redirigé vers le port de mirroring.

Script de configuration initial de SW-1 :
````text
Switch# configuration terminal
Switch(config)# hostname SW-1
SW-1(config)# do wr mem
````

Script de configuration initial de SW-2 :
````text
Switch# configuration terminal
Switch(config)# hostname SW-2
SW-1(config)# do wr mem
````

---

### 2.1 Le laboratoire :

---

### 2.2 Mise en oeuvre :

---

### 2.3 Recopier les protocoles couche 2 :

---