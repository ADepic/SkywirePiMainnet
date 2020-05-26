# Alternative Methods to accessing your Raspberry PI.

In the guide, a method is described to give each Raspberry PI a **static IP address** , so that you can easily access each PI from your main computer.

## Monitor and keyboard

If you have a monitor and a keyboard, you can attach these to your raspberry PI. A raspberry PI uses a **HDMI cable** to connect to monitors. Connect your keyboard via its USB cable or adaptor.

Then you can boot the raspberry PI, and the terminal will show on screen (if you installed the lite version). Follow these commands:
```
sudo nano /etc/dhcpcd.conf
```
And write out the following lines:
```
interface eth0
static ip_address= <STATIC IP ADDRESS>/24
static routers= <GATEWAY IP ADRESS>
static domain_name_servers= <GATEWAY IP ADDRESS>
```

The main guide describes what static IP address you should choose, and how to find your **Gateway IP address**.

An example configuration would look like this:
```
interface eth0
static ip_address= 192.168.1.21/24
static routers= 192.168.1.254
static domain_name_servers= 192.168.1.254
```
**PLEASE refer to the main guide to find out what your gateway IP address is and what Static IP addresses you should choose.**

After entered the four lines, press `ctrl-s` to save and `ctrl-x` to exit.

## Finding the assigned IP address

[Youtube Video](https://youtube.c/watch?v=wvgeUmVXJlM)
