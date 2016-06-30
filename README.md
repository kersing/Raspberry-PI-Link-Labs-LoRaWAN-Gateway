# Raspberry-PI-Link-Labs-LoRaWAN-Gateway

Reference setup for LoRaWAN Gateway based on a Raspberry Pi host and the Link Labs [Gateway Board](http://store.link-labs.com/products/lorawan-raspberry-pi-board).

## Hardware setup for Linklabs RPI Shield

[schematic](http://forum.thethingsnetwork.org/uploads/default/original/1X/dbdd7deb2b854bb7104019d79683f2d1ae9f1c51.pdf)

GPIO Mapping for linklabs Raspberry PI Shield

| Description | RPi pin | BCM GPIO | WiringPi | Mode
| :---: | :---: | :---: | :---: | :---:
| SX1301 Reset  | 29 | GPIO5  | 21 | output
| GPS Reset     | 31 | GPIO6  | 22 | output
| GPS PPS       | 7  | GPIO4  | 7  | input  
| SPI CLK       | 23 |        |    | 
| SPI MISO      | 21 |        |    | 
| SPI MOSI      | 19 |        |    | 
| SPI NSS       | 24 |        |    | 
| LED 1         | 13 | GPIO27 | 2  | output
| LED 2         | 22 | GPIO25 | 6  | output

Now you're ready to start the software setup.

## Software setup (Raspbian)

- Download [Raspbian Jessie Lite](https://www.raspberrypi.org/downloads/)
- Follow the [installation instruction](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) to create the SD card
- Connect an Ethernet cable to the RPi
- Plug the power supply of the RPi which will also power the concentrator board (**WARNING**: never power up without the antenna!)
- From a computer in the same LAN, `ssh` into the RPi using the default hostname:

        local $ ssh pi@raspberrypi.local

- Use `raspi-config` utility to: 1) disable graphical boot mode and 2) to **enable SPI** (`Advanced options -> SPI`):

        $ sudo raspi-config

- Reboot
- Configure locales and time zone:

        $ sudo dpkg-reconfigure locales
        $ sudo dpkg-reconfigure tzdata

- Remove desktop-related packages (if you installed debian jessie lite skip this step):

        $ sudo apt-get install deborphan
        $ sudo apt-get autoremove --purge libx11-.* lxde-.* raspberrypi-artwork xkb-data omxplayer penguinspuzzle sgml-base xml-core alsa-.* cifs-.* samba-.* fonts-.* desktop-* gnome-.*
        $ sudo apt-get autoremove --purge $(deborphan)
        $ sudo apt-get autoremove --purge
        $ sudo apt-get autoclean
        $ sudo apt-get update

- Create new user for linklabs and add it to sudoers

        $ sudo adduser linklabs 
        $ sudo adduser linklabs sudo

- Logout and login as `linklabs` and remove the default `pi` user

        $ sudo userdel -rf pi

- Clone the installer and start the installation

        $ git clone https://github.com/kersing/Raspberry-PI-Link-Labs-LoRaWAN-Gateway.git ~/linklabs
        $ cd ~/linklabs
        $ sudo ./install.sh

- Recommended, put your Raspberry PI in Read Only mode to protect sour SD Card. Follow Charles's [blog entry](https://hallard.me/raspberry-pi-read-only/) to do it.

# Led Management

On linklabs Raspberry Pi board, LED 1 and LED 2 will light on as soon as service is running, then 
- LED 1 will blink (light off for short time) if GPS satellites are acquired (even if GPS is disabled in config), in fact GPS PPS output pin is redirected to LED GPIO output pin by software.
- LED 2 will blink (light off for short time) on each packet received.

# Credits

These scripts are largely based on the awesome work by [Ruud Vlaming](https://github.com/devlaam) on the [Lorank8 installer](https://github.com/Ideetron/Lorank).
This repository has been forked from https://github.com/mirakonta/Raspberry-PI-Link-Labs-LoRaWAN-Gateway, the changes were made to use older gateway software
required because The Things Network does not yet support the new protocol used by the newest Lora-net/packet_forwarder.    
Then forked from https://github.com/kersing/Raspberry-PI-Link-Labs-LoRaWAN-Gateway, to add led support
