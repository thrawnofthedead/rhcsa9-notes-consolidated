### Create simple shell scripts

1. Conditionally execute code (use of: if, test, [], etc.)

    * An example using if and test statements is shown below:
        ```shell
        #!/bin/bash
        ping -c 1 $1
        if test "$?" -eq "0"; then
            echo "$1 IP is reachable"
        else
            echo "$1 IP is not reachable"
        fi
        exit
        ```

    * Input arguments can be passed in after the script name, with e.g. 1 being the first input argument. The *$?* term expands the exit status of the most recently executed command. When using *echo* the *-e* argument can be used to print characters such as new lines.

    * An example using a case statement is shown below:
        ```shell
        #!/bin/bash
        now=$(date + "%a")
        case $now in
            Mon)
                echo "Full Backup";
            ;;
            Tue|Wed|Thu|Fri)
                echo "Partial Backup";
            ;;
            Sat|Sun)
                echo "No Backup";
            ;;
            *)    ;;
        esac
        exit
        ```

    * An example using [] is shown below:
        ```shell
        #!/bin/bash
        ping -c 1 $1
        if ["$?" -eq "0"]; then
            echo "$1 IP is reachable"
        else
            echo "$1 IP is not reachable"
        fi
        exit
        ```

1. Use Looping constructs (for, etc.) to process file, command line input

    * An example of a for loop is shown below:
        ```shell
        #!/bin/bash
        for file in ./*.log
        do
            mv "${file}" "${file}".txt
        done
        exit
        ```

    * An example of a while loop is shown below:
        ```shell
        #!/bin/bash
        input = "/home/kafka.log"
        while IFS = read -r line
        do
            echo "$line"
        done < "$input"
        exit
        ```

1. Process script inputs ($1, $2, etc.)

    * The first variable passed into a script can be accessed with *$1*.

1. Processing output of shell commands within a script

    * An example is shown below:
        ```shell
        #!/bin/bash
        echo "Hello World!" >> example-`date +%Y%m%d-%H%M`.log
        exit
        ```

1. Processing shell command exit codes

    * The *$?* term expands the exit status of the most recently executed command.

# Bash Scripting

Check out [Bash Guide For Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/).

Every bash script is an executable file that runs tasks. Most often shell scripts
are used to automate routine tasks. 

Every shell script starts with the Shebang ``#!`` \
If you are using the Bash shell the Shebang would be ``#!/bin/bash`` \
Now "/bin" is no longer an ordinary directory. It is therefore recommended
to use the following Shebang since it sources your environment and will point you
to the correct Bash shell.

**#!/usr/bin/env bash**

It's a good practice but not mandatory to use the **.sh** extension for shell script files.

## Helpful Tools

### Test command
The test command is useful since it can test the properties of files, values of integers,
properties of files, etc. Test can be used for if, then, else statements.

Here is a simple bash script using the test command.

````
#!/bin/bash

if test $1 -eq 500 
then
        echo "You picked the right number!"
else
        echo "You failed!"
fi
````
You can also put the if statement into brackets. The square brackets are the same as the test command.
````
#!/bin/bash

if [ $1 -eq 500 ]
then
        echo "You picked the right number!"
else
        echo "You failed!"
fi
````
### Script Debug Mode

``bash -x mygreatscript.sh`` Shows you the script in debug mode.
