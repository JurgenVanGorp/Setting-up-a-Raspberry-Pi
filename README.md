Guide creation date: 28-Oct-2021 
# Step 1: Setting up a Raspberry Pi
This is a simple explanation on how you can set up a Raspberry Pi.

## ASSUMPTIONS

* This instruction was created using a Raspberry Pi 3 B+ (further also called RPi). It is expected that you can use the documentation for the Raspberry versions 2 to 4 at time of writing.
* For performance reasons I recommend to install the Raspberry Pi OS Lite (i.e. without the graphical interface). This is: if you want to use the RPi for e.g. Home-Assistant, which has its own web interface.

## Basic installation

This guide starts from the native Raspbian OS. The Raspberry Pi project provides a more than excellent imager that you can use to burn the SDCard on your PC. You can [find the Raspberry Pi Imager here](https://www.raspberrypi.com/software/). 

Plug the SDCard into the RPi, attach a keyboard and monitor, and power up the RPi. The initial logon is *pi* and the initial password is *raspberry*.

Start with updating the RPi to the latest patch level:

```
sudo apt update -y
sudo apt upgrade -y
sudo apt --autoremove -y
```

Then start the RPi configuration screen.

```
sudo raspi-config
```

Configure the following settings (at will):
* In *System Options*
  * change hostname
  * Change the *pi* logon default password
* In *Interface Options*
  * Enable SSH  
  * Enable I2C
* In *Localization Options*
  * Change the time zone to your local time zone
  * Change the Keyboard to match your keyboard layout
* In "Advanced Options*
  * Expand File System to use the full SD Card
* You may want to Update the tool to the latest version

Select [Finish] and reboot to be on the safe side. If you expanded the File System, this will be needed anyway.

```
sudo reboot
```

### Set a Fixed IP Address

Verify if dhcpcd already works with the following command.

```
sudo service dhcpcd status
```

**ONLY** if it is not configured for automatic boot yet, you can set it to automatic boot.

```
sudo service dhcpcd start
sudo systemctl enable dhcpcd
```

Edit the configuration for the network.

```
sudo nano /etc/dhcpcd.conf
```

Move down in the file until you can find the **#interface eth0** section. Configure a fixed IP address by adding (or updating) the following lines. Change the numbers to your own network configuration, of course.

```python
# THIS IS ONLY AN EXAMPLE for the fixed NIC.
interface eth0
static ip_address=192.168.1.200/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 8.8.8.8

# THIS IS ONLY AN EXAMPLE if you have e.g. also a wireless adapter installed.
interface wlan0
static ip_address=192.168.1.201/24
#static routers=192.168.1.1
#static domain_name_servers=192.168.1.1 8.8.8.8
```

type Ctrl-S and Ctrl-X to save and exit.

### You can disable IPv6 if you don't want to use it

Edit the sysctl.

```
sudo nano /etc/sysctl.conf
```

Add the following lines.

```python
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.lo.disable_ipv6=1
net.ipv6.conf.eth0.disable_ipv6=1
```

type Ctrl-S and Ctrl-X to save and exit.

This completes step 1 of the installation. You may want to test the new configuration with a final reboot.

```
sudo reboot
```

--- End of File
