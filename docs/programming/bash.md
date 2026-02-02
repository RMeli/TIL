# bash

## Trapping signals in bash scripts

In bash scripts, you can use the `trap` command to catch and handle signals.
This is useful for cleaning up resources or performing specific actions when your script receives certain signals.

!!! example "Trapping SIGTERM signal for clan shutdown"

    ```bash
    #!/bin/bash

    # Function to handle shutdown
    shutdown() {
        echo "Cleaning up before exiting..."
        exit 0
    }

    trap shitdown SIGTERM

    # Simulate a long-running process
    echo "PID: $$"
    while true; do
        echo "Running..."
        sleep 1
    done
    ```

    In this example, when the script receives a `SIGTERM` signal, it will execute the `cleanup` function before exiting.

    === "Running the script"

        ```console
        $ bash trap.sh
        PID: 37613
        Running...
        Running...
        Running...
        Cleaning up before exiting...
        ```

    === "Killing the script"

        ```console
        $ kill 37613
        ```

## Print the current script

It is often useful to print the content of a script before the output of the script itself,
for debugging or logging purposes.

This can be done by using the special variable `$0`, which contains the name of the script being executed.

!!! example "Print current bash script"

    ```console
    $ bash test.sh 
    #!/bin/bash

    cat $0

    echo "Hello"
    Hello
    ```
