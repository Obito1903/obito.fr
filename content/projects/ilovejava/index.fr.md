---
title: "Ilovejava"
date: 2022-11-04T13:51:40+01:00
image: "main_page.png"
description: "Petit forum conçu à l'aide des outils Java JEE et Spring, axé sur le langage Java."
repo: "https://github.com/alecs297/ilovejava"
# link: "https://ilovejava.palmes.dev"
---

Dans le cadre de mon troisième semestre en informatique à CY-Tech,
mes camarades de classe et moi devions créer un site web en utilisant les concepts
 et les technologies étudiés pendant ce semestre.

Ce site Web devait être construit à l'aide du framework [Spring](https://spring.io/).
Nous avons également dû utiliser le modèle MVC (Model View Controller)
et DAO (Data Access Object) de manière étendue, afin de conserver une base de
code structurée et permettre une extension facile.

Nous avons donc décidé de construire un petit forum inspiré par des plateformes
telles que [stackexchange](https://stackexchange.com/) et [Reddit](https://www.reddit.com/).

Ce forum serait principalement axé sur le langage de programmation Java,
et d'autres sujets qui lui sont liés.

## Fonctionnalités

- Authentification des utilisateurs
- Prise en charge complète Markdown
- Syntax highlight
- fils de discutions and réponses
- Tags/catégorie pour les fils de discutions
- Ordre de tri différent pour la liste des fils de discussion
- Système de points
- Outils de modération (supprimer/verrouiller un fil de discution ou un message)

## Technologies utilisées

- [Spring MVC](https://spring.io/)
- [Spring Security](https://spring.io/)
- [Spring Boot](https://spring.io/)
- [Hibernate](https://hibernate.org/)
- [Prism.js](https://prismjs.com/)
- [MySQL](https://www.mysql.com/)

## Captures d'écran

{{< image src="main_page.png" alt="Main page" position="center" style="border-radius: 8px;" >}}

<br>

{{< image src="thread_list.png" alt="Threads list" position="center" style="border-radius: 8px;" >}}

<br>

{{< image src="thread.png" alt="Thread view" position="center" style="border-radius: 8px;" >}}
