# HPC Clusters

## From node ID to xname

Component names (xnames) identify the geolocation for hardware components in the HPE Cray EX system.

On the node, you can run the following command to get the xname:

```bash
cat /etc/cray/xname
```

The xname has the following format:

```text
xXcCsSbBnN
```

| Field | Description |
|-------|-------------|
| x     | Cabinet number |
| c     | Chassis number |
| s     | Slot number |
| b     | Card number |
| n     | Node number |

For CSCS GH200 system, the node number is always `0` (`n0`).
The card number can be either `0` or `1`, for the two nodes in the compute blade.

