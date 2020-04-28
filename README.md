## Note: this guide is incomplete
# Introduction
This guide will show you how to get the **Skywire Mainnet** running on a set of raspberry PIs. Currently it is written from my experience of a GNU/linux computer, so all the instructions will be based on that. However, I may try this again with a Windows computer later and update the guide.

The main difference between using a GNU/linux or mac computer and a Windows one is that windows does not have SSH preinstalled, you will have to use PuTTY.

If anything isn't clear please open an issue explaining the problem and I will try to make this documentation clearer.

# Requirements
* A computer (this guide is aimed towards linux ones)
* A way that the computer can read and write to SD cards
  + This may be a SD card reader on your computer
  + You can instead buy USB sticks that read SD cards. They can then found for relatively cheap, for example (this one)[https://www.amazon.co.uk/Integral-USB-Micro-Card-Reader/dp/B07PM829TM/ref=sr_1_4?dchild=1&keywords=SD%2BCard%2BReader&qid=1588069124&sr=8-4&th=1]
* A few raspberry PIs (preferably 8).
  + If you have only one PI then this guide is not suitable. Read the "number of visor images" section to see alternative options.

# Instructions

## Downloading Skyimager & Raspbian

You can download skyimager (from here)[https://github.com/SkycoinProject/skybian/releases]. Click on "assets" and download the "skyimager.tar.xz" file for your operating system. The "darwin-amd64" file is the one for macOS. Extract it using your default archive extracting program for your distrubution. (Note: extracting will be more complicated for Windows, and you will have to use 7zip. However, how to do this is not covered here).

Download raspbian [from here](https://downloads.raspberrypi.org/raspbian_lite_latest). Extract the zip file to find raspbian, which will be a ".img" file.
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

After this, you need to find your **Gateway address** . It is very important that this step is set up correclty, or you could waste time flashing the images onto your raspberry PIs only to realise that the gateway address you entered is incorrect and nothing works.

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

### Flashing images.

In order to flash images you should download [BalenaEtcher](https://www.balena.io/etcher/).

Incomplete guide. The rest will be finished later.
