# Fortran

## ScaLAPACK

### `numroc`

`numroc` computes the NUMber of Rows Or Columns of a distributed matrix owned by the process indicated by `iproc`.
Trying to use `numroc` in a Fortran program, results in the following error:

```
   61 |     m = numroc(n, 1, myrow, 0, nprow)
      |        1
Error: Function 'numroc' at (1) has no IMPLICIT type
```

In order to avoid this error, the function needs to be declared as [external](https://docs.oracle.com/cd/E19957-01/805-4939/6j4m0vn9d/index.html).

??? "`external` statement"
    The `external` statement specifies procedures or dummy procedures as external, and allows their symbolic names to be used as actual arguments.

```fortran
integer, external :: numroc
```

