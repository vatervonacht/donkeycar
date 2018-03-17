# Automated Raspberry Pi Setup

## Prepare Pi for Ansible Scripts

The Raspberry Pi must have the public SSH key installed in `~/.ssh/authorized_keys`.
Run the following command

````
$ ./scripts/piconfig
````

The script will prompt you for your WiFi network spec then search for all 
Raspberry Pis on the network - this step may ask for your password (it
uses `sudo`).  Then you will be asked to enter the IP address of the Pi you
wish to update.  
