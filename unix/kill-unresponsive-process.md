# Kill Unresponsive Process

Unresponsive processes/services ignore the `kill` command. In order to shut down the process immediately use

```bash
kill -9 PID
```

where `PID` is the process ID.

This is equivalent to

```bash
kill -SIGKILL PID
```

The `kill -SIGKILL` command bypasses the standard shutdown routine and unsaved data will be lost.
