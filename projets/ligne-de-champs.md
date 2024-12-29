---
title: Simulation de lignes de champs électriques en C
description: Simulation graphique des lignes de champs électriques générées par des charges positives et négatives.
layout: default
---

[Retour à l'accueil](/)

Ce programme simule les lignes de champs électriques en utilisant le langage C.  
Il calcule les champs électriques générés par des charges positives et négatives et trace leurs lignes de champ correspondantes. Cette simulation permet de visualiser le comportement du champ électrique dans des configurations complexes.

---

### Fonctionnalités

- Prise en charge de charges positives et négatives.
- Calcul dynamique des champs électriques résultants.
- Génération graphique des lignes de champs.

---

### Screenshots

#### Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs

![Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs](/assets/lignes-de-champs/image.png)

#### Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champs

![Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champs](/assets/lignes-de-champs/image-1.png)

#### Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs

![Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs](/assets/lignes-de-champs/image-2.png)

---

### Exécuter le programme

Pour lancer la simulation, suivez les étapes ci-dessous :

1. **Pré-requis** : Assurez-vous d'avoir un compilateur C (comme `gcc`) et `make` installés.
2. **Cloner le dépôt** :
    ```bash
    git clone https://github.com/ArianDervishaj/simulation-systeme-solaire.git
    ```
3. **Compiler et éxecuter** :

    ```bash
    cd simulation-lignes-de-champs
    make main
    ./main <nombre_de_charges_positives> <nombre_de_charges_négatives> <nombre_de_lignes_de_champ>
    ```

---

### En voir plus

[Voir le dépôt GitHub](https://github.com/ArianDervishaj/simulation-lignes-de-champs)