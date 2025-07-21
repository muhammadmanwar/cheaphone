# Cheap Privacy-Respecting Linux "Phone" - v0.01 Build Plan

## Overview

The goal of this project is creating a low-cost, privacy-focused Linux "phone" using modular components and minimal setup. The project's future intent is to function out-of-the-box for basic phone use (calls and SMS) while also offering the full power of a Linux computer for advanced users. The aim is to maintain ethical sourcing where possible and prioritize accessibility and modularity.

---

## Bill of Materials

### Core Components

| Component              | Description (prices listed where relatively accurate prices could be determined)                                                                                                                   |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Raspberry Pi Zero 2 W  | SBC, computer that runs the whole thing. This could be replaced with a similar part from banana pi or orange pi. But the GPIO may be different. I bought the pi zero 2 w from microcenter, $15-$18 |
| Touch-screen           | I used a part from microcenter, Inland 3.5" GPIO Touchscreen HAT, \~$25                                                                                                                            |
| USB Battery Pack       | 5V output, 5000 mAh+, (I don't remember where I got mine, I just had it sitting around. A very basic power bank would do fine). Cost will depend on what is available near you                     |

### Additional Components

| Component                   | Description                                                                                                                                                                       |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Micro-USB to USB-A OTG      | Adapter for connecting USB peripherals, not needed for basic functionality, I bought this from microcenter                                                                        |
| USB Hub                     | For modem and audio dongle, not needed for basic functionality, I bought this from microcenter. Cost will depend on what is available near you                                    |
| USB LTE Modem (SIM support) | suggested devices to test: Quectel EC25 / Huawei E3372 / SIM7600 (untested)                                                                                                       |
| USB Audio Dongle            | 3.5mm mic/speaker interface, not needed for basic functionality, I bought this from microcenter (you're probably sensing a trend). Cost will depend on what is available near you |
| External Speaker            | earphones or speaker that connect through 3.5mm jack                                                                                                                              |
| Microphone                  | 3.5mm mic                                                                                                                                                                         |
| Case / Enclosure            | 3D printed (or other DIY)                                                                                                                                                         |

> Estimated Total Cost: ~$40–$120 depending on modem and audio config

---

##  Setup Instructions

### 1. Prepare SD Card

- Download Raspberry Pi OS Lite or similar lightweight Linux distro (I used Raspberry Pi OS Lite with Desktop and security updates)
- Flash OS to microSD card using pi imager (or other flasher)
- Insert into Pi Zero 2 W (or other SBC)

### 2. Boot and Configure Pi

- Connect Pi to mini HDMI to HDMI + USB keyboard (initial setup only) or ssh in if you enabled that already through the pi imager
- Enable I2C in interfaces (may also need to enable SPI depending on the display/configuration, this may require different terminal commands to set up depending on the OS):

  ```bash
  sudo raspi-config
  # Interface Options > Enable SPI and I2C
  ```
- Update system (may take a WHILE, the wifi on the pi zero 2 w is not very fast):

  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

### 3. Set Up Display

- Use`LCD-show` (I used this repo <https://github.com/goodtft/LCD-show>), follow the steps in the repo for the screen that matches your screen. if your screen isn't listed, please add your setup instructions to this step.
- Once the required drivers are installed, connect your screen and restart the device.

### 3.5. Set Up Matchbox Keyboard

- You'll likely also need an onscreen keyboard. These instructions go over how to set up matchbox keyboard: https://thepihut.com/blogs/raspberry-pi-tutorials/matchbox-keyboard-raspberry-pi-touchscreen-keyboard
- Can be installed with "apt install matchbox-keyboard"
- I found config in /usr/share/matchbox-keyboard, but depending on your OS and the user you install as, the config may end up somewhere else. please add that info here.
- I changed my default config to "keyboard-lq1.xml", and backed up my old one, using this command as root, from the config directory: "cp keyboard.xml keyboard-backup.xml; cp keyboard-lq1.xml keyboard.xml"

### 4. Install Audio Support

- Plug in USB audio dongle into the hub, as well as any adaptors required. the pi zero only has micro usb ports, and I haven't found a micro usb hub. Other SBCs may have ports that don't require adaptors, but I needed a micro USB to USB A, and a USB A to USB C, then used a USB C hub with USB ports, and plugged the usb audio card into that.
- You may need to install additional drivers depending on the linux distro used (untested, mine worked out of the box)
  - Install PulseAudio or ALSA:
    ```bash
    sudo apt install pulseaudio pavucontrol
    ```
  - Test input/output using `arecord` / `aplay` or `pavucontrol`

### 5. Install Modem Support (untested)

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

### Long term: Create Basic Phone UI

- Build custom interface (using python?) to display: 
  - Signal strength
  - Call/SMS buttons
  - Settings/Advanced Mode (fancy name for desktop mode)
- Determine additional default security measures to limit external tracking (Session messaging integration, linux hardening)
- Do we need to integrate this into an OS? Or some sort of apt package? maybe we could just start at an install script. 
