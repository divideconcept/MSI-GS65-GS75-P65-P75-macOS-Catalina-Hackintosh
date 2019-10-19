# Simplified MSI GS65/GS75/P65/P75 macOS Catalina Hackintosh Guide

Tested on a MSI P65 9SE, should work on most MSI GS65/GS75/P65/P75.<br />
This simplified guide is easy to follow and keep your MSI laptop intact (no repartitioning or hardware change required).<br />
Adapted from: https://github.com/ErrorErrorError/msi-gs65-gs75-hackintosh

# Table of Contents
- [Requirements](#requirements)
- [Pre-Installation](#pre-installation)
- [Installation](#installation)
- [Post-Installation](#post-installation)
- [Limitations](#limitations)

# Requirements

* A 8GB USB Drive
* Access to a Mac

### Optional Requirements (to avoid internal hardware changes or repartitionning)
* A [Wifi USB adapter](https://www.amazon.com/TP-Link-wireless-network-Adapter-SoftAP/dp/B008IFXQFU/ref=sr_1_3?keywords=wifi+nano+usb&qid=1571413129&smid=ATVPDKIKX0DER&sr=8-3) (the internal Intel Wifi chipset is not supported)
* A [NVMe SSD](https://www.amazon.com/Crucial-500GB-NAND-NVMe-PCIe/dp/B07J2WBKXF/ref=sr_1_1?crid=1NLB5KFIRPZO7&keywords=crucial%20nvme%20500gb&qid=1570866763&sprefix=crucial%20nvme%2Caps%2C196&sr=8-1&fbclid=IwAR2BiHjkwpVISFMksuNxGdE5NMhXyoCwShJu-M1V8v0hakwt3Jqy2HkoRRE) and an [USB 3.1 Gen 2 NVMe Enclosure](https://www.amazon.com/SSK-Aluminum-Enclosure-Adapter-External/dp/B07MNFH1PX/ref=sr_1_6?keywords=nvme%20enclosure&qid=1570866684&sr=8-6&fbclid=IwAR0rOUbQ7B9KCyt5wrYExYdfmUJ7g3KhkvEm7AjfF6MP1wGsF2MA0Lya5IQ) (to avoid destroying the original Windows install or opening the laptop to add an extra SSD)

# Pre-Installation

### MSI Bios Menu Setup 

* Advanced
  *   Sata mode = AHCI ***(Only necessary if you're not using an external SSD to install macOS. Warning: if switched from RAID to AHCI, your Windows install and all data will be lost)***
  *   VT-d = Disabled
 
* Boot 
  *   Fastboot = disabled
  *   Boot mode = UEFI with CSM

* Security
  *   Secure boot = disabled

### USB Drive Setup

 ***For this step you will need a mac and an USB Drive.***

  * Download the macOS Catalina installer [from the App Store](https://itunes.apple.com/us/app/macos-catalina/id1466841314?ls=1&mt=12) (if your mac is compatible with Catalina) or using [macOS Catalina Patcher](http://dosdude1.com/catalina/) (if your mac is incompatible with Catalina) or you can type this in a terminal if your mac is already running Catalina:
     ``` 
     softwareupdate --fetch-full-installer --full-installer-version 10.15
     ```
      
  * Find what is your USB disk identifier by typing in a terminal:
     ``` 
     diskutil list 
     ``` 
     Typically the usb would be located at /dev/disk1 if you have no other physical or virtual drive mounted. ***Please make sure that you are selecting the correct USB Drive as you can format your whole drive if you select the wrong disk.***
        
  * Format the USB Drive (replace X with the USB disk identifier):
     ```
     diskutil partitionDisk /dev/diskX 1 GPT HFS+J "install_osx" R
     ```
        
  * Install Clover (you can find it in the Tools directory of this repo), make sure you select `Change install location `  and select `install_osx`, then click `Customise` and check `Clover for UEFI booting only`
   
  * Once Clover is installed, go to the EFI drive, then EFI folder, and delete the Clover folder. Replace it by the one provided in this repo.
  
  * Copy the Catalina installer to your USB drive by typing: <br>
   ```
   sudo "/Applications/Install macOS Catalina.app/Contents/Resources/createinstallmedia" --volume  /Volumes/install_osx --nointeraction
   ```
   * After that's done, eject the USB drive and you're ready to install macOS.

# Installation
  * Plug in the USB drive with the macOS installer to one of the USB ports of the MSI laptop (as well as the SSD to which you want to install macOS) and turn on the MSI laptop.
  * As soon as the MSI Logo shows up, press F11 and select the USB drive containing the macOS installer.
  * Once Clover loads, click on "Install macOS Catalina" and let it load.
  * After the installation loads, click on Disk Utility and format the SSD drive where you want to install macOS as APFS
  * Once the format is done, exit the Disk Utility and click "Install macOS Catalina" and follow the steps and it will restart after it's done verifying. 
  * Once it restarts go back to the Clover bootloader (F11) and click again "Install macOS Catalina". This time it will extract the files to the hard drive. Once the installation finishes, it will restart again.
  * Once the laptop restarts, go to the Clover bootloader (F11) and select "Boot macOS from YOUR PARTITION NAME". Your partition name will show depending on what you typed during Disk Utility.

# Post-Installation
As you may have noticed, the Clover bootloader loads from a USB drive, but it would be more convenient loading the Clover bootloader from the SSD where macOS is installed.

  * Eject your USB drive

  * Install Clover (you can find it in the Tools directory of this repo), make sure you select `Change install location `  and select the partition where you installed macOS, then click `Customise` and check `Clover for UEFI booting only`
  
  * Once Clover is installed, find the hidden EFI partition of the SSD drive where you installed macOS by typing:
  ``` 
  diskutil list 
  ``` 
  
  * Then mount it by typing:
  ``` 
  sudo diskutil mount /dev/diskXsY
  ``` 

  * Go to the EFI drive, then EFI folder, and delete the Clover folder. Replace it by the one provided in this repo.
  
  * Finally, set up your keyboard as a PC keyboard : go to System Preferences, Keyboard, Modifier Keys... and invert the Option and Command keys. Then go to the Input Sources tab, and find the "- PC" version of your keyboard.
  
You're done ! When your computer starts, as long as the SSD drive where you installed macOS is plugged, you should be able to select it by pressing F11 and boot macOS from there.

# Limitations

* NVIDIA GPUs are no longer supported by macOS. Only the embedded Intel GPU (UHD 630) will be used for hardware acceleration.
* The HDMI and Mini-DisplayPort outputs won't work as they are connected to the NVIDIA GPU. However you can connect an external display to the Thunderbolt port.
* Sometime during the macOS boot sequence a black screen may be shown for 3 minutes due to a backlight issue. See [this thread](https://www.tonymacx86.com/threads/bug-black-screen-3-minutes-after-booting-coffeelake-uhd-630.261131/).