# PXE-Booting-Proxmox
Installing Proxmox VE on a bare metal computer using PXE.

## Environment
This guide assumes a typical home network with an existing router providing DHCP. No changes to the router or existing DHCP configuration are required.


For this setup I used:

- **Router** - Existing DHCP server

- **HP Envy x360 laptop (Debian 13.7)** - PXE server, DHCP proxy, TFTP server, and HTTP server

- **HP EliteDesk 800 desktop** - PXE client

## Configuration

#### Client
I am using UEFI. If you need to use legacy boot, the steps below will mostly be the same, but it will require some tinkering.

Enable network booting/network stack in BIOS and set IPv4 at the top of the boot order. I also found it helpful to disable the other boot options while I was troubleshooting.

That will be all the configuration you need for the client, just make sure it has power and ethernet ;)

#### Server
While writing this, I did a fresh install of Debian 13.7 on my laptop, but this setup likely works for any Debian-based distribution.

Let's start with making the root directory for this project (I made mine in my home directory)

`mkdir proxmox-pxe-boot`

Now let's make the directories we'll be serving files from

`cd proxmox-pxe-boot && mkdir tftp http`

Now, we will install iPXE the nifty "boot firmware"

`sudo apt install ipxe`

Copy the PXE binary to your tftp folder

`sudo cp /usr/lib/ipxe/ipxe.efi ./tftp`




