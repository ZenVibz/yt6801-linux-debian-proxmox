Motorcomm YT6801 Ethernet not working on Debian / Proxmox (N100 / N150 Mini PC)

⚠️ **Problem:** The Motorcomm YT6801 Ethernet controller is detected by Linux but no network interface appears on Debian or Proxmox.

✅ **Solution:** Use a USB Ethernet adapter (recommended) or replace the network controller.

This repository documents the investigation, diagnosis and practical solutions for this compatibility issue.

## Table of contents

- Hardware
- Goal
- Problem
- Diagnosis
- Solutions tested
- Practical solutions
- Summary

Many N100 and N150 mini PCs use the Motorcomm YT6801 Ethernet controller.  
Under Linux, Debian 13 and Proxmox VE, this Ethernet interface is often not detected because no driver is available in the standard kernels.

This document explains the Motorcomm YT6801 compatibility issue on Linux and provides practical solutions for Debian and Proxmox installations.

Hardware

Mini-PC : Ninkear N150 (Alder Lake-N)

Ethernet controller :

Motorcomm Microelectronics YT6801 Gigabit Ethernet Controller
PCI ID : 1f0a:6801

Detected with:

lspci -nn

Result:

01:00.0 Ethernet controller [0200]: Motorcomm Microelectronics YT6801 Gigabit Ethernet Controller [1f0a:6801]

Wi-Fi controller:

Realtek RTL8821CE
Goal

Install:

Debian 13 (Trixie)

then Proxmox VE

and use the built-in Ethernet interface (YT6801).

Problem

After installing Debian:

ip a

Output:

lo
wlp2s0

The Ethernet interface does not appear.

However the hardware is detected by PCI.

Diagnosis

The system detects the Ethernet controller:

Motorcomm YT6801 [1f0a:6801]

But the Linux kernel does not provide a driver for this PCIe controller.

Checking kernel configuration:

grep -i motorcomm /boot/config-$(uname -r)

Result:

CONFIG_MOTORCOMM_PHY is not set

Meaning:

only the PHY layer driver exists

the MAC PCIe driver for YT6801 is missing

Solutions tested (not working)
Kernel upgrade

Installed a newer kernel:

Linux 6.19.8

After reboot:

uname -r
6.19.8-061908-generic

But:

ip link

still shows only:

lo
wlp2s0

No Ethernet interface.

Loading Motorcomm PHY module

The kernel contains:

motorcomm.ko

Loading it:

modprobe motorcomm

Result:

No Ethernet interface appears.

Reason:

motorcomm.ko is only a PHY driver
not the PCIe MAC driver required for YT6801
Compiling external drivers

Several drivers found online were tested:

Motorcomm driver archives

GitHub repositories

community patches

Results:

repositories unavailable

archives invalid

driver incompatible with modern kernels

compilation errors

Example:

fuxi-gmac-phy.o error
Error 2
Conclusion

The controller:

Motorcomm YT6801

is not currently supported by the standard Linux kernels used by Debian or Proxmox.

The kernel includes only:

Motorcomm PHY driver

but not the full PCIe Ethernet driver.

Therefore no Ethernet interface can be created.

Practical solutions

After extensive testing, only two practical solutions exist.

Option 1 — USB Ethernet adapter (recommended)

Use a USB-Ethernet adapter based on a chipset well supported by Linux.

Recommended chipsets:

Realtek RTL8153
ASIX AX88179

Advantages:

works immediately

supported by Debian and Proxmox

stable for servers

inexpensive (~10-15 €)

After connecting:

ip a

Example interface:

enxXXXXXXXX

This interface can then be used by Proxmox.

Option 2 — Replace the network card

Replace the Motorcomm controller with a supported one:

Examples:

Intel I225
Intel I226

Limitations:

depends on mini-PC design

sometimes impossible (soldered controller)

requires hardware modification

Recommendation for Proxmox servers

For a homelab or server:

network stability is critical

experimental drivers are risky

Therefore the most reliable solution is:

use a USB Ethernet adapter
Summary

Hardware:

Motorcomm YT6801 Ethernet controller

Problem:

no driver available in Debian / Proxmox kernels

Working solutions:

1. USB Ethernet adapter
2. Replace network controller
Keywords

Linux
Debian 13
Proxmox VE
Motorcomm YT6801
N100 mini PC
N150 mini PC
Ethernet not working Linux

Purpose

This document aims to help other users avoid spending hours testing non-working solutions when encountering the Motorcomm YT6801 Ethernet compatibility issue under Linux.

## Additional documentation

- Hardware details → hardware.md
- Technical diagnosis → diagnosis.md

## Search keywords

Motorcomm YT6801 Linux  
YT6801 Debian Ethernet not working  
Proxmox YT6801 driver  
N100 mini PC Ethernet Linux  
N150 Linux network issue  
Motorcomm YT6801 driver Linux

## Related searches

People often find this page when searching for:

- YT6801 Ethernet not detected Linux
- Motorcomm YT6801 Debian driver
- Proxmox Ethernet not working N100 mini PC
- N150 mini PC network driver Linux
