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
/srv> tree -p -h -u -g -L 3
.
├── [drwxr-xr-x tftp     tftp       32]  tftpboot
│   ├── [drwxr-xr-x root     root       14]  EFI
│   │   └── [drwxr-xr-x root     root      126]  aarch64
│   ├── [drwxr-xr-x root     root       34]  grub
│   │   ├── [drwxr-xr-x root     root     2.6K]  arm64-efi
│   │   └── [-rw-r--r-- root     root      155]  grub.cfg
│   └── [drwxr-xr-x root     root     1.2K]  sles15sp7
│       ├── [-rwxr-xr-x root     root      74K]  ARCHIVES.gz
│       ├── [dr-xr-xr-x root     root       14]  boot
│       ├── [-rwxr-xr-x root     root      92K]  ChangeLog
│       ├── [-rwxr-xr-x root     root      14K]  CHECKSUMS
│       ├── [-rwxr-xr-x root     root      827]  CHECKSUMS.asc
│       ├── [-rwxr-xr-x root     root     1.4K]  COPYRIGHT
│       ├── [-rwxr-xr-x root     root     1.6K]  COPYRIGHT.de
│       ├── [drwxr-xr-x root     root     1.9K]  docu
│       ├── [drwxr-xr-x root     root        8]  EFI
│       ├── [-rwxr-xr-x root     root     1.6K]  gpg-pubkey-09d9ea69-67c857f3.asc
│       ├── [-rwxr-xr-x root     root      969]  gpg-pubkey-39db7c82-66c5d91a.asc
│       ├── [-rwxr-xr-x root     root     1.6K]  gpg-pubkey-3fa1d6ce-67c856ee.asc
│       ├── [-rwxr-xr-x root     root     1.0K]  gpg-pubkey-50a3dd1c-50f35137.asc
│       ├── [-rwxr-xr-x root     root     1.6K]  gpg-pubkey-73f03759-626bd414.asc
│       ├── [-rwxr-xr-x root     root     1.6K]  gpg-pubkey-d588dc46-63c939db.asc
│       ├── [-rwxr-xr-x root     root     1.1K]  INDEX.gz
│       ├── [-rwxr-xr-x root     root     2.1K]  ls-lR.gz
│       ├── [drwxr-xr-x root     root       26]  media.1
│       ├── [drwxr-xr-x root     root      172]  Module-Basesystem
│       ├── [drwxr-xr-x root     root      172]  Module-Containers
│       ├── [drwxr-xr-x root     root      172]  Module-Desktop-Applications
│       ├── [drwxr-xr-x root     root      172]  Module-Development-Tools
│       ├── [drwxr-xr-x root     root      172]  Module-HPC
│       ├── [drwxr-xr-x root     root      172]  Module-Legacy
│       ├── [drwxr-xr-x root     root      172]  Module-Public-Cloud
│       ├── [drwxr-xr-x root     root      172]  Module-Python3
│       ├── [drwxr-xr-x root     root      172]  Module-SAP-Applications
│       ├── [drwxr-xr-x root     root      172]  Module-Server-Applications
│       ├── [drwxr-xr-x root     root      172]  Module-Systems-Management
│       ├── [drwxr-xr-x root     root      172]  Module-Transactional-Server
│       ├── [drwxr-xr-x root     root      172]  Module-Web-Scripting
│       ├── [drwxr-xr-x root     root      172]  Product-HA
│       ├── [drwxr-xr-x root     root      172]  Product-SLES
│       ├── [-rwxr-xr-x root     root     2.6K]  README
│       ├── [drwxr-xr-x root     root     3.2K]  repodata
│       ├── [-rwxr-xr-x root     root     1.6K]  suse_ptf_key_2023.asc
│       └── [-rwxr-xr-x root     root      970]  suse_ptf_key.asc
└── [drwxr-xr-x root     root       26]  www
    ├── [drwxr-xr-x root     root        0]  cgi-bin
    └── [drwxr-xr-x root     root       34]  htdocs
        ├── [-rw-r--r-- root     root      497]  50x.html
        └── [dr-xr-xr-x root     root     6.0K]  sles15sp7

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
