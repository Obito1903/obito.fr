---
title: "Jeu-de-traverse"
date: 2020-04-10T13:51:40+01:00
description: "An implementation of a small board game in C"
repo: "https://github.com/Obito1903/Jeu-de-traverse"
---

During my course on Algorithms and Procedural Programming, I was asked to implement a game called "Jeu de Traverse" in which the goal is to move all your pieces to the other side of the board first.

## The rules

The game has 4 types of piece, with each their own move :

- The square : move 1 tile north, east, south or west
- The triangle : move 1 tile north-east, north-west or south
- The diamond : move 1 tile diagonally
- The circle : move 1 tile in whatever direction

They all evolve on a 10 by 10 board.

If after 30 turns, none of the players won, the game will end with a draw.

Here is an example of what the board look like.

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

## The features

For this project I was asked to implement the following features :

- 2 player mode
- Play against the computer
- Test boards to quickly validate every piece movement
- Save the board and load it back later

### AI

The AI for playing the game against the computer was implemented using the Min-Max algorithm.

At each turn, the computer will compute all the possible state that the board can be like for the next turn. For each of these states, it will assign a score and chose the move that will lead to the state with the best score.

## Examples

Start of the game

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