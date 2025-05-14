# Troubleshooting

## Wi-Fi Issues (ASUS ROG STRIX B650E-E Motherboard)

Sometimes the Wi-Fi stops working. 
The Wi-Fi icon disapperas or shows a `?` on Ubuntu.

```console
$ sudo lspci # (1)!
06:00.0 Ethernet controller: Intel Corporation Ethernet Controller I225-V (rev 03)
07:00.0 Network controller: MEDIATEK Corp. MT7922 802.11ax PCI Express Wireless Network Adapter
```

1. List all PCI devices.

Troubleshooting steps:

1. Turn off the system.
2. Unplug the system from the power outlet.
3. Press the power button multiple times, for several seconds (up to 60s). This discharges the capacitors.
4. Reboot the system.

After discharging the capacitors and rebooting the system, the Wi-Fi should work again.

