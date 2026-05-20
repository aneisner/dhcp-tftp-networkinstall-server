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
