# Cheap Privacy-Respecting Linux "Phone" - v0.02 Build Plan

## Overview

The goal of this project is creating a low-cost, privacy-focused Linux "phone" using modular components and minimal setup. The project's future intent is to function out-of-the-box for basic phone use (calls and SMS) while also offering the full power of a Linux computer for advanced users. The aim is to maintain ethical sourcing where possible and prioritize accessibility and modularity.

---

## Bill of Materials

### Core Components

| Component              | Description (prices listed where relatively accurate prices could be determined)                                                                                                                   |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Raspberry Pi 5         | SBC, computer that runs the whole thing. This could be replaced with a similar part from banana pi or orange pi. But the GPIO may be different. I bought the 8gb pi 5 from microcenter, $70 |
| Touch-screen           | I used a part from pishop, https://www.pishop.us/product/5inch-capacitive-touch-display-for-raspberry-pi-dsi-interface-800-480/?searchid=0&search_query=+5+inch+dsi, \~$45                                                                                                                            |
| USB Battery Pack       | 5V/5A output, 10000 mAh+, Cost will depend on what is available near you. I got an INIU one from best buy for around 30 dollars                     |
| OR                     |                                                      |
| Custom Battery        | 5V/5A output, some type of charge controller will need to be included to handle charging the cells evenly. Cost will depend on what is available near you. I'm still figuring this part out                     |

### Additional Components

| Component                   | Description                                                                                                                                                                       |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| USB LTE Modem (SIM support) | I've purchased Gravity: CAT1 A7670G Global 4G IoT Communication Module, but it has not arrived yet, so this is untested                                                           |
| USB Audio Dongle            | 3.5mm mic/speaker interface, I bought this from microcenter (you're probably sensing a trend). Cost will depend on what is available near you |
| External Speaker            | earphones or speaker that connect through 3.5mm jack                                                                                                                              |
| Microphone                  | 3.5mm mic                                                                                                                                                                         |
| Case / Enclosure            | 3D printed (or other DIY)                                                                                                                                                         |

> Estimated Total Cost: ~$115+ depending on modem and audio config

---

##  Setup Instructions

### 1. Prepare SD Card

- Download Raspberry Pi OS Lite or similar lightweight Linux distro (I used Raspberry Pi OS Lite with Desktop and security updates)
- Flash OS to microSD card using pi imager (or other flasher)
- Insert into Pi 5 (or other SBC)

### 2. Boot and Configure Pi

- Connect Pi to mini/micro HDMI to HDMI + USB keyboard (initial setup only) or ssh in if you enabled that already through the pi imager
- Enable I2C in interfaces (may also need to enable SPI depending on the display/configuration, this may require different terminal commands to set up depending on the OS):

  ```bash
  sudo raspi-config
  # Interface Options > Enable SPI and I2C
  ```
- Update system:

  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

### 3. Set Up Display

- I used this link to set up my config for the display: https://www.waveshare.com/wiki/5inch_DSI_LCD
- I used the "vc4-kms-v3d-pi5" overlay, which worked for both display and touch functionality

### 3.5. Set Up Matchbox Keyboard

- You'll likely also need an onscreen keyboard. These instructions go over how to set up matchbox keyboard: https://thepihut.com/blogs/raspberry-pi-tutorials/matchbox-keyboard-raspberry-pi-touchscreen-keyboard
- Can be installed with "apt install matchbox-keyboard"
- I found config in /usr/share/matchbox-keyboard, but depending on your OS and the user you install as, the config may end up somewhere else. please add that info here.
- I changed my default config to "keyboard-lq1.xml", and backed up my old one, using this command as root, from the config directory: "cp keyboard.xml keyboard-backup.xml; cp keyboard-lq1.xml keyboard.xml"

### 4. Install Audio Support

- Plug in USB audio dongle into the hub, as well as any adaptors required. Other SBCs may have audio ports built in
- You may need to install additional drivers depending on the linux distro used (untested, mine worked out of the box)
  - Install PulseAudio or ALSA:
    ```bash
    sudo apt install pulseaudio pavucontrol
    ```
  - Test input/output using `arecord` / `aplay` or `pavucontrol`

### 5. Install Modem Support (untested)
I have not been able to test this yet, so any help here will be appreciated. The info below is untested and likely entirely incorrect

Some reference documentation here: <https://andino.systems/andino-4g-modem/ppp>

The below alternate instructions are unconfirmed
- Install ModemManager:

  ```bash
  sudo apt install modemmanager
  sudo systemctl enable ModemManager
  sudo systemctl start ModemManager
  ```
- Test modem detection:

  ```bash
  mmcli -L
  mmcli -m 0
  ```

---

### UI/UX
Use phosh UI with basic debian install? (having difficulties getting wayland to work on the pi 5, maybe a different SBC would work better
Determine additional default security measures to limit external tracking (Session messaging integration, linux hardening)

### Difficulties/Concerns
The Raspberry Pi 5 may not support proper suspend/sleep functionality, so this may be something that we need to work around (maybe some sort of button that connects to GPIO and toggles a signal that blanks the screen and throttles CPU speeds? not sure)
  - this may be something that needs to be solved by using a different SBC. Will need further testing to confirm

### Random Thoughts
Maybe the project should pivot to use a RISC-V cpu, in an effort to further empasize the open source vision?
