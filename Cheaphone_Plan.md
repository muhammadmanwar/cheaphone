# Cheap Privacy-Respecting Linux "Phone" - v0.03 Build Plan

## Overview

The goal of this project is creating a low-cost, privacy-focused Linux "phone" using modular components and minimal setup. The project's future intent is to function out-of-the-box for basic phone use (calls and SMS) while also offering the full power of a Linux computer for advanced users. The aim is to maintain ethical sourcing where possible and prioritize accessibility and modularity.
See the case-models folder for FreeCAD files, to build one yourself.
---

## Bill of Materials 
Estimated Total Cost: ~$140+ depending on modem and audio config

### Core Components

| Component              | Description (prices listed where relatively accurate prices could be determined)                                                                                                                   |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Raspberry Pi 5         | SBC, computer that runs the whole thing. This could be replaced with a similar part from banana pi or orange pi. But the GPIO may be different. I bought the 8gb pi 5 from microcenter, $70 |
| Touch-screen           | I used a part from pishop, https://www.pishop.us/product/5inch-capacitive-touch-display-for-raspberry-pi-dsi-interface-800-480/?searchid=0&search_query=+5+inch+dsi, \~$45                                                                                                                            |
| USB Battery Pack       | I used a protected 21700 with a built in usb c port from vapcell, purchased here: https://liionwholesale.com/collections/batteries/products/protected-vapcell-p2160b-21700-10a-button-top-6000mah-usb-battery-genuine, $12 |

### Additional Components

| Component                   | Description                                                                                                                                                                       |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| USB LTE Modem (SIM support) | I've purchased Gravity: CAT1 A7670G Global 4G IoT Communication Module, but it has not arrived yet, so this is untested                                                           |
| USB Audio Dongle            | 3.5mm mic/speaker interface, I bought this from microcenter (you're probably sensing a trend). Cost will depend on what is available near you |
| External Speaker            | earphones or speaker that connect through 3.5mm jack                                                                                                                              |
| Microphone                  | 3.5mm mic                                                                                                                                                                         |
| Case / Enclosure            | 3D printed (or other DIY)                                                                                                                                                         |

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

### 4. Audio Support

- Plug in USB audio dongle into the SBC. The pi 5 has no built in audio ports. Other SBCs may have audio ports built in

### 5. Modem Support (untested)
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
- I switched to KDE Plasma as a frontend, but other frontends may work better. Phosh may be ideal for a phone-like device format, but I am having difficulties getting wayland UI's to run properly on the pi 5.  

### Battery Life Concerns
The Raspberry Pi 5 may not support proper suspend/sleep functionality, so this may be something that we need to work around (maybe some sort of button that connects to GPIO and toggles a signal that blanks the screen and throttles CPU speeds? not sure)
  - this may be something that needs to be solved by using a different SBC. Will need further testing to confirm

### Future Plans
Maybe the project should pivot to use a RISC-V SBC, in an effort to further empasize the open source vision?
