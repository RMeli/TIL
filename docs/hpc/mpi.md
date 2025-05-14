# MPI

## MPI Environment Variables

### Environment variables set for MPI applications

It is sometimes useful to use environment variables related to MPI,
for example to set a file name for each process.

=== "MPICH"
    
    The default `mpiexec` (`mpiexec.hydra`) sets the following environment variables
    for each process:

    * `PMI_RANK`: MPI rank
    * `PMI_SIZE`: tatal number of MPI processes

=== "OpenMPI"

    [OpenMPI] provides the following environment variables for each process:

    * `OMPI_COMM_WORLD_RANK`: numbere of ranks in `MPI_COMM_WORLD`
    * `OMPI_COMM_WORLD_SIZE`: rank of the current process in `MPI_COMM_WORLD`
    * `OMPI_COMM_WORLD_LOCAL_RANK`: relative rank of the process on the node
    * `OMPI_COMM_WORLD_LOCAL_SIZE`: number of ranks on the node

=== "Slurm" 

    [Slurm] provides the following environment variables for each process:

    * `SLURM_NPROCS`: total number of processes
    * `SLURM_PROCID`: MPI rank of the current process
    * `SLURM_LOCALID`: node-local ID for the process

??? example "Naming nsys report files with MPI rank"

    ```bash
    mpiexec -n 4 nsys profile --trace=cuda,mpi --mpi-impl=mpich -o report_rank%q{PMI_RANK} <APPLICATION>
    ```

    The command above will create a report for each MPI rank.

[Slurm]: https://slurm.schedmd.com/
[OpenMPI]: https://www.open-mpi.org/
