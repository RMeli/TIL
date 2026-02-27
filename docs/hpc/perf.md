# Performance Analysis

## Flame Graphs

A [flame graph] is a visualization of hierarchical data,
created by [Brendan Gregg] to visualize stack traces of profiled software.

Scritps to generate flame graphs are available on GitHub: [brendangredd/FlameGraph].

Modify `perf_event_paranoid` setting, so that appropriate events can be recorded:
```
echo 1 | sudo tee /proc/sys/kernel/perf_event_paranoid
```

Download the scripts to generate flame graphs:
```bash
wget https://github.com/brendangregg/FlameGraph/archive/refs/heads/master.zip cat
```

The following script allows to run an application with `perf`
and generate a flame graph:

```bash
#!/bin/bash

perf record -g --call-graph fp -o perf.data $@
perf script -i perf.data > perf.script

FlameGraph-master/stackcollapse-perf.pl perf.script > perf.folded
FlameGraph-master/flamegraph.pl ./perf.folded > flamegraph.svg

rm perf.data perf.script perf.folded
```

[flame graph]: https://www.brendangregg.com/flamegraphs.html
[Brendan Gregg]: https://www.brendangregg.com/index.html
