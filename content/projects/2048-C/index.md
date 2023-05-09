---
title: "2048-C"
date: 2020-06-10T13:51:40+01:00
image: "logo.png"
description: "A C implementation of the famous game 2048"
repo: "https://github.com/Obito1903/2048-EISTI-C"
---

As part of my course on Algorithms and Procedural Programming, I was asked to recreate the game 2048.

The goal of the game 2048 is to slide numbered tiles on a 4x4 grid, combining them to create a tile with the number 2048. Players move the tiles by sliding them in the up, down, left, or right direction. The game ends when there are no more moves to make or when the player creates a tile with the number 2048.

At first, we were only asked to make a CLI version with the capabilities to save the state of the game in a file and restore it for resuming the game at a later date.

After finishing the core of the game, I decided to try to implement a GUI using the SDL2 graphics library, mainly because I wanted to learn more about graphical programming in C.

{{< image src="gameplay.png" alt="Gameplay" position="center" style="border-radius: 8px;" >}}

<br/>

{{< image src="loose.png" alt="Gameover screen" position="center" style="border-radius: 8px;" >}}

Changing the size of the grid :

```shell
./2048 -t 8
```

{{< image src="grid8.png" alt="With a grid of size 8" position="center" style="border-radius: 8px;" >}}

Loading a saved game :

```shell
./2048 -l Saves/sav.bin
```