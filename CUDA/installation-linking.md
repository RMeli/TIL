# Installation and Linking

## Linking CUDA Runtime

 If the linker is unable to find basic CUDA functions, the `--cudart` flag is needed to explicitly link the CUDA runtime:
```make
main: $(OBJ) main.o
 $(CXX) $(CXXFLAGS) $(OBJ) main.o -o main -lcublas -lcudart
```
