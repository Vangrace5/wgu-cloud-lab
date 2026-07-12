# Lab 01: Proxmox, Ubuntu Server, Tailscale, and Nginx

## Objective

Build a remotely managed Ubuntu Server virtual machine on a Proxmox home lab and verify that SSH, Tailscale, and Nginx recover automatically after reboots.

## Environment

- Proxmox VE host on Dell Tower hardware
- Ubuntu Server 24.04.4 LTS
- KVM virtualization
- QEMU guest agent
- Tailscale
- OpenSSH
- Nginx

## VM Configuration

- VM name: `ubuntu-lab01`
- vCPUs: 2
- RAM: 4GB
- Disk: 40GB
- Firmware: UEFI
- Machine type: q35
- Network adapter: VirtIO
- Storage controller: VirtIO SCSI

## Work Completed

1. Created the Ubuntu Server VM in Proxmox.
2. Installed Ubuntu Server 24.04.4 LTS.
3. Configured the system timezone for Eastern Time.
4. Updated all installed packages.
5. Installed and verified the QEMU guest agent.
6. Installed Tailscale for secure remote access.
7. Enabled OpenSSH for remote administration.
8. Installed Nginx.
9. Replaced the default Nginx page with a custom test page.
10. Created a Proxmox snapshot.
11. Configured the VM to start automatically with the Proxmox host.
12. Rebooted both the VM and Proxmox host to verify service recovery.

## Troubleshooting

### Proxmox could not detect the NVMe SSD

The Dell BIOS was configured with RAID/RST enabled. The Proxmox installer could not detect the physical NVMe SSD.

### Resolution

Changed the Dell storage setting from:

`RAID On`

to:

`AHCI/NVMe`

After the change, the Proxmox installer detected the SSD correctly.

## Verification

The following services were confirmed active after reboot:

- `tailscaled`
- `ssh`
- `nginx`

The command used was:

`systemctl is-active tailscaled ssh nginx`

All three services returned `active`.

The custom Nginx webpage was also reachable remotely through Tailscale.

## Skills Demonstrated

- Bare-metal hypervisor deployment
- Virtual machine provisioning
- Linux server administration
- BIOS and storage-controller troubleshooting
- SSH administration
- Secure remote access
- Web server deployment
- Linux service management
- Snapshot creation
- Startup and reboot validation

## Security Notes

No passwords, authentication keys, private IP addresses, Tailscale IP addresses, email addresses, or service tags are included in this repository.

## Result

Successfully built and validated a remotely managed Ubuntu Server VM running on Proxmox with working SSH, Tailscale, and Nginx services.
