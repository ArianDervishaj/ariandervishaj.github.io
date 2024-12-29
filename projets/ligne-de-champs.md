---
title: Simulation de lignes de champs électriques en C
description:
layout: default
---
[Back](/)

Ce programme simule les lignes de champs électriques en utilisant le langage C. Il prend en compte les charges électriques et calcule les champs électriques résultants, puis trace les lignes de champs correspondantes. La simulation permet de visualiser le comportement du champ électrique en présence de multiples particules chargées.

### Screenshots

#### Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs

![Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs](/assets/lignes-de-champs/image.png)

#### Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champs

![Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champst](/assets/lignes-de-champs/image-1.png)

#### Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs

![Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs](/assets/lignes-de-champs/image-2.png)
### Exécuter le programme

1. Avoir un environnement de développement C configuré.
2. Cloner le répertoire.

```bash
git clone https://github.com/ArianDervishaj/simulation-systeme-solaire.git
```
3. Compiler et lancer le projet.

```bash
cd simulation-lignes-de-champs
make main
./main $Nombre_de_charge_positives $Nombre_de_charge_négatives $Nombre_de_ligne_de_champs
```
### En voir plus

[Cliquer ici pour voir la répertoire sur github](https://github.com/ArianDervishaj/simulation-lignes-de-champs)