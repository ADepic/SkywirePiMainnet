# Introduction
This guide will show you how to get the **Skywire Mainnet** running on a set of raspberry PIs. Currently it is written from my experience of a GNU/linux computer, so all the instructions will be based on that. However, I may try this again with a Windows computer later and update the guide.

The main difference between using a GNU/linux or mac computer and a Windows one is that windows does not have SSH preinstalled, you will have to use PuTTY.

**If anything isn't clear please open an issue explaining the problem and I will try to make this documentation clearer.**

# Requirements
* A computer (this guide is aimed towards linux ones)
* A way that the computer can read and write to SD cards
  + This may be a SD card reader on your computer
  + You can instead buy USB sticks that read SD cards. They can then found for relatively cheap, for example [this one](https://www.amazon.co.uk/Integral-USB-Micro-Card-Reader/dp/B003ZSW458/ref=sr_1_4?dchild=1&keywords=SD%2BCard%2BReader&qid=1588069124&sr=8-4&th=1)
* A few raspberry PIs (preferably 8).
  + If you have only one PI then this guide is not suitable. Read the "number of visor images" section to see alternative options.

# Instructions

## Downloading Skyimager & Raspbian

You can download skyimager [from here](https://github.com/SkycoinProject/skybian/releases). Click on "assets" and download the "skyimager.tar.xz" file for your operating system. The "darwin-amd64" file is the one for macOS. Extract it using your default archive extracting program for your distrubution. (Note: extracting will be more complicated for Windows, and you will have to use 7zip. However, how to do this is not covered here).

Download raspbian [from here](https://downloads.raspberrypi.org/raspios_lite_armhf_latest). Extract the zip file to find raspbian, which will be a ".img" file.
## Setting up skywire images
![image showing skyimager interface](https://github.com/ADepic/SkywirePiMainnet/blob/master/images/skyimager.png)
After having downloaded **skyimager**, open it up (by double clicking on the executable). Press next to reach the **Prepare boot parameters** screen (as shown above.)

Before continuing you will have to find a few important pieces of information. 

### Work Directory
Skyimager will create a folder to store the images in your home directory. If instead, you want the skywire images to be created in a folder on your desktop, simply add "Desktop" after the username and before skyimager. Here is an example filepath:
```
/home/username/Desktop/skyimager
```
Where "username" is the username of your computer account.

### Base image (raspbian)
In order to create these skywire supporting images, skyimager needs a **base image**. In our case, this will be raspbian. First, under "Base Image" in skyimager click on **"From local filesystem"**. Find the raspbian ".img" file on your computer, and right click -> copy. Then *paste* this into the **"path to .img file"** textbox. 

### Gateway address

After this, you need to find your **Gateway address** . It is very important that this step is set up correctly, or you could waste **hours** flashing the images onto your raspberry PIs only to realise that the gateway address you entered is incorrect and nothing works.

To find your gateway address, open the terminal and enter this command (works on linux and also probably mac):
```
ip r | grep default
```
A number starting with "192.168" will show up. This represents your gateway IP:

![image showing usage of ip r command](https://github.com/ADepic/SkywirePiMainnet/blob/master/images/find_gateway_IP.png)

This is your gateway IP address. You will need this later, so **write it down somewhere** (or memorise it if you are better at that).

Paste the gateway IP address into skyimager.

### Number of Visor Images

If you have 8 raspberry PIs, the default option of 7 *visor images* and a *hypervisor image* is fine. However, if you have less that 8 PIs, then please read this section.

If you have less than 8 PIs, you are presented with a few options.

1. Run visors on all your boards, and run a hypervisor on an old computer that you can keep on at all times.
2. Run one raspberry PI with both a visor and hypervisor, and visor images on the rest.
3. Run one raspberry PI with only a hypervisor, and run visors on the rest of the raspberry PIs.

Option 1 and 2 are difficult but they will gain you more rewards. Option three is the easiest:

The first option requires you to set up a skywire hypervisor on a normal computer. This will not be covered here, but a guide can be found in [the skywire wiki](https://github.com/SkycoinProject/skywire-mainnet/wiki/Run-HypervisorUI-on-local-machine). Setting up visors this way will be complicated and this guide won't help, so you shouldn't refer to this guide at all if you chose this route.

The second option requires you to set up both a skywire hypervisor and visor simultaneously on one board. Again, this guide does not cover that, and you will have to refer to [the wiki](https://github.com/SkycoinProject/skywire-mainnet/wiki/Run-Skywire-Visor-on-manager-board). However, I think that you can set up visors on all the other boards as usual using this guide (though I am not certain of this yet).

The last option is the easiest one. Simply change the "number of visor images" to one less than number of raspberry PIs you have and you can continue. However, this way you will earn less rewards as you will have less visor images than the other options.

### Finalise Boot Paremeters

After filling in the screen, press next. You will be taken to a screen called "Finalize Boot Parameters". You may want to store the information shown here, although it isn't ready. 

After storing the information, or not, press build, and skyimager will create the images. They will be stored in a folder called "final" in the **Work Directory** that was defined earlier.

## Flashing images.

In order to flash images you should download [BalenaEtcher](https://www.balena.io/etcher/).

Simply insert your SD card. Select one of the image files for the "image" (make sure it is a different image for each raspberry pi) and flash. Once you have flashed, you will want to enable SSH, which is detailed below.

### Enabling SSH & Setting Up a Static IP Address

Once the image is flashed, your SD card will show up as a drive called **boot**. There might also be a drive called **rootfs**, also this does not always show up.

In the **boot** drive, create an *empty file* called **ssh** (in the "root" directory, i.e. don't go into any folder of the drive, just stay in the main one). Make sure this file has *no extension*.

#### Static IP

**This section is only possible if a *rootfs* drive showed up when you flashed the SD card. If it didn't, you will need to find [an alterative way of accessing all your raspberry PIs](AltStaticIpAddressMethods.md)**

Static IP is useful because it gives a local IP address to each one of your cards. This makes it easy to access via SSH (as we will need later).

To set up a Static IP, go to the **rootfs** drive and go into a folder called **etc**. In this folder you should find a file called **dhcpcd.conf**.

In the file paste these four lines:
```
interface eth0
static ip_address= <STATIC IP ADDRESS>/24
static routers= <GATEWAY IP ADRESS>
static domain_name_servers= <GATEWAY IP ADDRESS>
```
Remember the gateway IP address that you wrote down earlier? Well, you will need it now. Replace <GATEWAY IP ADDRESS> with your gateway IP address after pasting the 4 lines shown above.
  
After this, you will need to replace <STATIC IP ADDRESS> with a static IP address of your choice. The format of static IP addresses are `192.168.x.xx` where x is a number of your choice. Each PI needs to have a **different static IP address**. It also needs to not be the same as any static IP address that currently exists on the network. If you don't know what static IP address to chose, I recommend you give addresses in the same way I have:
  
I gave the first PI the address `192.168.1.21`, the second PI `192.168.1.22`, third PI `192.168.1.23` and so on and so forth. This way I know my first PI ends with 1, and 4rth PI ends with four, and this becomes easy for reference. I also flashed the images in this order, so that `image 0` is flashed to the PI with `192.168.1.21`, `image 1` to the PI with `192.168.1.22`. Since `image 0` is the **hypervisor** image, I know that the PI with the lowest number for it's static IP is the hypervisor.

You should write down your static IP address if you know you won't remember it. If you using a naming scheme similar to mine, then you can write that instead of writing down all 8 static IP addresses. Just make sure you have access to your static IP addresses, because you will need them later.

Make sure you have the `/24` after the IP address, this is necessary.
So my code would look like this:
```
interface eth0
static ip_address= 192.168.1.21/24
static routers= 192.168.1.254
static domain_name_servers= 192.168.1.254
```
If your computer denies you permission from making changes to those files (it did this to me on linux, I don't know if it will do this on mac or windows), then you need to edit it in the terminal. To do this, simply copy the file (dhcpcd.conf)(can't do this on windows) in your file browser. The run this command (and paste your file where it says <FILE>) 
```
sudo nano <FILE>
```
However, if pasting the file led to it adding `file://` on the start, the remove the `file://` text. (On linux, make sure there is one forward-slash at the start, however).
  
This command will open a text editor in the terminal. You can paste the above 4 lines using `ctrl-shift-v` and edit them as normal (using `ctrl-shift-v` to paste).

After the static IP is set up, safely remove the SD card and insert it into the the raspberry PI. Repeat all the steps in the "flashing" section for each one of your PIs.

## Setting up raspberry pi

After you have finished flashing all your PIs and turned them on, you will need to connect to each one and install skywire. 

### Connecting to the PI and first steps

To connect to your PI, enter the following command into your terminal:
```
ssh pi@<STATIC IP ADDRESS>
```
where <STATIC IP ADDRESS> is the static IP address of the PI you want to connect to. I would set up the PIs in the order you named them (e.g. set up `192.168.1.21` fisrt, then `192.168.1.22` next, etc), so that you do not forget which ones you set up and which ones you haven't. I would **set up the hypervisor first**, so if your naming scheme is different to mine in that the hypervisor does not have the lowest static IP address, then enter the address of your **hypervisor**. 
  
Once you enter the above command, it will ask you if you trust the device you are connecting to. Enter `yes`. After this, it will ask for a password. The default password for a raspberry PI is:
```
raspberry
```
So simply enter this password.

As soon as you have logged in, run this command:
```
sudo raspi-config
```
And select the `Change Password` option. Change the password to something different, as `raspberry` is not a secure password.

### Installing Skywire

NOTE: The commands listed below are taken from the ['Sky update' repository](https://github.com/Skyfleet/sky-update/blob/master/README.md). These commands may change, so if anything isn't working you may wan't to check the instructions in that repository. However, the instructions are less clear and assume you have some knowledge of Terminal Commands already, so it may be more confusing. There are also *divergent points* which could add to the confusion.    

After changing the password, enter this command:
```
sudo -i
```
All the following commands listed below **will not work** unless you enter the above command.

Next, enter this command to open a text editor:
```
nano /etc/apt/sources.list
```
And in the file, enter these two lines:
```
deb http://skyfleet.github.io/sky-update sid main
# deb-src http://skyfleet.github.io/sky-update sid main
```
Press `ctrl-s` to save and then `ctrl-x` to exit the text editor.

After that, simply copy and paste the following commands one by one and wait for each one to finish:

NOTE: For the following commands, if a dialogue saying "Continue?" comes up, type `y` and press `enter` to confirm.

```
curl -L http://skyfleet.github.io/sky-update/KEY.asc | apt-key add -
```

```
apt update
```
```
apt install network-manager
```
```
apt install skywire
```
### Finish Installation Steps

Run this command:
```
ls /etc/*.json
```

If the output is:
```
skywire-visor.json
```
You are running a **skywire visor board**. **Remember that you are currently on a visor board**. You can now skip this step and proceed to the **running skywire** step.

**HOWEVER, if this was the output:**
```
skywire-hypervisor.json
```
You are running a **skywire *hyper*visor board**.

**Enter these extra commands**
```
skywire-cli visor gen-config -ro /etc/skywire-visor.json
```
```
hvisorkey=$(cat /etc/skywire-hypervisor.json | grep "public_key" | awk '{print substr($2,2,66)}') && sed -i 's/"hypervisors".*/"hypervisors": [{"public_key": "'"${hvisorkey}"'"}],/' /etc/skywire-visor.json
```

**Remember that you are currently on a *hyper*visor board**. After that, you can continue.

### Running skywire
Run skywire **on a hypervisor** like this:
```
sudo mv /usr/bin/hypervisor /usr/bin/skywire-hypervisor
```
NOTE: If the above command gives an error, that's probably fine.
```
systemctl start skywire-hypervisor
systemctl start skywire-visor
```

Run skywire **on a visor** like this:
```
systemctl start skywire-visor
```

If you forgot whether you are on a *visor* or *hypervisor*, simply run this command and you will find out:
```
ls /etc/*.json
```
If you see both `skywire-hypervisor.json` and `skywire-visor.json` in the output, you are running a **hypervisor**. If you *only* see `skywire-visor.json`, you are running a visor.

If you are setting up a **hypervisor** (which is the first one you should be setting up as I recommended), in a web browser go to `<STATIC IP ADDRESS>:8000`. For example, in my case that would be `192.168.1.21:8000`. Click on `Configure initial launch` and choose a password for your hypervisor. After this, enter the password you just chose.

If you are instead setting up a **visor**, then after running it, see if it shows up in the list of visors in your **hypervisorUI** (as shown above). 

This will run it automatically without your intervention, and it will automatically start up skywire even if your reboot your raspberry PIs.

Once you have set up skywire on one PI, repeat the steps for all the other PIs.
