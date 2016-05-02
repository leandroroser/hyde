---
layout: 
title: Installing Ubuntu 16.04 in a bay-trail tablet
---

A month ago I have bought an Exo W1025  "2 in 1" tablet, that can be obtained here in Argentina. It has a processor Intel Atom Z3735F, 2Gb of RAM memory, 32 Gb of hard drive, and includes Windows 10.1 preinstalled. My objective was to install a GNU-Linux based OS, but it was not so easy. I have formatted the hard disk (because I am not a Windows user and there are only 32 Gb of HD available) and followed a couple of tutorials for other similar machines.  I have tested several distributions as Ubuntu 14.04, Ubuntu 15.10, Fedora, Linux Mint and even Android or Remix OS, but some were unstable and other had a poor performance and/or apoor driver support. The best solution comes with the new release of Ubuntu, and althought some characteristics are not working yet, it is still "usable". The following steps gave me the best result. At the moment there is no support for the screen in touch mode, the screen rotation and the sound; the power settings also have problems, because the machine do not charge when it is on and is not detecting the actual battery charge level.

## Requirements

* An USB storage medium  
* An Ubuntu 16.04 image  
*  A wired internet connection to accessing the web via USB. I plugged a cable into my cell phone and use it to have internet access.
* Patience  

## Installing Ubuntu
-1- Create a bootable image of Ubuntu 16.04 in Windows with Rufus (https://rufus.akeo.ie/) or with the  
Startup Disk Creator in Ubuntu.  
-2- As the machine has a 64-bits architecture but a 32-bits UEFI (!), a trick must be made. A copy of the 32-bit loader *bootia32.efi* can be obtained from the command line with:
 
```{bash}
 git clone https://github.com/leandroroser/leandroroser.github.io/blob/master/public/bootia32.efi
```

The file must be included in the folder /EFI/BOOT  
-3- Turn on the tablet and disable the "Secure Boot" option into the UEFI menu (without inserting the usb stick yet) and reboot.  
-4- Insert the usb stick and turn on the tablet. The Ubuntu menu will appear. Boot into the Live Sesion and install Ubuntu. I have partitioned the disk as follows: 100 mb for /boot (EFI), 20 Gb for root (Ext4), 1 Gb for swap and the rest for /home (Ext4). Reboot.  
  
  
## First boot
-5- The first time the machine is not able to boot directly into the installed system. Boot the machine with the USB storage 
medium plugged in and hit "c" when the menu appears, starting a grub shell.  
-6- Probably the keyboard will type some numbers instead of letters for the half of the keys. This is dissabled by pressing 
"Fn + Ins".  
-7- Type:  

```{bash}
 linux (hd1,
```
And press tab. A list of devices must appear in the screen. Take note of the UUID number of the root partition, (usually the hd1,gpt2 partition; you will recognize it for the Ext file system).  
-8- Now type:  

```{bash}
 linux (hd1,gpt2)/boot/vmlinuz-4.4.0-21-generic.efi.signed root=UUID=YOUR_NUMBER_OF_UUID reboot=pci,force
 initrd (hd1,gpt2)/boot/initrd-4.4.0-21-generic
 boot
```
-9- At this time, if you have writed correctly the code above (is usual to make a typo) the system must boot.  
  
  
## Installing the 32-bit Grub
-10- Connect the machine to an ethernet connection. I used my cell phone and works fine.  
-11- Update the repositories and install git  

```{bash}
 sudo apt-get update
 sudo apt-get install grub-efi-ia32-bin
 sudo update-grub
```

If you got problems to install the grub, you can download a version  [here](https://launchpad.net/ubuntu/xenial/amd64/grub-efi-ia32-bin/2.02~beta2-36ubuntu3)
Install the file via ```sudo dpkg -i PATH_TO_THE_FILE```, then type ```sudo update-grub```, and hit enter.
  
  
## Installing the wifi driver  

```{bash}
 sudo apt-get install git
 git clone https://github.com/hadess/rtl8723bs
 cd rtl8723bs
 sudo make
 sudo make install
 depmod -a
 modprobe r8723bs
```
  
## Upgrading the system and correcting the freezing problem
First upgrade the system  

```{bash}
 sudo apt-get upgrade
```

The system tends to freezes ocasionally. To correct this problem, a solution that work for my was changing a line
in the grub file. Type in the console:  

```{bash}
 sudo nano /etc/default/grub
```

And change the line:  

```
 GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

to  

```
 GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_idle.max_cstate=1"
```

Update the grub and reboot:  

```{bash}
 sudo update-grub
 sudo reboot
```
---------------------------





