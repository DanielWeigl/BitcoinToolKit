 
BTK - BitcoinToolKit Live Linux
===============================

What is BKT?
------------
 Bitcoin Tool Kit (or **BKT**) is a Live Linux (bootable from USB Stick or CD-Rom) which proivides different Tools to manage your Bitcoins in an secured enviroment. It tries to only use stock-software and I provide the step-by-step guid, how to build it on your own, so you don't have to trust me, that I didn't backdoor it.
 
 
Provided Tools
--------------

#### Linux tools, not Bitcoin related

* Based on debian (NetInstall)
* Graphical Interface: LXDE 
* Browser: midori
    
#### Bitcoin tools

* Electrum Wallet (Lightweight Desktop Bitcoin-Client - http://electrum.org)
* Armory (Bitcoin-Client, able to sign offline transactions - )
* BitAddress.html (Tools for generating Paperwallets, working with Private/Public Keys - http://www.bitaddress.org)
* ZBar (Scan QR Codes with a WebCam - http://zbar.sourceforge.net/)
* SX (Commandline Tools for lowlevel Bitcoin Actions, experts-only - http://sx.dyne.org/)
  
 
 
Disclaimer
-----------

    I can't vouch for any software I recommend here to be secure and backdoor-free. 
    Further I can't guarantee for the proposed setup to be the most secure and/or 
    robust. I tried to be as cautious as possible.

**This is only a guideline to help you getting started - everything happens on your own risk.**
 
 
 
How to build it from scratch
------------------------------
 
### Used Conventions in this document
  
  `host#> command`  specifies a command which gets executed in the build-enviroment

  `btk#> command`   specifies a command which gets executed in the bitcoin-tool-kit enviroment (inside virtualbox)
  
  If there is a multiline-command, I only write the shell-token once, so you can easily copy-paste it
  
  
### Prerequisits

* A working Linux Distro (I use Ubuntu 13.10)
* Connection to the internet, while installing the different tools
* VirtualBox, wget, other tools
    
    `host#> sudo aptitude install virtualbox wget`
 
* An USB-Stick, a CD-Rom or any other bootable medium to install BKT to
* BKT should be able to run on any linux-compatible platform. Any PC, Notebook, Netbook, Raspberry Pie, ...
    
    
### Step-by-Step

 
 Here follows a step-by-step guide, how to build your own BKT-bootable Linux, so you can be sure to get an
 untempered install.
 
 If you want to take a shortcut, you can download prebuild images from here:
  PUT_URL_HERE...
  
 
  **Step 1)** Invent the universe (should be already done)

  **Step 2)**
    Create a working directory
      `host#> mkdir ~/bkt; cd ~/bkt`
    
  **Step 3)**
    Download Debian NetInst 
      http://cdimage.debian.org/debian-cd/7.2.0/i386/iso-cd/
  
      host#> wget http://cdimage.debian.org/debian-cd/7.2.0/i386/iso-cd/debian-7.2.0-i386-netinst.iso

  
  **Step 4)**
    Create a new Virtual Image in virtualbox
      
      host#> 
        VBoxManage createvm --name "bkt" --register
        VBoxManage modifyvm "bkt" --memory 1024 --acpi on --boot1 dvd
        VBoxManage modifyvm "bkt" --nic1 bridged --bridgeadapter1 eth0
        VBoxManage modifyvm "bkt" --ostype Debian
        
        VBoxManage createhd --filename ./bkt.vdi --size 10000
        VBoxManage storagectl "bkt" --name "IDE" --add ide
        VBoxManage storageattach "bkt" --storagectl "IDE" \
            --port 0 --device 0 --type hdd --medium ./bkt.vdi
        VBoxManage storageattach "bkt" --storagectl "IDE" \
            --port 1 --device 0 --type dvddrive --medium debian-7.2.0-i386-netinst.iso

        #Start the VM
        VBoxManage startvm bkt


  **Step 5)**
    Follow the Debian-Install (I used the Text-Based one) with defaults to install the Debian NetImg inside the new     VirtualBox Image.
   
    Some notes on settings:
      *) Language/KeyMap: As you like
      *) Hostname: bkt
      *) Domain: bkt
      *) Root-Password: As you like, but remember it (not really security relevant)
      *) User: bkt
      *) User password: As you like, but remember it (not really security relevant)
      *) Timezone: As you like
      *) Partition: <Guided, Use entire disk>, <All files in one partition>
      
      *) Wait for the install to finish

      *) Mirror: As you like
      *) At the "Choose Software to install" step, only select following points:
          "Laptop" and "Standard system utilities"
      

  **Step 6)**
   Reboot the new virtual machine and login as **root**
   
   Initialize the base system with LXDE and other tools
   
    bkt#>
        apt-get update
        apt-get install apt-get install lxde wget midori
        


wip...