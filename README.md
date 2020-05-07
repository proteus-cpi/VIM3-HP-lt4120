# VIM3-HP-lt4120 setup

Notes on setting up a lt4120 Cellular Modem (HP lt4120 Snapdragon X5 LTE) on Khadas VIM3

## Steps

1. VIM3 Image used   : VIM3_Ubuntu-server-bionic_Linux-4.9_arm64_SD-USB_V0.8.4-20200507.img
   Erased the eMMC (after booting into  Khadas rescue image in SD card. seems to be the only way to get it booted from SD Card reliably).
2. Installed modemmanager and libqmi
   sudo apt install modemmanager
   sudo apt install libqmi-glib5 libqmi-utils libqmi-proxy
3. installed udhcpc
   sudo apt install udhcpc
4. Created file   "/etc/udev/rules.d/99-hp-lt4120.rules" with content:
```
##################################################################
ACTION!="add|change", GOTO="mbim_to_qmi_rules_end"
SUBSYSTEM!="usb|drivers", GOTO="mbim_to_qmi_rules_end"

# force HP lt4120 to configuration #1
SUBSYSTEM=="usb", \
ATTR{idVendor}=="03f0", ATTR{idProduct}=="9d1d", \
ATTR{bConfigurationValue}="1"

# load qmi_wwan module
SUBSYSTEM=="usb", \
ATTR{idVendor}=="03f0", ATTR{idProduct}=="9d1d", \
RUN+="/sbin/modprobe -b qmi_wwan"

# add the new id in the qmi_wwan driver
SUBSYSTEM=="drivers", \
ENV{DEVPATH}=="/sys/bus/usb/drivers/qmi_wwan", \
ATTR{new_id}="03f0 9d1d"

# load qcserial module
SUBSYSTEM=="usb", \
ATTR{idVendor}=="03f0", ATTR{idProduct}=="9d1d", \
RUN+="/sbin/modprobe -b qcserial"

# add the new id in the qcserial driver
SUBSYSTEM=="drivers", \
ENV{DEVPATH}=="/sys/bus/usb-serial/drivers/qcserial", \
ATTR{new_id}="03f0 9d1d"
LABEL="mbim_to_qmi_rules_end"
##################################################################
```
See "https://bugs.launchpad.net/ubuntu/+source/network-manager/+bug/1574582/comments/9"

5. 
 
