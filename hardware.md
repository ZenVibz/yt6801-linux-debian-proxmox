# Hardware details

## Mini PC

Model: Ninkear N10PRO
CPU: Intel Alder Lake-N (N150)

## Ethernet controller

Motorcomm Microelectronics YT6801 Gigabit Ethernet Controller

PCI ID:

lspci -nn
01:00.0 Ethernet controller [0200]: Motorcomm Microelectronics YT6801 Gigabit Ethernet Controller [1f0a:6801]
Wi-Fi controller

Realtek RTL8821CE


```bash
lspci -nn | grep -i network
02:00.0 Network controller [0280]: Realtek RTL8821CE
Tested operating systems

Debian 13 (Trixie)

Linux kernel 6.19 mainline

Proxmox VE installer
