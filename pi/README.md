# Raspberry Pi Setup

These instructions and scripts are based on the information at [this
site](http://docs.donkeycar.com/guide/install_software) and assume you are
installing from a Mac.

## Flash the Donkey Car Image Onto Micro SD Card

1. Download the [Donkey Car image](https://www.dropbox.com/s/wiudnm2dcsvoquu/donkey_v22.img.zip?dl=0) 
   to your computer and unzip.

````
    $ cd ~/Downloads
    $ unzip donkey_v22.img.zip
````
2. Plug an SD card into your computer
3. Download [SD Card Formatter](http://sdcard.org)
4. Open *SD Card Formatter* and format your SD card.  You may be able to
   get away with the *Quick Format* option.  The *Overwrite* option
   will take awhile.
5. Identify the device representing your SD card by executing the command
   below and looking for the disk with "Windows_FAT_32" boot partition:

````
    $ diskutil list
     ...
    /dev/disk5 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:     FDisk_partition_scheme                        *15.5 GB    disk5
       1:             Windows_FAT_32 boot                    43.7 MB    disk5s1
     ...
````
6. Now unmount that disk and flash the image to that disk. Be very careful
   to get the disk identifier correct.  This could irreparably wipe out
   your system data making your computer unusable.

````
    $ diskutil unmountDisk disk5
    $ sudo dd bs=1m if=donkey_v22.img of=/dev/disk5
````

7. View the disk definition again. You should now see a new Linux partition

````
    $ diskutil list
     ...
    /dev/disk5 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:     FDisk_partition_scheme                        *15.5 GB    disk5
       1:             Windows_FAT_32 boot                    43.7 MB    disk5s1
       2:                      Linux                         2.9 GB     disk5s2
     ...
````
8. Remount the disk.

````
    $ diskutil mountDisk disk5
````

9. Create a file called, `wpa_supplicant.conf` that looks like the file
   below.  Make sure the SSIDs and passwords match the wireless networks
   you plan having the Pi connect to. (NOTE: a sample can be found in
   the `files` directory)

````
    country=US
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    
    network={
      ssid="SSIDONE"
      psk="PASSWORD1"
    }
    network={
      ssid="SSIDTWO"
      psk="PASSWORD2"
    }
    network={
      ssid="SSIDTHREE"
      psk="PASSWORD3"
    }
````
10. Copy `wpa_supplicant.conf` to the `boot` partition of your SD card.

````
    $ cp wpa_supplicant.conf /Volumes/boot
````

11. Unmount the disk
````
    $ diskutil unmountDisk disk5
````

Your SD card is now ready to be inserted into the Raspberry Pi.  Do so now
and power up the Pi.

## Configure the Raspberry Pi

### Configure SSH For Remote Access
You need to generate a public/private key pair to connect remotely to the
the Pi.  This is a necessary prerequisite to running the configuration
scripts.

Generate your key pair using the `ssh-keygen` command.  The passphrase
is optional but offers a higher degree of security.

````
    $ ssh-keygen -t rsa -b 2048
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/me/.ssh/id_rsa): /Users/me/.ssh/id_donkey_car_rsa
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /Users/me/.ssh/id_donkey_car_rsa.
    Your public key has been saved in /Users/me/.ssh/id_donkey_car_rsa.pub.
    The key fingerprint is:
    SHA256:SOMERANDOMHASH_SOMERANDOMHASH_SOMERANDOMHASH me@mymac.lan
    The key's randomart image is:
    +---[RSA 2048]----+
    |             .*+*|
    |         . ..+ +o|
    |      . . ..o O o|
    |       + +.. B +.|
    |        S.=.E . .|
    |       o O=.     |
    |      . ==o.     |
    |      o*+=.      |
    |     o++=        |
    +----[SHA256]-----+
````

Now you need to add the key to your the authentication agent (`ssh-agent`)
If you entered a passphrase, you will need to re-enter below.

````
    $ ssh-add ~/.ssh/id_donkey_car_rsa
````

You should now be ready to run the automated configuration scripts.  These
will do the following.

* Copy the public SSH key to the Pi so that you can log in remotely without
  a password
* Run an Ansible playbook to install the requisite software/files
* Run the bluetooth setup script

Before you run these scripts, you will need to get the following information:

* WiFi network - e.g., 192.168.1.0/24
* Bluetooth hardware address of your PS3 controller.
  See [these instructions](http://osxdaily.com/2014/12/28/connect-playstation-3-controller-mac-os-x/) 
  to pair your controller with a Mac and find the hardware address in hex.

Run the following command

````
$ ./scripts/piconfig
````

The script will prompt you for your WiFi network then search for all 
Raspberry Pis on the network - this step may ask for your password (as it
uses `sudo`).  It will present a list of all the Pis on the network and
prompt you to enter the IP address of the Pi you wish to update.  (NOTE:
be careful you are selecting the right IP address)

Once the script is finished running the Ansible playbook (you will see
the output from this playbook) you will be prompted for the Bluetooth
hardware address of the PS3 controller.  Enter that information 
and follow the instructions.
