# Spack

## Testing Locally with Spack

The [Spack](https://spack.io) package manager is written in Python, therefore it can be used interactively from a Python
shell:

```shell
> spack python
```

### Concretize and inspect a spec

A [Spack spec] can be concretized and inspected from the Python shell:

```python
from spack.spec import Spec
from spack.concretize import concretize_one

s = Spec("cp2k ^[virtuals=mpi] mpich ^[virtuals=blas,lapack] openblas ^[virtuals=scalapack] netlib-scalapack") # (1)! Define the Spack spec
sc = concretize_one(s) # (2)! Concretize the spec
```

One can then inspect the concretized spec, for example:

```python
sc["mpi"].libs.ld_flags
sc["blas"].headers
```

[Spack]: https://spack.io
[Spack spec]: https://spack.readthedocs.io/en/latest/packaging_guide.html#spack-specs

## Conflicts

The following conflicts are equivalent:

```python
conflicts("+rocm +cuda")
conflicts("+rocm", when="+cuda")
conflicts("+cuda", when="+rocm")
```

The first option is cleaner, shorter, and less asymmetric.
