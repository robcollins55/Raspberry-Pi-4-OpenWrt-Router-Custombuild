# Why I made this Custom-build Raspberry Pi 4 OpenWrt Firmware

For years I have been looking for a home router solution which is decent hardware at a price that won't make you cry.  The majority of available home routers out there have such minimal hardware specs that they really aren't up to any task much more than just supplying internet to the house.   Too bad if you want to get decent NAS capability out of the device - in my experience that's usually a pretty frustratingly slow affair with your typical home router.  Then I spotted an interesting project website by a guy named Vladimír Záhradník.  Vladimir describes his Raspberry Pi 4 router project using OpenWrt as the software basis and his project site for that is [HERE](https://www.zahradnik.io/raspberry-pi-as-a-home-router "HERE").  So I bought a Raspberry Pi 4 and followed Vlad's guidance; it worked really well and I've now got a very capable Pi 4 OpenWrt router running at my house which is vastly superior to most typical home routers.  The Raspberry Pi 4 makes a perfect rock solid fast home router as (unlike previous versions of the Pi) it has a gigabit ethernet port and two full speed USB3 ports integrated into this Broadcom BCM2711 device; with a very capable quad core Cortex-A72 ARM v8 driving the whole set up.  For 40 bucks US I bought the 2GB RAM model (here in New Zealand) which I've found is plenty RAM for router purposes and is way more than your typical off-the-shelf home router.

**Problem.**  Following Vlad's guide is fairly straight-forward if you plan on doing it just the one time, setting and forgetting.  However, if you at some point later want to extend the capability of the router by installing or upgrading the occasional OpenWrt package you will likely run into software incompatibility trouble as the Pi 4 version of OpenWrt is still under active development so has not moved to "stable" yet and thus you need to start from scratch following Vlad's guide and using the latest snapshot build from OpenWrt.  While that is very doable, it is not very convenient and one might spend at least an hour or so rebuilding the router installation with the latest snapshot build.

**Solution.**  Make a custom build from the latest snapshot!  Being the nice guy that I am I will be creating an updated custom build on a weekly basis using the official OpenWrt image builder system, which uses all the official OpenWrt software for the build.  I will aim to create a build each week and upload it [HERE](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild/releases "HERE") around the middle of each week.

# Aims of this Customised Build
The *"[List of Packages](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild#list-of-packages-additional-to-official-snapshot-image-img "List of Packages")"* section at the end of this readme come preinstalled on my custom build and aim to do the following "out of the box":

1. Render the new router as immediately accessible via wifi network SSID *"Rivendell"* and password *Hobbiton* to enable easy further setting up for individual use cases.
2. Enable the Pi 4 gigabit ethernet port as "Lan" on *eth0* for networking the devices on the local network.  The intention being that this port is to be connected to your home switch/wifiAP device.
3. Enable "plug and play" functionality for a USB3 ethernet adapter via preinstalled drivers for a range of adapters with typical various chipsets.  The default setting for a plugged adapter has been configured as *eth1* so that one of the USB3 ports of the Pi 4 can be used to connect to the ethernet port of the ISP modem/gateway in your house to supply internet goodness to the Pi via this "Wan" connection.
4. Provide as preinstalled common router system enhancements including:
    1. USB storage plug and play functionality.  Plug in a USB drive and see Luci>system>mountpoints to see and edit how your drive gets connected.
    2. Router statistics module for monitoring (by viewable graphs) the Pi 4 CPU temperature and other device loads.  CPU Temperature graphing needs to be enabled by Luci>statistics>setup>generalplugins>Thermal.
    3. Bash instead of the default OpenWrt Ash for SSH terminal access to the router (Ash does not keep a history of previous commands accessed through up-arrow key whereas Bash does do that so a no-brainer swap-out here)
    4. SQM (aka Smart Queue Management) can be turned on in Luci>network>SQMQoS with desired preferences. Also see OpenWrt SQM docs [HERE](https://openwrt.org/docs/guide-user/network/traffic-shaping/sqm "HERE").
    5. Network-wide ad-blocking with Adblock enabled by default Luci>Adblock.  This module blanks out the majority of web advertising in networked browsers. This is very cool - I now get virtually no annoying advertising while browsing at home!
    6. [NAS](https://en.wikipedia.org/wiki/Network-attached_storage "NAS") network storage can be configured on the Pi router by plugging in a USB drive into a Pi 4 USB3 port.  Samba sharing Luci module is enabled to achieve NAS functionality Luci>services>networkshares.
    7. WireGuard and Dynamic DNS Luci modules are enabled by default so that remote access to the Pi router and connected USB drives can be achieved.

# How-to
## System Minimum Requirements
 *  Raspberry Pi 4 with min 2GB RAM
 *  Raspberry Pi 4 enclosure/case recommended
 *  USB C cable
 *  USB power supply
 *  4GB micro SD card (2x cards is useful as for upgrading purposes you can write an updated build to one card while the other is still running in the Pi)
 *  Ethernet cable x2 - one for lan and one for wan.
 *  USB3 ethernet adapter.  Make sure you select one that is USB3 (*not* USB2) and has OpenWrt drivers available for its chipset.  Packages preinstalled in my custom build include all available OpenWrt drivers for a range of chipsets; I use a Realtek device of rtl8152 chipset that uses the kmod-usb-net-rtl8152 driver. See *"[List of Packages](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild#list-of-packages-additional-to-official-snapshot-image-img "List of Packages")"* at bottom of this readme for available OpenWrt drivers preinstalled. 
 *  A working laptop/desktop with wifi and SD slot (if no SD slot, use a USB sd card reader instead)
 *  micro SD to SD adapter card so that the micro can be inserted into the laptop/desktop/reader

## Linux Steps
1. Download the latest release RCmod custom build from my repository [HERE](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild/releases "HERE") and unzip it so you have a file like openwrt-blah-blah.img
2. Insert the micro SD card into the card slot of the laptop or USB reader
3. At Linux command prompt run `lsblk` which will show the device name of the SD.  In my case it is /dev/mmcblk0 but if yours is different make sure to change it to what you've got when doing step 4.
4. Run dd image writer at command prompt, see below command line.  This will write a 3GB root partition on the SD which should be plenty for any usage case but you can up-size that to take up the whole card if you like using GParted or command line tools.
  ```console
  youruser@yourmachine:~# dd if=openwrt-bcm27xx-bcm2711-rpi-4-[*complete the file name here*].img of=/dev/mmcblk0 bs=4M; sync
  ```
5. Plug in your USB ethernet adapter to one of the the Pi's blue USB3 ports and connect your Pi 4 to your ISP's modem/gateway port.  Insert the micro SD card into the Pi 4 and power it up.
6. On your laptop/desktop go into network setting selection gui and connect to the new wifi network *"Rivendell"*.  Enter the password *Hobbiton*. 
7.  You should now be connected to the Pi 4.
8.  Carry out initial internet check, do the following at a Linux terminal prompt.
    1. SSH into the router by doing:
    ```console
    youruser@yourmachine:~# ssh root@192.168.1.1
    ```
    2. Set your router password , type *passwd* and hit enter, then enter the password you want to use twice.
    ```console
    root@OpenWrt:~# passwd
    Changing password for root
    New password:*****
    Retype password:*****
    passwd: password for root changed by root
    ```
    3. Ensure you have internet connection on the Pi, do `ping google.com` at the command prompt which should show very low % packet loss (if any) if internet is available.  If you don't get connection to google.com you should check out the connection settings for the Pi and change them to suit your ISP's modem requirements - do in terminal:
    ```console
    root@OpenWrt:~# nano /etc/config/network
    ```
       and edit the sections of that file *"config interface 'wan' "* and *"config device 'wan_eth1_dev' "*, save and exit nano then run command `reboot`, then SSH in again and recheck the google.com ping.  
    ```console
    root@OpenWrt:~# ping google.com
    PING google.com (216.58.199.78): 56 data bytes
    64 bytes from 216.58.199.78: seq=0 ttl=121 time=35.549 ms
    *(four lines of transmit omitted for brevity)*
    --- google.com ping statistics ---
    5 packets transmitted, 5 packets received, 0% packet loss
    round-trip min/avg/max = 35.162/35.999/38.799 ms
    ```
9.  Fire up a web browser and in the address bar enter 192.168.1.1 and hit enter; you may have to accept security warnings of the browser to continue.  You should now have access to  the OpenWrt Luci web interface of your new Pi 4 router.  If you get a browser "cannot connect to host" or similar message follow step 10.  If you successfully accessed Luci you can skip that step.
10.  Luci may need to be reinstalled to access it, which I have to do after every (rare) reboot of the Pi; do the following.
     1. SSH into the Pi and do at the command prompt
     ```console
     root@OpenWrt:~# opkg update; opkg --autoremove remove luci-ssl-nginx; opkg install luci-ssl-nginx
     ```
     2. Do not reboot, just leave the Pi running and recheck web browser access to Luci, it should be working now.
11.  Go to Luci>system>system>language&style and set the theme to OpenWrt2020 - it's much nicer than bootstrap :wink:
12.  If using a lan switch, connect a cable from the gigabit port on the Pi to the first lan port of your switch/wifiAP device.  Remember to switch DHCP and NAT off on the switch (as the Pi does all that) and set the gateway address on the switch to the Pi address - default for the Pi is 192.168.1.1.
13.  Turn the wifi on the Pi off if you intend to use your switch/wifiAP instead of Network>wireless>Rivendell.  If you intend to keep the wifi on and use the Pi as the wifiAP, please do alter the country code under advanced settings to your country - by default it is set to NZ New Zealand.  Also rename the SSID and password to your preference.
14.  Go to system>system>generalsettings>timezone and set your timezone to your location.
15.  Method for setting up a NAS service via Samba with WireGuard remote access I will cover in Extras.md, coming soon!

## Windows 10 steps
The general principles of *"[Linux Steps](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild#linux-steps "Linux Steps")"* above also apply to working with Windows and all steps in that section can be followed except items 3 and 4 for writing the image to the micro SD.  Please download [Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/ "Win32 Disk Imager") to do the image writing, and the free [Paragon Partition Manager](https://www.paragon-software.com/free/pm-express/# "Paragon Partition Manager") can be used to expand the rootfs partition to take up the whole micro SD card if that is wanted.

## Mac Steps
The *"[Linux Steps](https://github.com/robcollins55/Raspberry-Pi-4-OpenWrt-Router-Custombuild#linux-steps "Linux Steps")"* above can be followed when working with a Mac and all steps in that section can be followed except items 3 and 4 for writing the image to the micro SD.  Please follow these following points regarding steps 3 and 4 of the process.
 * For step 3 at the Mac terminal command prompt the command `diskutil list` is used to list the device name of the SD.  In my case it is /dev/mmcblk0 but if yours is different make sure to change it to what you've got when doing step 4.
 * Step 4 above for dd image writing to micro SD is basically the same in Mac as it is in Linux.  Hovever, in a quirk of the Mac nix system, small case unit should be used for the "bs=" flag which in my view is a "BS" system choice by Apple LOL.  See the script below I used.  I couldn't find any native app for resizing the ext4 rootfs partition to take up the full card if you're wanting to do that, but you can do this task by booting the excellent GParted live USB - [GParted Live USB](https://gparted.org/liveusb.php "GParted Live USB").
```console
iMac:[your-download-folder] youruser$ dd if=openwrt-bcm27xx-bcm2711-rpi-4-[*complete the file name here*].img of=/dev/mmcblk0 bs=4m; sync
```

# List of Packages (additional to official snapshot image .img)
This customised build is based upon the latest (at time of building) standard OpenWrt snapshot for the Raspberry Pi 4, as available from [HERE](https://downloads.openwrt.org/snapshots/targets/bcm27xx/bcm2711/ "HERE") on the official OpenWrt webpage.  The following is not a total list of the packages additional to the snapshot but instead the packages I named in the OpenWrt image builder environment using the “PACKAGES=” flag. All dependencies (not listed here) are automatically pulled in by the image builder during the automated build process.

**General Luci interface system:**
luci-ssl-nginx luci-theme-openwrt-2020 luci-app-statistics collectd-mod-thermal luci-app-sqm nano bash

**USB-ethernet adapter device drivers for a selection of popular chipsets (ie all drivers available on the openwrt repos):**
kmod-usb-net-asix kmod-usb-net-asix-ax88179 kmod-usb-net-cdc-ether kmod-usb-net-ipheth kmod-usb-net-kaweth kmod-usb-net-mcs7830 kmod-usb-net-pegasus kmod-usb-net-rtl8150 kmod-usb-net-rtl8152 kmod-usb-net-sr9700 

**WireGuard:**
luci-app-wireguard wireguard-tools wireguard qrencode

**Favourite extras (Samba, dynamic DNS, networkwide ad-blocking):**
luci-app-samba4 luci-app-ddns luci-app-adblock

**Network ip address discovery by manual terminal query:**
arp-scan arp-scan-database netdiscover

**USB storage auto mounting:**
kmod-fs-exfat kmod-fs-ext4 kmod-fs-ntfs kmod-fuse kmod-usb-storage kmod-usb2 kmod-usb2-pci kmod-usb3 kmod-fs-autofs4 block-mount blockd

# Further Tips
***(Don't pay attention to the following just yet, it is an incomplete fragment work in progress)***

Add samba user and password (luckybastard already set up as linux user in users file /etc/passwd) do....
smbpasswd -a luckybastard

To discover what connected devices are present on the network do one or the other:
arp-scan --localnet
netdiscover -i eth0
