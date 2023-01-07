---
title: "Rusty Hass Laptop"
date: 2022-01-26T14:05:11+01:00
draft: false
toc: false
repo: "https://github.com/Obito1903/rusty-hass-laptop"
description: "Un outil pour envoyer des stats sur la batterie et les ressources d'un ordinateurs portables à Home-assistant."
---

**RHL** ou **Rusty-Hass-Laptop** est un programme qui peut être exécuté en tant
que service utilisateur avec Systemd pour envoyer des statistiques d'utilisation
et de batterie de votre ordinateur portable à une instance Home-assistant.

Actuellement, seuls le niveau de la batterie, la fréquence du processeur, les cœurs
et la version du kernel sont envoyées à home-assistant.

{{< image src="sensors.png" alt="Sensor list" position="center" style="border-radius: 8px;" >}}

<br>

{{< image src="hass-dash.png" alt=" Entity dashboard" position="center" style="border-radius: 8px;" >}}
