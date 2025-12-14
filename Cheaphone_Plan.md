# Cheap Privacy-Respecting Linux "Phone" - v0.04 Build Plan

## Overview

The goal of this project is creating a low-cost, privacy-focused Linux "phone" using modular components and minimal setup. The project's future intent is to function out-of-the-box for basic phone use (calls and SMS) while also offering the full power of a Linux computer for advanced users. The aim is to maintain ethical sourcing where possible and prioritize accessibility and modularity.
---
See the case-models folder for FreeCAD files, to build one yourself.

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
| USB LTE Modem (SIM support) | I've purchased Gravity: CAT1 A7670G Global 4G IoT Communication Module, and it works, albeit with a slow connection speed. This may be a viable alternative for higher speeds https://www.dfrobot.com/product-2801.html                                                           |
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

- Plug in USB audio dongle into the SBC. The pi 5 has no built in audio ports. Other SBCs may have audio ports built in. You can also use bluetooth audio devices.

### 5. Modem Support (Easy Way)
- So far I've only been able to text and use mobile data, I have not been able to make a standard phone call successfully yet.
- I discovered that the Phosh DE included some auto management of Network Modems. The easiest way to get the modem to work reliably** is to install the Phosh Desktop Environment by running `sudo apt install phosh-full`. Then reboot, and connect your modem by USB. You may need to set up the Access Point for your network provider. If you're still having issues, take a look at the next section.

**reliably when plugged into a 5 volts 5 amps power supply for the pi 5, when plugged into the battery the pi limits power to the usb plugs, which can result in the modem powering on and off and never actually connecting to mobile data. 
#### 5.5. Modem Support (The Hard Way)

- So far I've only been able to text and use mobile data, I have not been able to make a standard phone call successfully yet.
- Plug the modem into the SBC. Open a terminal and type `systemctl status ModemManager` to check if modem manager is running. 
- Run `nmtui` to add a mobile broadband connection. You may need to look up your mobile network provider's APN to complete this step. Connect to the newly configured network.
- At this point, there is a chance that the network is functional. Test by opening a trusted web page. If its still not working, proceed with the next steps.

- If the modem is still not connected, you'll want to stop the ModemManager service with `systemctl stop ModemManager`. 
- Then, you can run ModemManager in debug mode like `ModemManager --debug`. 
- You'll need to watch the logs in this terminal, so open a new terminal.

- In the new terminal, run `sudo mmcli --scan-devices`, which will start a scan. After a few moments, run `sudo mmcli --list-devices`. you should see your modem, identified by a number. Replace the number in the following commands with this number
- Check signal quality using `sudo mmcli -m 0 --command=+csq?;`. Check operator status `sudo mmcli -m 0 --command=+cops?;`
- Running a simple connect command may allow the modem to connect `sudo mmcli -m 0 --simple-connect='apn=<access point name>,ip-type=ipv4'`. You may need to look up your mobile network provider's APN to complete this step.
- At this point, there is a chance that the network is functional. Test by opening a trusted web page. If its still not working, proceed with the next steps.

- Run `sudo mmcli -m 0 --command=+creg?;` to get registration status. The output may be in the ModemManager debug terminal, rather than being output directly.
- If the result of creg has a 2 as the first value (plus some other info), setting creg=1 may allow the network to connect. Run `sudo mmcli -m 0 --command=+creg=1;` 
- At this point, there is a chance that the network is functional. Test by opening a trusted web page. If its still not working, proceed with the next steps.

- If the modem is still not connected, try `sudo mmcli -m 0 --command=+creset;` to reboot the modem. Wait a few moments, then run `sudo nmcli --list-devices`. If nothing shows up, `sudo nmcli --scan-devices`, then wait a few moments, and list again.
- The modem may be connected now, or it may require another connect command `sudo mmcli -m 0 --simple-connect='apn=<access point name>,ip-type=ipv4'`
- At this point, there is a chance that the network is functional. If its not, you may need to experiment with the AT commands for the modem you are using. Here is a link to the wiki for the modem linked above, which contains a link to a pdf with the AT commands for the modem. https://wiki.dfrobot.com/SKU_TEL0163_A7670G_CAT1_4G_Communication_Module#More%20Document%20Downloads
- Also the modem may not be properly powered through USB unless the pi is plugged into a 5 volts 5 amps power supply, when plugged into the battery the pi limits power to the usb plugs, which can result in the modem powering on and off and never actually connecting to mobile data. 

---

### UI/UX
- I switched to Phosh as a DE, I was able to install it in pi OS by running `sudo apt install phosh-full` in the terminal, then rebooting the device, and on the login screen, selecting phosh DE.

### Battery Life Concerns
The Raspberry Pi 5 may not support proper suspend/sleep functionality, so this may be something that we need to work around (maybe some sort of button that connects to GPIO and toggles a signal that blanks the screen and throttles CPU speeds? not sure)
  - this may be something that needs to be solved by using a different SBC. Will need further testing to confirm

### Future Plans
Maybe the project should pivot to use a RISC-V SBC, in an effort to further empasize the open source vision?
