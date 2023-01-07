---
title: "Free Real Estate"
date: 2021-01-27T13:51:40+01:00
draft: false
toc: false
repo: "https://github.com/Free-Real-Estate"
description: "Un environnement de développement complet pour les cours de développement web PHP à l'université de CY-Tech."
---

**FRE** ou **Free Real Estate**, est un ensemble d'utilitaires et de services
réunis afin de fournir un environnement de développement complet, pour les projets
web, à l'intérieur d'un navigateur web.

Il est destiné à être utilisé pour les cours et les projets de développement web
à l'université de CY-Tech.

**FRE** fournir un IDE Web isolé à chaque utilisateur en utilisant le projet [code-server](https://github.com/coder/code-server).

Les fichiers de chaque instance sont accessibles via un gestionnaire de fichiers
web appelé [filebrowser](https://github.com/hurlenko/filebrowser-docker)

Chaque instance est équipée d'un serveur web PHP et d'une base de données MySQL
afin d'héberger les projets.

L'image Docker contenant code-server, le serveur web PHP et la base de données
est essentiellement une version modifiée de l'image Docker pour le serveur de code.
[code-server](https://github.com/Free-Real-Estate/code-nginx-php-docker)

{{< image src="landing-page.png" alt="Langind page" position="center" style="border-radius: 8px;" >}}
