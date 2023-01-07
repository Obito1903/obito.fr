---
title: "Contrôler votre installation HI-FI avec Home-assistant et un IR-ESP01."
date: 2022-01-25T17:13:00+01:00
draft: false
toc: true
images:
tags:
  - home-assistant
  - esphome
  - esp01
---

{{< image src="hass-player.png" alt="Home-assistant media-player" position="center" style="border-radius: 8px;" >}}

J'ai récemment acheté un nouvel amplificateur pour mon installation HI-FI afin
de remplacer mon ancien qui devenait capricieux. J'ai choisi celui-ci parce que
j'ai fait une bonne affaire, mais il était également équipé d'une télécommande
IR pour le contrôler (pas très impressionnant, je sais). Et donc naturellement,
j'ai voulu l'automatiser et l'ajouter à mon réseau IoT contrôlé par
[Home-Assistant](https://www.home-assistant.io/), un serveur domotique open-source.

Pour ce projet, je vais utiliser une carte ESP01 avec le framework [ESPHome](https://esphome.io/).

## Obtenir les codes IR de votre télécommande

Avant de commencer, vous devez d'abord mettre la main sur les codes IR de votre
télécommande, pour pouvoir ensuite les cloner avec l'ESP01.
Vous avez plusieurs possibilités pour cela :

- Trouvez votre télécommande ou une télécommande compatible avec votre appareil
  sur les sites qui répertorient les codes IR des télécommandes, comme Remote Central.
- Enregistrez-les vous-même à l'aide d'un module récepteur IR (ex : VS1838B)
  connecté à votre ESP.

Personnellement, comme je n'avais pas de récepteur sous la main, j'ai opté pour
Remote Central. Sur le site, vous trouverez une liste des différentes fonctions
de la télécommande que vous avez choisie et le code `Pronto` qui leur est associé.
Le format du code peut être différent selon le site où vous l'avez trouvé,
mais dans la plupart des cas, vous pourrez les utiliser. Pour savoir si votre
protocole est compatible avec ESPHome, vérifiez [ici](https://esphome.io/components/remote_transmitter.html).

## Configuration de ESPHome

ESPHome est un outil permettant de connecter et de configurer facilement des
Arduino ou esp à Home Assistant. Dans ce guide, nous allons utiliser la version
CLI de cet outil dans un conteneur Docker.

Allez dans un dossier où vous voulez garder la configuration de votre ESP puis
initialisez le projet ESPHome :

```shell
docker run --rm -v "${PWD}":/config -it esphome/esphome wizard ir_remote.yaml
```

Cette commande vous demandera d'entrer des informations sur la carte que
vous voulez flasher, la plate-forme, le nom de la carte,
informations sur le réseau auquel vous voulez qu'il se connecte, et enfin
le mot de passe de l'API ESPHome qui sera hébergé sur la carte. À la fin du
processus, un fichier YAML sera généré avec le nom que vous avez spécifié précédemment.
Il doit ressembler à quelque chose comme ça :

```yaml
esphome:
  name: ir_remote

esp8266:
  board: esp01

# Enable logging
logger:

# Enable Home Assistant API
api:
  password: "azertyuiop"

ota:
  password: "azertyuiop"

wifi:
  ssid: "hello"
  password: "azertyuiop"

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "IR-Remote Fallback Hotspot"
    password: "superstrongpassword"

captive_portal:
```

Ensuite, nous devons demander à ESPhome de créer un émetteur-récepteur infrarouge.
Pour ce faire, nous devons d'abord définir une entré `remote_transmitter` dans
notre fichier de configuration :

```yaml
remote_transmitter:
  pin: GPIO2
  carrier_duty_percent: 50%
```

Il suffit de changer l'entrée de la broche à la broche à laquelle votre led
infrarouge sera connecté.

Ensuite, nous devrons ajouter une entité `button` pour chaque bouton que vous
souhaitez émuler.Il suffit d'ajouter la section `button` au YAML, une entrée
`plateform: template` pour chacun de vos boutons :

```yaml
button:
  - platform: template
    id: amp_power_btn
    name: Amp Power Button
    on_press:
  - platform: template
    id: amp_vol_plus
    name: Amp Vol+
    on_press:
```

Ensuite, pour chacun des boutons, nous devons spécifier un code correspondant à
envoyer lorsqu'il est activé. Pour cela, nous devons ajouter une action pour
chaqu'un d'eux dans la section `on_press`.
Vous pouvez trouver une liste d'actions possibles [ici](https://esphome.io/components/remote_transmitter.html#remote-transmitter-actions).

```yaml
- platform: template
    id: amp_power_btn
    name: Amp Power Button
    on_press:
      - remote_transmitter.transmit_pronto:
          data: "Some pronto hex code"
```

Dans certains cas, l'appareil que vous voulez automatiser aura besoin que le
code soit répété plusieurs fois pour qu'il soit enregistré.
Pour cela, il suffit d'ajouter une section `repeat` sous notre action.
Dans mon cas, mon ampli nécessite que le code soit envoyé 2 fois :

```yaml
- remote_transmitter.transmit_pronto:
    data: "Some pronto hex code"
    repeat:
        times: 2
```

Une fois que vous avez fini d'ajouter tous vos boutons, nous pouvons passer à
l'envoie de la configuration vers votre ESP. Pour ce faire, il suffit de le
brancher à votre ordinateur et de lancer la compilation et l'envoie avec ESPhome :

```shell
docker run --rm -v "${PWD}":/config --device=/dev/ttyUSB0 -it esphome/esphome run ir_remote.yaml
```

Où `/dev/ttyUSB0` est le port usb auquel votre ESP est connecté.

## Câblage de l'émetteur IR

La prochaine étape est de construire notre circuit pour piloter la led avec l'ESP.

Vous aurez besoin de :

- Une alimentation 3.3v
- 1x LED infrarouge
- 1x resistance de 1k Ohm
- 1x Transistor PNP (P2N2222A dans mon cas)

**Schéma du circuit :**

{{< image src="circuit-diagram.png" alt="Circuit diagram" position="center" style="border-radius: 8px;" >}}

<br/><br/>

{{< image src="circuit-drawing.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}

Une fois que tout est câblé, vous pouvez tout mettre sous tension. Pour vérifier
que tout fonctionne correctement, vous pouvez retourner sur votre ordinateur
et lancer le tableau de bord d'ESPhome :

```shell
docker run --rm --net=host -v "${PWD}":/config -it esphome/esphome
```

{{< image src="esp-home-dashboard.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}

Si tout va bien, vous devriez voir votre ESP apparaître en ligne d'ici une
minute ou deux.

## Ajout de ESPhome à Home-assistant

Enfin, pour ajouter votre télécommande au Home-assistant, vous avez deux options :

- Attendre que Home-assistant trouve votre ESP.
- L'ajouter manuellement si vous connaissez son adresse IP.

Si vous choisissez de l'ajouter manuellement, c'est assez simple :
il suffit d'aller dans le menu Integration dans les paramètres de Hass et d'ajouter
l'intégration ESPhome. Il vous sera demandé d'entrer l'IP de l'ESP.
Une fois cela fait, il vérifiera s'il peut l'atteindre et si oui, c'est terminé !

{{< image src="hass-entity.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}
