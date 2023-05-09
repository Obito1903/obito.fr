---
title: "Jeu-de-traverse"
date: 2020-04-10T13:51:40+01:00
description: "Implémentation d'un petit jeu de société en C"
repo: "https://github.com/Obito1903/Jeu-de-traverse"
---

Lors de mon cours d'Algorithmes et de Programmation Procédurale, on m'a demandé de recréer un jeu appelé "Jeu de Traverse" dans lequel le but est de déplacer toutes ses pièces de l'autre côté du plateau.

## Les règles

Le jeu comporte 4 types de pièces, chacune avec ses propres mouvements :

- Le carré : déplacer 1 tuile vers le nord, l'est, le sud ou l'ouest
- Le triangle : déplacer 1 tuile vers le nord-est, le nord-ouest ou le sud
- Le diamant : déplacer 1 tuile en diagonale
- Le cercle : déplacez 1 tuile dans n'importe quelle direction

Elles évoluent toutes sur un plateau de 10 par 10.

Si après 30 tours, aucun des joueurs n'a gagné, la partie se terminera par un match nul.

Exemple de ce à quoi ressemble le tableau.

```shell
╔═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╗
║0,0║ C ║ A ║ L ║ O ║ O ║ L ║ A ║ C ║9,0║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,1║1,1║2,1║3,1║4,1║5,1║6,1║7,1║8,1║9,1║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,2║1,2║2,2║3,2║4,2║5,2║6,2║7,2║8,2║9,2║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,3║1,3║2,3║3,3║4,3║5,3║6,3║7,3║8,3║9,3║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,4║1,4║2,4║3,4║4,4║5,4║6,4║7,4║8,4║9,4║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,5║1,5║2,5║3,5║4,5║5,5║6,5║7,5║8,5║9,5║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,6║1,6║2,6║3,6║4,6║5,6║6,6║7,6║8,6║9,6║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,7║1,7║2,7║3,7║4,7║5,7║6,7║7,7║8,7║9,7║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,8║1,8║2,8║3,8║4,8║5,8║6,8║7,8║8,8║9,8║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,9║ C ║ A ║ L ║ O ║ O ║ L ║ A ║ C ║9,9║
╚═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╝
```

## Fonctionalités

Pour ce projet, il m'a été demandé d'implémenter les fonctionnalités suivantes :

- Mode 2 joueurs
- Jouez contre l'ordinateur
- Des plateaux de test pour valider rapidement chaque mouvement de pièce
- Enregistrez la partie et la recharger plus tard
- 
### IA

L'IA pour jouer au jeu contre l'ordinateur a été implémentée à l'aide de l'algorithme Min-Max.

A chaque tour, l'ordinateur calcule tous les états possibles que peut avoir le plateau pour le tour suivant. Pour chacun de ces états, il attribuera un score et choisi le coup qui conduira à l'état avec le meilleur score.

## Examples

Début du jeu

```shell
./jeu
Bienvenue sur le jeu de traverse. Quel mode lancer ?
1 - Mode normal (2 Joueur)
2 - Jeu contre ordi
3 - Mode Test
4 - Charge sav
```

Gameplay

```shell
╔═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╦═══╗
║0,0║ C ║ A ║ L ║ O ║ O ║ L ║ A ║ C ║9,0║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,1║1,1║2,1║3,1║4,1║5,1║6,1║7,1║8,1║9,1║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,2║1,2║2,2║3,2║4,2║5,2║6,2║7,2║8,2║9,2║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,3║1,3║2,3║3,3║4,3║5,3║6,3║7,3║8,3║9,3║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,4║1,4║2,4║3,4║4,4║5,4║6,4║7,4║8,4║9,4║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,5║1,5║2,5║3,5║4,5║5,5║6,5║7,5║8,5║9,5║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,6║1,6║2,6║3,6║4,6║5,6║6,6║7,6║8,6║9,6║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,7║1,7║2,7║3,7║4,7║5,7║6,7║7,7║8,7║9,7║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,8║1,8║2,8║3,8║4,8║5,8║6,8║7,8║8,8║9,8║
╠═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╬═══╣
║0,9║ C ║ A ║ L ║ O ║ O ║ L ║ A ║ C ║9,9║
╚═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╩═══╝
Tour n°0
Joueur 0 :
�
║Pion C(1,9)
Quel direction ?
5-SUD
7-OUEST
3-EST
1-NORD
5
 /!\Ce pion ne peut pas ce deplacer de cette maniere./!\ 
```