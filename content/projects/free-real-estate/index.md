---
title: "Free Real Estate"
date: 2021-01-27T13:51:40+01:00
draft: false
toc: false
# image: "Screenshot_20230104_135431.png"
repo: "https://github.com/Free-Real-Estate"
description: "A full development environment for PHP web development classes at CY-Tech university."
---

**FRE** or **Free Real Estate**, is a set of utilities and services put together in order to provide a fully featured development environment, for web projects inside a web browser.

It is meant to be used for web development classes and project at CY-Tech university.

**FRE** provide an isolated  Web IDE to each user by using the [code-server](https://github.com/coder/code-server) project.

Files in each instance are accessible through a web file manager called [filebrowser](https://github.com/hurlenko/filebrowser-docker)

Each instance is equipped with a PHP web-server and a MySQL database in order to host the projects.

The docker image containing the code-server, PHP web server and database is basically a modified version of the docker image for [code-server](https://github.com/Free-Real-Estate/code-nginx-php-docker)

{{< image src="landing-page.png" alt="Langind page" position="center" style="border-radius: 8px;" >}}
