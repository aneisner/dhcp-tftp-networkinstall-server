# dhcp-tftp-networkinstall-server

# PXE Boot Server Setup for SLES 15 SP7 (ARM / EFI)

This repository contains the necessary configuration files to set up a SUSE Linux Enterprise Server (SLES) 15 SP7 as a PXE boot server. The purpose of this setup is to enable the network-based or automated installation of **SLES 15 SP7** on an **ARM server (aarch64)** running in **EFI mode**.

## Repository Structure

The files in this repository mirror the file system structure of the SLES server:

* **`/etc/dhcpd.conf`**: The DHCP server configuration file. It is configured to detect PXE clients on the ARM architecture and assign the correct EFI boot file (e.g., `bootaa64.efi` or `grub.efi`) via TFTP.
* **`/etc/sysconfig/tftp`**: The basic settings for the TFTP service (e.g., defining the TFTP root directory).
* **`/srv/`**: This directory contains the actual boot files served via TFTP (typically located under `/srv/tftpboot`). This includes:
    * The EFI boot image (GRUB2 for ARM64)
    * The Kernel (`linux`) and Initrd (`initrd`) from the SLES 15 SP7 installation media
    * GRUB configuration files (e.g., `grub.cfg`) for the PXE boot menu

```bash
/srv> tree -L 3
.
├── tftpboot
│   ├── EFI
│   │   └── aarch64
│   ├── grub
│   │   ├── arm64-efi
│   │   └── grub.cfg
│   └── sles15sp7
│       ├── ARCHIVES.gz
│       ├── boot
│       ├── ChangeLog
│       ├── CHECKSUMS
│       ├── CHECKSUMS.asc
│       ├── COPYRIGHT
│       ├── COPYRIGHT.de
│       ├── docu
│       ├── EFI
│       ├── gpg-pubkey-09d9ea69-67c857f3.asc
│       ├── gpg-pubkey-39db7c82-66c5d91a.asc
│       ├── gpg-pubkey-3fa1d6ce-67c856ee.asc
│       ├── gpg-pubkey-50a3dd1c-50f35137.asc
│       ├── gpg-pubkey-73f03759-626bd414.asc
│       ├── gpg-pubkey-d588dc46-63c939db.asc
│       ├── INDEX.gz
│       ├── ls-lR.gz
│       ├── media.1
│       ├── Module-Basesystem
│       ├── Module-Containers
│       ├── Module-Desktop-Applications
│       ├── Module-Development-Tools
│       ├── Module-HPC
│       ├── Module-Legacy
│       ├── Module-Public-Cloud
│       ├── Module-Python3
│       ├── Module-SAP-Applications
│       ├── Module-Server-Applications
│       ├── Module-Systems-Management
│       ├── Module-Transactional-Server
│       ├── Module-Web-Scripting
│       ├── Product-HA
│       ├── Product-SLES
│       ├── README
│       ├── repodata
│       ├── suse_ptf_key_2023.asc
│       └── suse_ptf_key.asc
└── www
    ├── cgi-bin
    └── htdocs
        ├── 50x.html
        └── sles15sp7
```

## Installation & Deployment

To deploy this PXE server on a fresh SLES 15 SP7 system, follow these steps:

### 1. Install Required Packages
Install the DHCP and TFTP server packages:

sudo zypper in dhcp-server tftp

## CRITICAL: Network Configuration Adjustments

Before starting the services, **you must adapt the configuration files to match your specific network environment**. The provided files are templates and will not work out-of-the-box on a different network.

### 1. Adapt `/etc/dhcpd.conf`
Open the file and update the following parameters:
* **`subnet` and `netmask`**: Change these to match your actual network segment (e.g., `192.168.1.0 netmask 255.255.255.0`).
* **`range`**: Define the pool of IP addresses that will be handed out to PXE clients.
* **`option routers`**: Set the IP address of your default network gateway.
* **`next-server`**: This **must** be set to the IP address of this PXE/TFTP server.
