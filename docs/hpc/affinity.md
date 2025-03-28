# CPU affinity

## List running threads and their CPU

To see which core each thread of a given application is running on, one can use the following command:

```bash
for i in $(pgrep APPLICATION); do ps -mo pid,tid,fname,user,psr -p $i; done
```

PSR is the processor that process is currently assigned to.

## Print affinity masks

An affinity mask is a bitmask where indices correspond to logical processors.
The least significant bit corresponds to the first logical processor number on the system,
while the most significant bit corresponds to the last logical processor number on the system.

### `hwloc-bind`

```bash
srun [...] bash -c 'printf "%02g:%s\n" ${SLURM_LOCALID} $(hwloc-bind --get --taskset)' | sort
```

??? example "Output"

    ```console
    $ srun --cpus-per-task 8 -N 1 --ntasks 32 bash -c 'printf "%02g:%s\n" ${SLURM_LOCALID} $(hwloc-bind --get --taskset)' | sort
    00:0x1fe
    01:0x1fe000000000000000000
    02:0x1fe000000000000000000000000000000000000
    03:0x1fe000000000000000000000000000000000000000000000000000000
    04:0x1fe00
    05:0x1fe00000000000000000000
    06:0x1fe00000000000000000000000000000000000000
    07:0x1fe00000000000000000000000000000000000000000000000000000000
    08:0x1fe0000
    09:0x1fe0000000000000000000000
    10:0x1fe0000000000000000000000000000000000000000
    11:0x1fe0000000000000000000000000000000000000000000000000000000000
    12:0x1fe000000
    13:0x1fe000000000000000000000000
    14:0x1fe000000000000000000000000000000000000000000
    15:0x1fe000000000000000000000000000000000000000000000000000000000000
    16:0x1fe00000000
    17:0x1fe00000000000000000000000000
    18:0x1fe00000000000000000000000000000000000000000000
    19:0x1fe00000000000000000000000000000000000000000000000000000000000000
    20:0x1fe0000000000
    21:0x1fe0000000000000000000000000000
    22:0x1fe0000000000000000000000000000000000000000000000
    23:0x1fe0000000000000000000000000000000000000000000000000000000000000000
    24:0x1fe000000000000
    25:0x1fe000000000000000000000000000000
    26:0x1fe000000000000000000000000000000000000000000000000
    27:0x1fe000000000000000000000000000000000000000000000000000000000000000000
    28:0x1fe00000000000000
    29:0x1fe00000000000000000000000000000000
    30:0x1fe00000000000000000000000000000000000000000000000000
    31:0x1fe00000000000000000000000000000000000000000000000000000000000000000000
    ```

### Slurm

```bash
srun [...] --cpu-bind=verbose
```

??? example "Output"

    ```
    cpu-bind=MASK - nid005367, task  0  0 [43161]: mask 0x1fe set
    cpu-bind=MASK - nid005367, task  1  1 [43162]: mask 0x1fe000000000000000000 set
    cpu-bind=MASK - nid005367, task  2  2 [43163]: mask 0x1fe000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task  3  3 [43164]: mask 0x1fe000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task  4  4 [43165]: mask 0x1fe00 set
    cpu-bind=MASK - nid005367, task  5  5 [43166]: mask 0x1fe00000000000000000000 set
    cpu-bind=MASK - nid005367, task  6  6 [43167]: mask 0x1fe00000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task  7  7 [43168]: mask 0x1fe00000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task  8  8 [43169]: mask 0x1fe0000 set
    cpu-bind=MASK - nid005367, task  9  9 [43170]: mask 0x1fe0000000000000000000000 set
    cpu-bind=MASK - nid005367, task 10 10 [43171]: mask 0x1fe0000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 11 11 [43172]: mask 0x1fe0000000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 12 12 [43173]: mask 0x1fe000000 set
    cpu-bind=MASK - nid005367, task 13 13 [43174]: mask 0x1fe000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 14 14 [43175]: mask 0x1fe000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 15 15 [43176]: mask 0x1fe000000000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 16 16 [43177]: mask 0x1fe00000000 set
    cpu-bind=MASK - nid005367, task 17 17 [43178]: mask 0x1fe00000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 18 18 [43179]: mask 0x1fe00000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 19 19 [43180]: mask 0x1fe00000000000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 20 20 [43181]: mask 0x1fe0000000000 set
    cpu-bind=MASK - nid005367, task 21 21 [43182]: mask 0x1fe0000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 22 22 [43183]: mask 0x1fe0000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 23 23 [43184]: mask 0x1fe0000000000000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 24 24 [43185]: mask 0x1fe000000000000 set
    cpu-bind=MASK - nid005367, task 25 25 [43186]: mask 0x1fe000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 26 26 [43187]: mask 0x1fe000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 27 27 [43188]: mask 0x1fe000000000000000000000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 28 28 [43189]: mask 0x1fe00000000000000 set
    cpu-bind=MASK - nid005367, task 29 29 [43190]: mask 0x1fe00000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 30 30 [43191]: mask 0x1fe00000000000000000000000000000000000000000000000000 set
    cpu-bind=MASK - nid005367, task 31 31 [43192]: mask 0x1fe00000000000000000000000000000000000000000000000000000000000000000000 set
    ```

### OpenMP

```bash
export OMP_DISPLAY_AFFINITY=TRUE
```

??? example "Output"

    ```
    level 1 thread 0x40000a7e9900 affinity 249-256
    level 1 thread 0x400013adc900 affinity 249-256
    level 1 thread 0x400013cec900 affinity 249-256
    level 1 thread 0x400013efc900 affinity 249-256
    level 1 thread 0x40001410c900 affinity 249-256
    level 1 thread 0x40001431c900 affinity 249-256
    level 1 thread 0x40001452c900 affinity 249-256
    level 1 thread 0x4000426a9900 affinity 185-192
    level 1 thread 0x40004b99c900 affinity 185-192
    level 1 thread 0x40004bbac900 affinity 185-192
    level 1 thread 0x40004bdbc900 affinity 185-192
    level 1 thread 0x40004bfcc900 affinity 185-192
    level 1 thread 0x40004c1dc900 affinity 185-192
    level 1 thread 0x40004c3ec900 affinity 185-192
    level 1 thread 0x400020279900 affinity 265-272
    level 1 thread 0x40002956c900 affinity 265-272
    level 1 thread 0x40002977c900 affinity 265-272
    level 1 thread 0x40002998c900 affinity 265-272
    level 1 thread 0x400029b9c900 affinity 265-272
    level 1 thread 0x400029dac900 affinity 265-272
    level 1 thread 0x400029fbc900 affinity 265-272
    level 1 thread 0x400038c39900 affinity 225-232
    level 1 thread 0x400041f2c900 affinity 225-232
    level 1 thread 0x40004213c900 affinity 225-232
    level 1 thread 0x40004234c900 affinity 225-232
    level 1 thread 0x40004255c900 affinity 225-232
    level 1 thread 0x40004276c900 affinity 225-232
    level 1 thread 0x40004297c900 affinity 225-232
    level 1 thread 0x4000096a9900 affinity 233-240
    level 1 thread 0x40001299c900 affinity 233-240
    level 1 thread 0x400012bac900 affinity 233-240
    level 1 thread 0x400012dbc900 affinity 233-240
    level 1 thread 0x400012fcc900 affinity 233-240
    level 1 thread 0x4000131dc900 affinity 233-240
    level 1 thread 0x4000133ec900 affinity 233-240
    level 1 thread 0x40001da09900 affinity 129-136
    level 1 thread 0x400026cfc900 affinity 129-136
    level 1 thread 0x400026f0c900 affinity 129-136
    level 1 thread 0x40002711c900 affinity 129-136
    level 1 thread 0x40002732c900 affinity 129-136
    level 1 thread 0x40002753c900 affinity 129-136
    level 1 thread 0x40002774c900 affinity 129-136
    level 1 thread 0x400009619900 affinity 217-224
    level 1 thread 0x40001290c900 affinity 217-224
    level 1 thread 0x400012b1c900 affinity 217-224
    level 1 thread 0x400012d2c900 affinity 217-224
    level 1 thread 0x400012f3c900 affinity 217-224
    level 1 thread 0x40001314c900 affinity 217-224
    level 1 thread 0x40001335c900 affinity 217-224
    level 1 thread 0x400044149900 affinity 257-264
    level 1 thread 0x40004d43c900 affinity 257-264
    level 1 thread 0x40004d64c900 affinity 257-264
    level 1 thread 0x40004d85c900 affinity 257-264
    level 1 thread 0x40004da6c900 affinity 257-264
    level 1 thread 0x40004dc7c900 affinity 257-264
    level 1 thread 0x40004de8c900 affinity 257-264
    level 1 thread 0x4000406a9900 affinity 89-96
    level 1 thread 0x40004999c900 affinity 89-96
    level 1 thread 0x400049bac900 affinity 89-96
    level 1 thread 0x400049dbc900 affinity 89-96
    level 1 thread 0x400049fcc900 affinity 89-96
    level 1 thread 0x40004a1dc900 affinity 89-96
    level 1 thread 0x40004a3ec900 affinity 89-96
    level 1 thread 0x40000a7f9900 affinity 273-280
    level 1 thread 0x400013aec900 affinity 273-280
    level 1 thread 0x400013cfc900 affinity 273-280
    level 1 thread 0x400013f0c900 affinity 273-280
    level 1 thread 0x40001411c900 affinity 273-280
    level 1 thread 0x40001432c900 affinity 273-280
    level 1 thread 0x40001453c900 affinity 273-280
    level 1 thread 0x4000256a9900 affinity 49-56
    level 1 thread 0x40002e99c900 affinity 49-56
    level 1 thread 0x40002ebac900 affinity 49-56
    level 1 thread 0x40002edbc900 affinity 49-56
    level 1 thread 0x40002efcc900 affinity 49-56
    level 1 thread 0x40002f1dc900 affinity 49-56
    level 1 thread 0x40002f3ec900 affinity 49-56
    level 1 thread 0x4000301b9900 affinity 161-168
    level 1 thread 0x4000394ac900 affinity 161-168
    level 1 thread 0x4000396bc900 affinity 161-168
    level 1 thread 0x4000398cc900 affinity 161-168
    level 1 thread 0x400039adc900 affinity 161-168
    level 1 thread 0x400039cec900 affinity 161-168
    level 1 thread 0x400039efc900 affinity 161-168
    level 1 thread 0x4000137c9900 affinity 33-40
    level 1 thread 0x40001cabc900 affinity 33-40
    level 1 thread 0x40001cccc900 affinity 33-40
    level 1 thread 0x40001cedc900 affinity 33-40
    level 1 thread 0x40001d0ec900 affinity 33-40
    level 1 thread 0x40001d2fc900 affinity 33-40
    level 1 thread 0x40001d50c900 affinity 33-40
    level 1 thread 0x40003d509900 affinity 121-128
    level 1 thread 0x4000467fc900 affinity 121-128
    level 1 thread 0x400046a0c900 affinity 121-128
    level 1 thread 0x400046c1c900 affinity 121-128
    level 1 thread 0x400046e2c900 affinity 121-128
    level 1 thread 0x40004703c900 affinity 121-128
    level 1 thread 0x40004724c900 affinity 121-128
    level 1 thread 0x400037349900 affinity 9-16
    level 1 thread 0x40004063c900 affinity 9-16
    level 1 thread 0x40004084c900 affinity 9-16
    level 1 thread 0x400040a5c900 affinity 9-16
    level 1 thread 0x400040c6c900 affinity 9-16
    level 1 thread 0x400040e7c900 affinity 9-16
    level 1 thread 0x40004108c900 affinity 9-16
    level 1 thread 0x40001bdf9900 affinity 17-24
    level 1 thread 0x4000250ec900 affinity 17-24
    level 1 thread 0x4000252fc900 affinity 17-24
    level 1 thread 0x40002550c900 affinity 17-24
    level 1 thread 0x40002571c900 affinity 17-24
    level 1 thread 0x40002592c900 affinity 17-24
    level 1 thread 0x400025b3c900 affinity 17-24
    level 1 thread 0x4000409a9900 affinity 113-120
    level 1 thread 0x400049c9c900 affinity 113-120
    level 1 thread 0x400049eac900 affinity 113-120
    level 1 thread 0x40004a0bc900 affinity 113-120
    level 1 thread 0x40004a2cc900 affinity 113-120
    level 1 thread 0x40004a4dc900 affinity 113-120
    level 1 thread 0x40004a6ec900 affinity 113-120
    level 1 thread 0x40001d349900 affinity 73-80
    level 1 thread 0x40002663c900 affinity 73-80
    level 1 thread 0x40002684c900 affinity 73-80
    level 1 thread 0x400026a5c900 affinity 73-80
    level 1 thread 0x400026c6c900 affinity 73-80
    level 1 thread 0x400026e7c900 affinity 73-80
    level 1 thread 0x40002708c900 affinity 73-80
    level 1 thread 0x4000298a9900 affinity 145-152
    level 1 thread 0x400032b9c900 affinity 145-152
    level 1 thread 0x400032dac900 affinity 145-152
    level 1 thread 0x400032fbc900 affinity 145-152
    level 1 thread 0x4000331cc900 affinity 145-152
    level 1 thread 0x4000333dc900 affinity 145-152
    level 1 thread 0x4000335ec900 affinity 145-152
    level 1 thread 0x400005c79900 affinity 169-176
    level 1 thread 0x40000ef6c900 affinity 169-176
    level 1 thread 0x40000f17c900 affinity 169-176
    level 1 thread 0x40000f38c900 affinity 169-176
    level 1 thread 0x40000f59c900 affinity 169-176
    level 1 thread 0x40000f7ac900 affinity 169-176
    level 1 thread 0x40000f9bc900 affinity 169-176
    level 1 thread 0x40003a6b9900 affinity 105-112
    level 1 thread 0x4000439ac900 affinity 105-112
    level 1 thread 0x400043bbc900 affinity 105-112
    level 1 thread 0x400043dcc900 affinity 105-112
    level 1 thread 0x400043fdc900 affinity 105-112
    level 1 thread 0x4000441ec900 affinity 105-112
    level 1 thread 0x4000443fc900 affinity 105-112
    level 1 thread 0x400040ce9900 affinity 41-48
    level 1 thread 0x400049fdc900 affinity 41-48
    level 1 thread 0x40004a1ec900 affinity 41-48
    level 1 thread 0x40004a3fc900 affinity 41-48
    level 1 thread 0x40004a60c900 affinity 41-48
    level 1 thread 0x40004a81c900 affinity 41-48
    level 1 thread 0x40004aa2c900 affinity 41-48
    level 1 thread 0x40000bcc9900 affinity 193-200
    level 1 thread 0x400014fbc900 affinity 193-200
    level 1 thread 0x4000151cc900 affinity 193-200
    level 1 thread 0x4000153dc900 affinity 193-200
    level 1 thread 0x4000155ec900 affinity 193-200
    level 1 thread 0x4000157fc900 affinity 193-200
    level 1 thread 0x400015a0c900 affinity 193-200
    level 1 thread 0x40002fb39900 affinity 81-88
    level 1 thread 0x400038e2c900 affinity 81-88
    level 1 thread 0x40003903c900 affinity 81-88
    level 1 thread 0x40003924c900 affinity 81-88
    level 1 thread 0x40003945c900 affinity 81-88
    level 1 thread 0x40003966c900 affinity 81-88
    level 1 thread 0x40003987c900 affinity 81-88
    level 1 thread 0x40000e809900 affinity 153-160
    level 1 thread 0x400017afc900 affinity 153-160
    level 1 thread 0x400017d0c900 affinity 153-160
    level 1 thread 0x400017f1c900 affinity 153-160
    level 1 thread 0x40001812c900 affinity 153-160
    level 1 thread 0x40001833c900 affinity 153-160
    level 1 thread 0x40001854c900 affinity 153-160
    level 1 thread 0x400012829900 affinity 25-32
    level 1 thread 0x40001bb1c900 affinity 25-32
    level 1 thread 0x40001bd2c900 affinity 25-32
    level 1 thread 0x40001bf3c900 affinity 25-32
    level 1 thread 0x40001c14c900 affinity 25-32
    level 1 thread 0x40001c35c900 affinity 25-32
    level 1 thread 0x40001c56c900 affinity 25-32
    level 1 thread 0x400041a19900 affinity 97-104
    level 1 thread 0x40004ad0c900 affinity 97-104
    level 1 thread 0x40004af1c900 affinity 97-104
    level 1 thread 0x40004b12c900 affinity 97-104
    level 1 thread 0x40004b33c900 affinity 97-104
    level 1 thread 0x40004b54c900 affinity 97-104
    level 1 thread 0x40004b75c900 affinity 97-104
    level 1 thread 0x40003a3a9900 affinity 241-248
    level 1 thread 0x40004369c900 affinity 241-248
    level 1 thread 0x4000438ac900 affinity 241-248
    level 1 thread 0x400043abc900 affinity 241-248
    level 1 thread 0x400043ccc900 affinity 241-248
    level 1 thread 0x400043edc900 affinity 241-248
    level 1 thread 0x4000440ec900 affinity 241-248
    level 1 thread 0x400031339900 affinity 57-64
    level 1 thread 0x40003a62c900 affinity 57-64
    level 1 thread 0x40003a83c900 affinity 57-64
    level 1 thread 0x40003aa4c900 affinity 57-64
    level 1 thread 0x40003ac5c900 affinity 57-64
    level 1 thread 0x40003ae6c900 affinity 57-64
    level 1 thread 0x40003b07c900 affinity 57-64
    level 1 thread 0x40001a7d9900 affinity 201-208
    level 1 thread 0x400023acc900 affinity 201-208
    level 1 thread 0x400023cdc900 affinity 201-208
    level 1 thread 0x400023eec900 affinity 201-208
    level 1 thread 0x4000240fc900 affinity 201-208
    level 1 thread 0x40002430c900 affinity 201-208
    level 1 thread 0x40002451c900 affinity 201-208
    level 1 thread 0x40001a519900 affinity 177-184
    level 1 thread 0x40002380c900 affinity 177-184
    level 1 thread 0x400023a1c900 affinity 177-184
    level 1 thread 0x400023c2c900 affinity 177-184
    level 1 thread 0x400023e3c900 affinity 177-184
    level 1 thread 0x40002404c900 affinity 177-184
    level 1 thread 0x40002425c900 affinity 177-184
    level 1 thread 0x400038269900 affinity 1-8
    level 1 thread 0x40004155c900 affinity 1-8
    level 1 thread 0x40004176c900 affinity 1-8
    level 1 thread 0x40004197c900 affinity 1-8
    level 1 thread 0x400041b8c900 affinity 1-8
    level 1 thread 0x400041d9c900 affinity 1-8
    level 1 thread 0x400041fac900 affinity 1-8
    ```

The following could be a better setting for printing out the affinity:

```bash
export OMP_AFFINITY_FORMAT="host=%H, pid=%P, thread_num=%n, thread affinity=%A"
```

## Determine current CPU

```c
#include <sched.h>

int get_current_cpu() {
    return sched_getcpu();
}
```

## Resources

- [affinity](https://github.com/vkarak/affinity), from Vasilis Karakasis ([@vkarak](https://github.com/vkarak))
