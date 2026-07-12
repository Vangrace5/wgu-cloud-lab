# WGU Cloud Lab

Hands-on cloud and network engineering labs built while preparing for the WGU B.S. in Cloud and Network Engineering, AWS specialization.

## Lab Environment

- Dell Tower ECT1250
- Intel Core Ultra 7 265
- 32GB DDR5 RAM
- 1TB NVMe SSD
- Proxmox VE
- Ubuntu Server 24.04.4 LTS
- Tailscale
- OpenSSH
- Nginx

## Current Architecture

Remote Laptop  
↓ Tailscale  
Proxmox Host  
↓  
Ubuntu Server VM  
├── SSH  
├── Nginx  
└── Tailscale

## Completed Work

- Installed Proxmox VE directly on physical hardware
- Diagnosed NVMe detection failure caused by Dell RAID/RST mode
- Changed storage mode from RAID On to AHCI/NVMe
- Configured a static management address
- Configured Proxmox community repositories
- Installed and updated Ubuntu Server in a KVM virtual machine
- Installed the QEMU guest agent
- Enabled secure remote access with Tailscale
- Enabled remote administration with SSH
- Installed Nginx
- Published a custom test webpage
- Created a working Proxmox snapshot
- Configured the Ubuntu VM to start automatically with the host
- Verified Proxmox, Ubuntu, SSH, Tailscale, and Nginx recover after reboot

## Lab 01

Proxmox, Ubuntu Server, Tailscale, and Nginx

## Skills Practiced

- Virtualization
- Linux administration
- Server installation
- BIOS and storage troubleshooting
- Static IP configuration
- Remote access
- SSH
- Web server deployment
- Service management
- Snapshots
- Infrastructure documentation

## Security Notes

This repository does not publish:

- passwords
- private IP addresses
- Tailscale IP addresses
- email addresses
- service tags
- authentication keys
- private network details

## Roadmap

- Docker
- Windows Server
- Active Directory
- pfSense or OPNsense
- VLANs
- AWS S3
- AWS EC2
- AWS VPC
- Terraform
- Monitoring and logging
- Backup strategy
