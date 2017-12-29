# Simple Auto-Deployed Greengrass Core Gateway

This uses Vagrant and Ansible in concert to:

* Spin up an Ubuntu/Xenial virtual machine with Virtual Box
* Run an Ansible playbook against that VM to install AWS Greengrass
  Core and get it running as a service.

## My Setup

* Connection to the Internet and local router with DHCP
* Mac OS X High Sierra 10.13.2 (other OS'es TBD) 
* Ansible 2.4.2.0 
* Virtual Box 5.1.26
* Vagrant 1.9.7 

## Making it Work

### Download AWS Artifacts

There are two main artifacts that you will need to have downloaded from AWS
before you can get started.  These are documented in the 
[AWS tutorials](http://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html) 
that walk you through creation of a Greengrass group and corresponding Core.
The artifacts are:

* AWS Greengrass Core SDK
* Compressed archive containing default Greengrass configuration, certificates and
  private key for accessing AWS IoT.

### Set up Environment

Set the appropriate environment variables:

* `SSH_PRIVATE_KEY` - Full path to the RSA private SSH key file (e.g., `~/.ssh/id_rsa`)
* `SSH_PUBLIC_KEY` - Full path to the RSA public SSH key file (e.g., `~/.ssh/id_rsa.pub`)
* `GREENGRASS_CORE_SETUP_ARCHIVE` - Path to downloaded compressed archive file from 
  AWS containing Greengrass setup files including certs and private key.

If you don't have a key pair yet, you'll have to generate one using `ssh-keygen`.

The compressed archive file from AWS contains AWS IoT certificates AND the 
private key for the Greengrass Core device.  (See "Download AWS Artifacts" for more
information)

> :warning: **WARNING** - Make sure that the containing folder and the downloaded archive from AWS
> are both protected as they contain secrets (e.g., `chmod 0600`)


[aws-tutorial] http://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html
