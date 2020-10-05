THIS IS A STARTER FILE (INCOMPLETE).  I WILL BE ADDING MY RASPBERRY PI OPENWRT FIRMWARE SOON AND UPDATING THIS FILE TO SUIT AND MAKING IT LOOK PRETTY :)

General Luci interface system:
luci-ssl-nginx luci-theme-openwrt-2020 luci-app-statistics collectd-mod-thermal luci-app-sqm bash

Install USB-ethernet device drivers for a selection of popular chipsets (ie all drivers available on the openwrt repos):
kmod-usb-net-asix kmod-usb-net-asix-ax88179 kmod-usb-net-cdc-ether kmod-usb-net-ipheth kmod-usb-net-kaweth kmod-usb-net-mcs7830 kmod-usb-net-pegasus kmod-usb-net-rtl8150 kmod-usb-net-rtl8152 kmod-usb-net-sr9700 

WireGuard:
luci-app-wireguard wireguard-tools wireguard qrencode

Favourite extras (Samba, dynamic DNS, networkwide ad-blocking):
luci-app-samba4 luci-app-ddns luci-app-adblock

For network ip address discovery by manual terminal query:
arp-scan netdiscover

For USB storage auto mounting:
kmod-fs-exfat kmod-fs-ext4 kmod-fs-ntfs kmod-fuse kmod-usb-storage kmod-usb2 kmod-usb2-pci kmod-usb3 kmod-fs-autofs4 block-mount blockd

Add samba user and password (luckybastard already set up as linux user in users file /etc/passwd) do....
smbpasswd -a luckybastard

If luci not accessible in the web-browser after a reboot do:
opkg update; opkg --autoremove remove luci-ssl-nginx; opkg install luci-ssl-nginx

To discover what connected devices are present on the network do one or the other:
arp-scan --localnet
netdiscover -i eth0
