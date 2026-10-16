# PXE-Booting-Proxmox
Installing Proxmox VE on a bare metal computer using PXE.

## Environment
This guide assumes a typical home network with an existing router providing DHCP. No changes to the router or existing DHCP configuration are required.


For this setup I used:

- **Router** - Existing DHCP server

- **HP Envy x360 laptop (Debian 13.7)** - PXE server, DHCP proxy, TFTP server, and HTTP server

- **HP EliteDesk 800 desktop** - PXE client

## Configuration

### Client
I am using UEFI. If you need to use legacy boot, the steps below will mostly be the same, but it will require some tinkering.

Enable network booting/network stack in BIOS and set IPv4 at the top of the boot order. I also found it helpful to disable the other boot options while I was troubleshooting.

That will be all the configuration you need for the client, just make sure it has power and ethernet ;)

### Server
While writing this, I did a fresh install of Debian 13.7 on my laptop, but this setup likely works for any Debian-based distribution.

### Proxmox Tool Setup
First, we are going to install the 'proxmox-auto-install-assistant'

In order to do this the proxmox package repository must be made known to apt (documentation: https://pve.proxmox.com/wiki/Package_Repositories)

Create a new file: 

`sudo touch /etc/apt/sources.list.d/proxmox.sources`

And paste:
```
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
```

You'll also need the proxmox keyring:

`wget https://enterprise.proxmox.com/debian/proxmox-archive-keyring-trixie.gpg -O /usr/share/keyrings/proxmox-archive-keyring.gpg`

Finally, you can install the tool:
`sudo apt install proxmox-auto-install-assistant`

### Directory Setup

Let's start with making the root directory for this project (I made mine in my home directory)

`mkdir proxmox-pxe-boot`

Now let's make the directories we'll be serving files from

`cd proxmox-pxe-boot && mkdir tftp http`

### Prepping ISO

Download the [Proxmox ISO for your machine](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso)

### iPXE Setup

Now, we will install iPXE the nifty "boot firmware"

`sudo apt install ipxe`

Copy the PXE binary to your tftp folder

`sudo cp /usr/lib/ipxe/ipxe.efi ./tftp`

### Caddy setup

I'm using caddy as my http server. Nginx is another popular option, but caddy is easier to setup.

Install caddy:
`sudo apt install caddy`

Then create your caddyfile in /etc/caddy/Caddyfile
```
:8000 {
  log {
    output file /var/log/caddy/caddy.log
  }
  root * "/home/yourusername/proxmox-pxe-boot/http/"
  file_server browse
}
```
Now reload caddy with the new configuration

`sudo systemctl reload caddy`
