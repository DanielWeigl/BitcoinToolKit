 
BTK - BitcoinToolKit Live Linux
===============================

What is BTK?
------------
 Bitcoin Tool Kit (or **BTK**) is a Live Linux (bootable from USB Stick or CD-Rom) which proivides different Tools to manage your Bitcoins in an secured enviroment. It tries to only use stock-software and I provide the step-by-step guide, how to build it on your own, so you don't have to trust me, that I didn't backdoor it.
 
 
Provided Tools
--------------

#### Linux tools, not Bitcoin related

* Based on Debian (NetInstall)
* Graphical Interface: LXDE 
* Browser: midori
    
#### Bitcoin tools

* Electrum Wallet (Lightweight Desktop Bitcoin-Client - http://electrum.org)
* Armory (Bitcoin-Client, able to sign offline transactions - )
* BitAddress.html (Tools for generating Paperwallets, working with Private/Public Keys - http://www.bitaddress.org)
* Blockchain.info Backup Decrypt Tool (https://blockchain.info/DecryptWallet.html)
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
  
  `host#> command`  specifies a command which gets executed in the build-environment

  `btk#> command`   specifies a command which gets executed in the bitcoin-tool-kit environment (inside virtualbox)
  
  If there is a multiline-command, I only write the shell-token once, so you can easily copy-paste it
  
  
### Prerequisits

* A working Linux Distro (I use Ubuntu 13.10)
* Connection to the internet, while installing the different tools
* VirtualBox, wget, other tools
    
    `host#> sudo aptitude install virtualbox wget`
 
* An USB-Stick, a CD-Rom or any other bootable medium to install BTK to
* BKT should be able to run on any linux-compatible platform. Any PC, Notebook, Netbook, Raspberry Pie, ...
    
    
### Step-by-Step

 
 Here follows a step-by-step guide, how to build your own BTK-bootable Linux, so you can be sure to get an
 untempered install.
 
 If you want to take a shortcut, you can download prebuild images from here:
  PUT_URL_HERE...
  
 
  **Step 1)** Invent the universe (should be already done)

  **Step 2)**
    Create a working directory
      `host#> mkdir ~/btk; cd ~/btk`
    
  **Step 3)**
    Download Debian NetInst 
      http://cdimage.debian.org/debian-cd/7.2.0/i386/iso-cd/
  
      host#> wget http://cdimage.debian.org/debian-cd/7.2.0/i386/iso-cd/debian-7.2.0-i386-netinst.iso

  
  **Step 4)**
    Create a new Virtual Image in virtualbox
      
      host#> 
        VBoxManage createvm --name "btk" --register
        VBoxManage modifyvm "btk" --memory 1024 --acpi on --boot1 dvd
        VBoxManage modifyvm "btk" --nic1 bridged --bridgeadapter1 eth0
        VBoxManage modifyvm "btk" --ostype Debian
        
        VBoxManage createhd --filename ./btk.vdi --size 10000
        VBoxManage storagectl "btk" --name "IDE" --add ide
        VBoxManage storageattach "btk" --storagectl "IDE" \
            --port 0 --device 0 --type hdd --medium ./btk.vdi
        VBoxManage storageattach "btk" --storagectl "IDE" \
            --port 1 --device 0 --type dvddrive --medium debian-7.2.0-i386-netinst.iso

        #Start the VM
        VBoxManage startvm btk


  **Step 5)**
    Follow the Debian-Install (I used the Text-Based one) with defaults to install the Debian NetImg inside the new     VirtualBox Image.
   
    Some notes on settings:
      *) Language/KeyMap: As you like
      *) Hostname: btk
      *) Domain: btk
      *) Root-Password: As you like, but remember it (not really security relevant)
      *) User: btk
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
   
    root@btk#>
        apt-get update
        apt-get upgrade
        apt-get install lxde wget midori git
        
        # set LXDE as default GUI, check that "startlxde" is selected
        update-alternatives --config x-session-manager
        
        # and start it at boot
        update-rc.d lightdm defaults

        # LXDE starten
        /etc/init.d/lightdm start

**Step 7)** (optional) Install VirtualBox ClientExtensions (for better Performance inside the VM)

In the VirtualBox Window, click on "Devices" > "Install Guest Additions"

Open a root-terminal

    root@btk#> 
        apt-get install build-essential module-assistant
        m-a prepare
        mount /media/cdrom
        sh /media/cdrom/VBoxLinuxAdditions.run
    
**Step 8)** Bitcoin related Tools/Programs

Open a normal terminal - basefolder 

    btk#>
        cd ~/Desktop
        mkdir BitcoinTools; cd BitcoinTools

Open a root terminal - general tools
    
    root@btk#>
        apt-get install qrencode zbar-tools
        
**Step 8.1)** BitAdress.org

Open a normal terminal    

    btk#>
        cd ~/Desktop/BitcoinTools
        wget https://raw.github.com/pointbiz/bitaddress.org/master/bitaddress.org.html
        
        # check if the checksum is in the changelog:
        wget -o/dev/null -O- http://www.bitaddress.org/pgpsignedmsg.txt | \
            grep -P2 `sha1sum bitaddress.org.html` - || echo CHECKSUM FAIL

**Step 8.2)** Blockchain.info Wallet Recovery Tool

Open a normal terminal    

    btk#>
        cd ~/Desktop/BitcoinTools/
        wget https://blockchain.info/DecryptWallet.html
        mv DecryptWallet.html Decrypt_BlockchainInfo_Wallet.html
        sha1sum Decrypt_BlockchainInfo_Wallet.html | grep 50451490e0e5ac571b5ff0c6c52cb5c47440fbd5 || echo CHECKSUM FAIL

**Step 8.3)** SX

Open a root terminal - install/compile the sx-toolset

    root@btk#>
        cd /tmp
        wget http://sx.dyne.org/install-sx.sh
        bash ./install-sx.sh

Open a normal terminal - install a nice offline copy of the documentation (optional)
    
    btk#>
        mkdir ~/Desktop/BitcoinTools/doc; cd ~/Desktop/BitcoinTools/doc
        wget -m http://sx.dyne.org/index.html
        mv sx.dyne.org/ sx_doc

**Step 8.4)** Electrum

Open a root terminal    

    root@btk#>
        apt-get install python-qt4 python-pip
        pip install http://download.electrum.org/Electrum-1.9.2.tar.gz#md5=0666168901b7aa2c0d7cf42947966ceb
        su bkt
        cd ~/Desktop/BitcoinTools
        ln -s `which electrum` electrum

**Step X)** Generate a Live-Enviroment via Linux Live Kit (http://www.linux-live.org/)

Open a root terminal

    root@btk#>
        apt-get install squashfs-tools
        cd
        git clone https://github.com/Tomas-M/linux-live.git
        cd linux-live
        
        #edit config -> set name to "btk"
        #(todo, provide own config)
        #   LIVEKITNAME="btk"
        #   VMLINUZ=/boot/vmlinuz-3.2.0-4-486 

        nano .config

        ./build
        
        
wip...bkt