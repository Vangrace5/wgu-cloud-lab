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

```text
Remote Laptop
    |
    | Tailscale
    v
Proxmox Host
    |
    v
Ubuntu Server VM
    |
    +-- SSH
    +-- Nginx
    +-- Tailscale
