# Spack

## Testing locally with Spack

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

s = Spec("spfft ^[virtuals=fftw-api] nvpl-fft") # (1)!
sc = concretize_one(s) # (2)!
```

1. Define the Spack spec to concretize.
2. Concretize the spec.

One can then inspect the concretized spec, for example:

```python
sc["fftw-api"].libs.ld_flags
sc["fftw-api"].headers
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
