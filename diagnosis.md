```markdown
# Technical diagnosis

## Hardware detection

The Ethernet controller is detected by the system:

```bash
lspci -nn

Result:

Motorcomm YT6801 [1f0a:6801]
Network interfaces

Checking available interfaces:

ip link

Result:

lo
wlp2s0

The Ethernet interface is missing.

Kernel support

Checking kernel configuration:

grep -i motorcomm /boot/config-$(uname -r)

Result:

CONFIG_MOTORCOMM_PHY is not set

Only the PHY layer driver is available.

Kernel modules

Searching for Motorcomm modules:

find /lib/modules/$(uname -r) -name "*motor*"

Result:

motorcomm.ko

This module provides only PHY support and does not create a PCIe Ethernet interface.

Driver compilation attempts

Multiple external drivers were tested:

Motorcomm driver archives

community GitHub repositories

kernel patches

Compilation failed due to kernel API incompatibilities.
