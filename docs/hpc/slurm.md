# Slurm

Tip and tricks about the [Slurm] workload manager.

## Slurm configuration

### Check generic resources (Gres)

The following command will show the generic resources (Gres) available on the node:

```bash
scontrol show nods | grep Gres | sort -u
```

This command can be used to check for misconfigures nodes.

### Check reservations

```bash
scontrol show res
```

To run on a serervations use the `--reservation` flag:

```bash
srun --reservation=<reservation_name> <command>
```

[Slurm]: https://slurm.schedmd.com/
