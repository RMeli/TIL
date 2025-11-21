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

## Power measurements

On Cray EX systems, power measurements can be obtained from the `pm_counters` in

```
/sys/cray/pm_counters/
```

??? example "`pm_counters` for a GH200 node on Alps"
    
    ```console
    $ ls -l /sys/cray/pm_counters/
    total 0
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel0_energy
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel0_power
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel0_power_cap
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel1_energy
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel1_power
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel1_power_cap
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel2_energy
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel2_power
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel2_power_cap
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel3_energy
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel3_power
    -r--r--r-- 1 root root 65536 Nov 21 10:48 accel3_power_cap
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu0_energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu0_power
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu0_temp
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu1_energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu1_power
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu1_temp
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu2_energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu2_power
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu2_temp
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu3_energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu3_power
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu3_temp
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu_energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 cpu_power
    -r--r--r-- 1 root root 65536 Nov 21 14:38 energy
    -r--r--r-- 1 root root 65536 Nov 21 14:38 freshness
    -r--r--r-- 1 root root 65536 Nov 21 14:38 generation
    -r--r--r-- 1 root root 65536 Nov 21 10:45 power
    -r--r--r-- 1 root root 65536 Nov 21 10:45 power_cap
    -r--r--r-- 1 root root 65536 Nov 21 14:38 raw_scan_hz
    -r--r--r-- 1 root root 65536 Nov 21 14:38 startup
    -r--r--r-- 1 root root 65536 Nov 21 14:38 version
    ```

!!! example "Measuring power consumption"

    The following script samples relevant `pm_contters`:

    ```bash
    #!/bin/bash

    while [ ! -f stop_monitor ];
    do
      cat /sys/cray/pm_counters/power >> node_power.txt
      cat /sys/cray/pm_counters/accel0_power >> gpu0_power.txt
      cat /sys/cray/pm_counters/accel1_power >> gpu1_power.txt
      cat /sys/cray/pm_counters/accel2_power >> gpu2_power.txt
      cat /sys/cray/pm_counters/accel3_power >> gpu3_power.txt
      cat /sys/cray/pm_counters/cpu0_power >> cpu0_power.txt
      cat /sys/cray/pm_counters/cpu1_power >> cpu1_power.txt
      cat /sys/cray/pm_counters/cpu2_power >> cpu2_power.txt
      cat /sys/cray/pm_counters/cpu3_power >> cpu3_power.txt
      sleep 5
    done
    ```

    It can run alongside an application to log power consumption over time.

    ```bash
    #!/bin/bash
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=4
    #SBATCH --gpus-per-task=1
    #SBATCH --time=00:01:00
    #SBATCH --partition=debug
    #SBATCH --uenv=prgenv-gnu/25.6:v2
    #SBATCH --view=default

    rm -f stop_monitor

    date

    srun --overlap -n1 monitor.sh &
    pid=$! # Get PID of most recent background process

    srun --overlap -n1 ./bindgpu0.sh hwloc-bind --cpubind core:0-7 -- node-burn/build/burn -ggemm,5000 -cstream,500000 -d30 &
    pidj1=$!

    srun --overlap -n1 ./bindgpu1.sh hwloc-bind --cpubind core:72-79 -- node-burn/build/burn -ggemm,5000 -cstream,500000 -d30 &
    pidj2=$!

    wait $pidj1
    wait $pidj2

    sleep 10
    touch stop_monitor

    date
    ```

    ??? note "`trap` does not play nice with Slurm"

        Using `trap` to stop the monitoring script does not work well with Slurm jobs, and and error is produced.
        Instead, a sentinel file (`stop_monitor`) is used to signal the monitoring script to stop.
