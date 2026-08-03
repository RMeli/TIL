# Slurm

Tip and tricks about the [Slurm] workload manager.

## Print output per task

When multiple ranks output to stdout (for example when using `nvidia-smi` with multiple ranks),
the output can be labelled with the rank number using the `--label` flag.

!!! example "Label output per task"

    ```bash
    srun -n 4 --gpus-per-task=1 --label nvidia-smi
    ```

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
