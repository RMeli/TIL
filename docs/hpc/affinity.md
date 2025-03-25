# CPU affinity

## Print Slurm CPU affinity mask

An affinity mask is a bitmask where indices correspond to logical processors.
The least significant bit corresponds to the first logical processor number on the system,
while the most significant bit corresponds to the last logical processor number on the system.

To print the affinity mask of a Slurm job, one can use the following command:

```bash
srun [...] bash -c 'printf "%02g:%s\n" ${SLURM_LOCALID} $(hwloc-bind --get --taskset)' | sort
```

Equivalently, one can use `--cpu-bind=verbose` with `srun` to get the same information.
