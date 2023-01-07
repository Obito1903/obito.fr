---
title: "Rusty Hass Laptop"
date: 2022-01-26T14:05:11+01:00
draft: false
toc: false
repo: "https://github.com/Obito1903/rusty-hass-laptop"
description: "A small tool used to send laptop energy and resource usage stats to Home-assistant."
---

**RHL** or **Rusty-Hass-Laptop** is a small program that can be run as a user service in systemd to send usage and energy statistics about your laptop to a Home-assistant instance.

Currently, only battery level, CPU Frequency, cores and kernel version are reported to home-assistant.

{{< image src="sensors.png" alt="Sensor list" position="center" style="border-radius: 8px;" >}}

<br>

{{< image src="hass-dash.png" alt=" Entity dashboard" position="center" style="border-radius: 8px;" >}}
