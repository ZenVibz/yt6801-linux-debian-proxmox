Markdown
# Step-by-Step Implementation: YT6801 Driver on Proxmox 9

This guide details the procedure to activate the Motorcomm YT6801 controller on Proxmox VE 9.1.6+ using the official Debian Backports.

## 1. Prerequisites
Ensure your system is up to date and headers are installed for your specific kernel:
```bash
apt update && apt full-upgrade -y
apt install pve-headers-$(uname -r) -y
reboot
2. Enabling Debian Trixie Backports
The driver yt6801-dkms is available in the Trixie backports repository. Add it to your sources:

Bash
echo "deb [http://deb.debian.org/debian](http://deb.debian.org/debian) trixie-backports main contrib non-free non-free-firmware" > /etc/apt/sources.list.d/backports.list
apt update
3. Driver Installation via DKMS
Install the driver. DKMS (Dynamic Kernel Module Support) will automatically compile the module for your current kernel:

Bash
apt install -t trixie-backports yt6801-dkms -y
4. Module Activation
Load the driver into the kernel and verify the interface:

Bash
modprobe yt6801
ip l
Note: You should see a new interface (e.g., enp1s0) in the list.

5. Proxmox Networking Configuration
To use this interface for your Virtual Machines:

Navigate to Proxmox Web UI > Node > Network.

Create a new Linux Bridge (vmbr1).

Set Bridge ports to your new interface name (e.g., enp1s0).

Apply Configuration.

Attach your VM network hardware to vmbr1.

Technical Note: Why DKMS?
Using DKMS is critical for Proxmox. Since Proxmox updates its kernel frequently, a standard "make install" would break after every update. DKMS triggers a re-compilation of the YT6801 driver every time a new PVE kernel is detected, ensuring zero downtime.
