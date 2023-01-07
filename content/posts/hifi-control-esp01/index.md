---
title: "Control your HI-FI setup with Home-assistant and an IR-ESP01"
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

I recently bought a new integrated amplifier for my hi-fi setup to replace my old malfunctioning one. I mainly chose this one because I had a good deal on it, but it also came with a IR-remote to control it (not that impressive I know). And so naturally I wanted to automate it and add it with my IoT network which is controlled by [Home-Assistant](https://www.home-assistant.io/), an open-source home automation tool.

For this project I'll be using a ESP01 board with the [ESPHome](https://esphome.io/) framework

## Getting your remote IR-codes

Before beginning, you'll first need to get your hands on the remote IR-codes,
to then be able to clone them with the ESP01. You have multiple option for that :

- Find your remote or one compatible with your device on sites that list remote
 IR-codes like Remote Central
- Record them yourself with the help of an IR-receiver module (ex : VS1838B)
  connected to your ESP.

Personally, since I did not have a receiver on hand, I went with Remote Central.
On the website, you'll be presented with a list of the different functions for
the remote you choose and their associated `Pronto` code. The format of the code
might be different depending on the website where you found it, but in most cases,
 you'll be able to use them. To see if your protocol is compatible with ESPHome,
 check [here](https://esphome.io/components/remote_transmitter.html)

## Setting up ESPHome

ESPHome is a tool to easily connect, and configure devices like Arduino, and ESP
to Home Assistant. In this guide, we will use the CLI version of this tool in a
docker container.

Go to a folder where you want to put the configuration for ESP then initialize
the ESPHome project :

```shell
docker run --rm -v "${PWD}":/config -it esphome/esphome wizard ir_remote.yaml
```

This command will ask you to enter some information on the device you want to
flash, name of the device, platform, board name, information about the network
to which you want it to connect to, and finally the password of the ESPHome API
that will be hosted on the board. At the end of the process, a YAML file will
be generated with the name you specified earlier.
It should look something like that :

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

Next, we will need to tell ESPhome to create an IR transceiver. To do that, first,
 we'll need to define a `remote_transmitter` entry in our configuration file :

```yaml
remote_transmitter:
  pin: GPIO2
  carrier_duty_percent: 50%
```

Just change the pin entry to the pin to which youre IR-led will be connected.

Next, we'll have to add a `button` entity for each button that you want to emulate.
Just add the button section to the YAML, and add a template platform for each
of your buttons :

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

Then for each of the buttons, we need to specify a corresponding code to send when
it is activated. For that, we need to add an action for each button under the
`on_press` section. You can find a list of possible action [here](https://esphome.io/components/remote_transmitter.html#remote-transmitter-actions).

```yaml
- platform: template
    id: amp_power_btn
    name: Amp Power Button
    on_press:
      - remote_transmitter.transmit_pronto:
          data: "Some pronto hex code"
```

In some cases, the device you want to automate will need the code to be repeated
multiple times for it to be registered. To do that you can just add a `repeat`
section under our action. In my case, my amp requires the code to be sent 2 times :

```yaml
- remote_transmitter.transmit_pronto:
    data: "Some pronto hex code"
    repeat:
        times: 2
```

Once you're done adding all your buttons we can move on to uploading the config to your ESP. To do that just plug your device in your computer and start the compilation and upload process with ESPhome :

```shell
docker run --rm -v "${PWD}":/config --device=/dev/ttyUSB0 -it esphome/esphome run ir_remote.yaml
```

Where `/dev/ttyUSB0` is the usb port to which your ESP is connected.

## Wiring up the IR-transceiver

Next step is to build our circuit to drive the led with the ESP.

You will need :

- A 3.3v power supply
- 1x infrared LED
- 1x 1k Ohm resistor
- 1x PNP Transistor (P2N2222A in my case)

**Circuit diagram :**

{{< image src="circuit-diagram.png" alt="Circuit diagram" position="center" style="border-radius: 8px;" >}}

<br/><br/>

{{< image src="circuit-drawing.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}

Once everything is wired up you can power everything. To check if everything is working correctly you can go back to your pc and launch the ESPhome dashboard :

```shell
docker run --rm --net=host -v "${PWD}":/config -it esphome/esphome
```

{{< image src="esp-home-dashboard.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}

If everything is fine you should see your device appear online within a minute or two.

## Adding ESPhome to Home-assistant

Finally to add your remote to Home-assistant you have to option :

- Wait for Home-assistant to find your ESPhome device.
- Add it manually if you know its IP address.

If you choose to add it manually, it's pretty simple just go to the Integrations menu in your Hass settings and add the ESPhome integration. It will prompt you to enter the IP of the ESP. Once done it will check if it can reach it and if so you are done.

One final note on the button entity, If you want to create a button in your
Lovelace dashboard to control your remote you need to tell the UI button to call
the **Button** service on press and select the button entity to activate.

{{< image src="hass-entity.png" alt="Circuit drawing" position="center" style="border-radius: 8px;" >}}
