# Fortran

Additional code snippets and notes can be found at [RMeli/fortran-playground].

## ScaLAPACK

### `numroc`

`numroc` computes the NUMber of Rows Or Columns of a distributed matrix owned by the process indicated by `iproc`.
Trying to use `numroc` in a Fortran program, results in the following error:

```
   61 |     m = numroc(n, 1, myrow, 0, nprow)
      |        1
Error: Function 'numroc' at (1) has no IMPLICIT type
```

In order to avoid this error, the function needs to be declared as
[external](https://docs.oracle.com/cd/E19957-01/805-4939/6j4m0vn9d/index.html).

```fortran
integer, external :: numroc
```

??? "`external` statement"
    The `external` statement specifies procedures or dummy procedures as external, and allows their symbolic names to be used as actual arguments.

## Busy Wait

```fortran
subroutine busywait(wait_time)
   real(kind=dp), intent(in) :: wait_time ! (1)!

   real(kind=dp) :: t_start, t_now, elapsed_time

   elapsed_time = 0.0_dp

   call cpu_time(t_start)

   do while (elapsed_time < wait_time)
      call cpu_time(t_now)
      elapsed_time = t_now - t_start
   end do

end subroutine
```

1. Time to wait in seconds.

[RMeli/fortran-playground]: https://github.com/RMeli/fortran-playground
