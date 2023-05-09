---
title: "2048-C"
date: 2020-06-10T13:51:40+01:00
image: "logo.png"
description: "Une implementation en C du jeu 2048"
repo: "https://github.com/Obito1903/2048-EISTI-C"
---

Dans le cadre de mon cours sur les Algorithmes et la Programmation Procédurale, on m'a demandé de recréer le jeu 2048.

Le but du jeu 2048 est de faire glisser des tuiles numérotées sur une grille 4x4, en les combinant pour créer une tuile portant le numéro 2048. Les joueurs déplacent les tuiles en les faisant glisser vers le haut, le bas, la gauche ou la droite. La partie se termine lorsqu'il n'y a plus de coups à effectuer ou lorsque le joueur crée une tuile portant le numéro 2048.

Au début, on nous a seulement demandé de créer une version CLI avec les capacités de sauvegarder l'état du jeu dans un fichier et de le restaurer pour reprendre le jeu à une date ultérieure.

Après avoir terminé la logique du jeu, j'ai décidé d'essayer d'implémenter une interface graphique en utilisant la bibliothèque graphique SDL2, principalement parce que je voulais en savoir plus sur la programmation graphique en C.

{{< image src="gameplay.png" alt="Gameplay" position="center" style="border-radius: 8px;" >}}

<br/>

{{< image src="loose.png" alt="Ecran de fin du jeu" position="center" style="border-radius: 8px;" >}}

Changer la taille de la grille :

```shell
./2048 -t 8
```

{{< image src="grid8.png" alt="Grille de taille 8" position="center" style="border-radius: 8px;" >}}

Charger une partie sauvegardée :

```shell
./2048 -l Saves/sav.bin
```