### Understand and use essential tools

1. Programmable completion for bash is provided in the bash-completion module. To install this module:
    ```shell
    sudo dnf install bash-completion
    ```

1. Access a shell prompt and issue commands with correct syntax

    * Common commands and their options, as well as vim usage, are shown below:
        | Command        | Options                                                                                                                                                          | Description                                     |
        |----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------|
        | ls             | -h (human readable) <br>  -a (show hidden) <br> -l (detailed) <br> -lt (newist file first) <br> -ltr (oldest file first)                                         | List of files and directories                   |
        | pwd            |                                                                                                                                                                  | Print working directory                         |
        | cd             | ~ (home) <br> / (root) <br> - (switch) <br> .. (parent)                                                                                                          | Change directories                              |
        | who            | whoami (show user)                                                                                                                                               | Show logged in users                            |
        | what           | w (shorthand)                                                                                                                                                    | Show logged in users with more detail           |
        | uptime         |                                                                                                                                                                  | Show system uptime                              |
        | logname        |                                                                                                                                                                  | Show real username (if using su)                |
        | id             |                                                                                                                                                                  | Shows a user's UID, username, GUID etc.         |
        | groups         |                                                                                                                                                                  | Lists groups for users                          |
        | last           |                                                                                                                                                                  | List all user logins and system reboots         |
        | lastb          |                                                                                                                                                                  | List all failed login attempts                  |
        | lastlog        |                                                                                                                                                                  | List recent logins                              |
        | uname          | -a (details)                                                                                                                                                     | System information                              |
        | hostnamectl    | set-hostname                                                                                                                                                     | View hostname                                   |
        | clear          |                                                                                                                                                                  | Clear the screen                                |
        | timedatectl    | set-time <br> list-timezones <br> set-timezone <br>                                                                                                              | Display system time                             |
        | date           | --set                                                                                                                                                            | View system date                                |
        | which          |                                                                                                                                                                  | Show path to a command                          |
        | wc             |                                                                                                                                                                  | Word count                                      |
        | lspci          | -m (legible)                                                                                                                                                     | PCI buses details                               |
        | lsusb          |                                                                                                                                                                  | USB buses details                               |
        | lscpu          |                                                                                                                                                                  | Processor details                               |
        | gzip/bzip2     | -d (uncompress)                                                                                                                                                  | Compress files                                  |
        | gunzip/bunzip2 |                                                                                                                                                                  | Uncompress files                                |
        | tar            | -c (create) <br> -f (specifies name) <br> -v (verbose) <br> -r (append to existing) <br> -x (extract) <br> -z (compress with gzip) <br> -j (compress with bzip2) | Archive file                                    |
        | star           |                                                                                                                                                                  | Enhanced tar                                    |
        | man            | -k (keyword) <br> -f (short description)                                                                                                                         | Manual                                          |
        | mandb          |                                                                                                                                                                  | Update the mandb                                |
        | ssh            | -l (as different user)                                                                                                                                           | SSH to another Linux system                     |
        | tty            |                                                                                                                                                                  | Display terminal name                           |
        | whatis         |                                                                                                                                                                  | Search the command in the mandb for description |
        | info           |                                                                                                                                                                  | More detailed than man                          |
        | apropos        |                                                                                                                                                                  | Search the command in the mandb                 |
        | grep           | -n (show line numbers) <br> -v (pattern exclusion) <br> -i (case insensitive) <br> -E (use alternation) <br> -w (word match)                                     | Find text                                       |
        
        | Key                      | Description                     |
        |--------------------------|---------------------------------|
        | i                        | Change to insert mode           |
        | h, j, k, l               | Move left, down, up, right      |
        | w, b, e, ge              | Move word at a time             |
        | n[action]                | Do n times                      |
        | x                        | Remove a character              |
        | a                        | Append                          |
        | f[char]                  | Move to next given char in line |
        | F[char]                  | Move to previous char in line   |
        | ; and ,                  | Repeat last f or F              |
        | /yourtext and then: n, N | Search text                     |
        | d[movement]              | Delete by giving movement       |
        | r[char]                  | Replaces character below cursor |
        | 0, $                     | Move to start/end of line       |
        | o, O                     | Add new line                    |
        | %                        | Goto corresponding parentheses  |
        | ci[movement]             | Change inside of given movement |
        | D                        | Delete to end of line           |
        | S                        | Clear current line              |
        | gg / G                   | Move to start / end of buffer   |
        | yy                       | Copy current line               |
        | p                        | Paste copied text after cursor  |
    
1. Use input-output redirection (>, >>, |, 2>, etc.)
    * The default locations for input, output, and error are referred to as standard input (stdin), standard output (stdout), and standard error (stderr).
    
    * Standard input redirection can be done to have a command read the required information from an alternative source, such as a file, instead of the keyboard. For example:
        ```shell
        cat < /etc/cron.allow 
        ```

    * Standard output redirection sends the output generated by a command to an alternative destination, such as a file. For example:
        ```shell
        ll > ll.out
        ```

    * Standard error redirection sends the output generated by a command to an alternative destination, such as a file. For example: 
        ```shell
        echo test 2> outerr.out
        ```

    * Instead of > to create or overwrite, >> can be used to append to a file.

    * To redirect both stdout and stderror to a file:
        ```shell
        echo test >> result.txt 2>&1
        ```

1. Use grep and regular expressions to analyse text
    * The grep command is used to find text. For example:
        ```shell
        grep user100 /etc/passwd
        ```    
    * Common regular expression parameters are shown below:
        | Symbol | Description                                                        |
        |--------|--------------------------------------------------------------------|
        | ^      | Beginning of a line or word                                        |
        | $      | End of a line or word                                              |
        | \|     | Or                                                                 |
        | .      | Any character                                                      |
        | *      | Any number of any character                                        |
        | ?      | Exactly one character                                              |
        | []     | Range of characters                                                |
        | \      | Escape character                                                   |
        | ''     | Mask meaning of enclosed special characters                        |
        | ""     | Mask meaning of all enclosed special characters except \, $ and '' |
    
1. Access remote systems using SSH

    * Secure Shell (SSH) provides a secure mechanism for data transmission between source and destination systems over IP networks.
    
    * SSH uses encryption and performs data integrity checks on transmitted data.
    
    * The version of SSH used is defined in `/etc/ssh/sshd_config`.
    
    * The most common authentication methods are Password-Based Authentication and Public/Private Key-Based Authentication.
    
    * The command *ssh-keygen* is used to generate keys and place them in the .ssh directory, and the command *ssh-copy-id* is used to copy the public key file to your account on the remote server.
    
    * TCP Wrappers is a host-based mechanism that is used to limit access to wrappers-aware TCP services on the system by inbound clients. 2 files `/etc/hosts.allow` and `/etc/hosts.deny` are used to control access. The .allow file is referenced before the .deny file. The format of the files is \<name of service process>:\<user@source>.
    
    * All messages related to TCP Wrappers are logged to the `/var/log/secure` file.
    
    * To login using SSH: 
        ```shell
        ssh user@host
        ``` 

1. Log in and switch users in multiuser targets

    * A user can switch to another user using the *su* command. The *-i* option ensures that the target users login scripts are run:
        ```shell
        sudo -i -u targetUser
        ``` 

    * To run a command as root without switching:
        ```shell
        sudo -c
        ``` 

    * The configuration for which users can run which commands using sudo is defined in the `/etc/sudoers` file. The visudo command is used to edit the sudoers file. The sudo command logs successful authentication and command data to `/var/log/secure`.

1. Archive, compress, unpack, and decompress files using tar, star, gzip, and bzip2

    * To archive using tar:
        ```shell
        tar cvf myTar.tar /home
        ``` 

    * To unpack using tar:
        ```shell
        tar xvf myTar.tar
        ``` 

    * To compress using tar and gzip:
        ```shell
        tar cvfz myTar.tar /home
        ``` 

    * To compress using tar and bzip2:
        ```shell
        tar cvfj myTar.tar /home
        ``` 

    * To decompress using tar and gzip:
        ```shell
        tar xvfz myTar.tar /home
        ``` 

    * To decompress using tar and bzip2:
        ```shell
        tar xvfj myTar.tar /home
        ``` 

    * The star command is an enhanced version of tar. It also supports SELinux security contexts and extended file attributes. The options are like tar.


1. Create and edit text files

    * To create an empty file:
        ```shell
        touch file
        cat > newfile
        ``` 

    * To create a file using vim:
        ```shell
        vi file
        ``` 

1. Create, delete, copy, and move files and directories

    * To create a directory:
        ```shell
        mkdir directory
        ``` 

    * To move a file or directory:
        ```shell
        mv item1 item2
        ```     

    * To copy a file or directory:
        ```shell
        cp item1 item2
        ```     

    * To remove a file:
        ```shell
        rm file1
        ```

    * To remove an empty directory:
        ```shell
        rmdir directory
        ```

    * To remove a non-empty directory:
        ```shell
        rm -r directory
        ```

1. Create hard and soft links

    * A soft link associates one file with another. If the original file is removed the soft link will point to nothing. To create a soft link to file1:
        ```shell
        ln -s file1 softlink
        ``` 

    * A hard link associates multiple files to the same inode making them indistinguishable. If the original file is removed, you will still have access to the data through the linked file. To create a hard link to file1:
        ```shell
        ln file1 hardlink
        ``` 

1. List, set, and change standard ugo/rwx permissions

    * Permissions are set for the user, group, and others. User is the owner of the file or the directory, group is a set of users with identical access defined in `/etc/group`, and others are all other users. The types of permission are read, write, and execute.
    
    * Permission combinations are shown below:
        | Octal Value | Binary Notation | Symbolic Notation | Explanation                           |
        |-------------|-----------------|-------------------|---------------------------------------|
        | 0           | 000             | ---               | No permissions.                       |
        | 1           | 001             | --x               | Execute permission only.              |
        | 2           | 010             | -w-               | Write permission only.                |
        | 3           | 011             | -wx               | Write and execute permissions.        |
        | 4           | 100             | r--               | Read permission only.                 |
        | 5           | 101             | r-x               | Read and execute permissions.         |
        | 6           | 110             | rw-               | Read and write permissions.           |
        | 7           | 111             | rwx               | Read, write, and execute permissions. |

    * To grant the owner, group, and others all permissions using the *chmod* command:
        ```shell
        chmod 777 file1
        ```

    * The default permissions are calculated based on the umask. The default umask for root is 0022 and 0002 for regular users (the leading 0 has no significance). The pre-defined initial permissions are 666 for files and 777 for directories. The umask is subtracted from these initial permissions to obtain the default permissions. To change the default umask:
        ```shell
        umask 027
        ```

    * Every file and directory has an owner. By default, the creator assumes ownership. The owner's group is assigned to a file or directory. To change the ownership of a file or directory:
        ```shell
        useradd user100
        chown user100 item1
        chgrp user100 item1
        ```
    
        ```shell
        chown user100:user100 item1
        ```
    * Note that the -R option must be used to recursively change all files in a directory.

1. Locate, read, and use system documentation including man, info, and files in `/usr/share/doc`

    * The *man* command can be used to view help for a command. To search for a command based on a keyword the *apropros* command or *man* with the -k option can be used. The *mandb* command is used to build the man database.

    * To search for a command based on a keyword in occurring in its man page:
        ```shell
        man -K <keyword>
        ```

    * The *whatis* command can be used to search for a command in the man database for a short description.

    * The *info* command provides more detailed information than the *man* command. 

    * The `/usr/share/doc` directory contains documentation for all installed packages under sub-directories that match package names followed by their version.

# Compare and manipulate file content

## Extended Regular Expressions 

Checkout out [regexr.com](https://regexr.com/) for more information.

If we want to do e.g., ``grep 'b.?t'`` we should use an **extended regular expression**.
Those types of expressions start with **egrep**, not grep.

In basic regular expressions the meta-characters ?, +, {, |, (, and ) lose their special meaning; instead use the backslashed (escaped) versions \\?, \\+, \\{, \\|, \\(, and \\). **It's easier to always use egrep and not grep**, then you don't have to escape the meta-characters.

``egrep -r "0{3,}" /etc/``

This gives us anything matching at least three zeros in "/etc/".
If we wanted to use a regular expression we would have to escape the {} characters.

``grep -r "0\{3,\}" /etc/``

To search files in "/etc/" for the word "disabled". The ? character means that the "d" in disabled can exist once or zero times. So it would return words like, "disabled" or "disable".

``egrep -r "disabled?" /etc/

This searches "/etc/" for either disabled or enabled.

``egrep -r "enabled|disabled" /etc/``

Search in the "/dev/" directory for any file that begins with a-z, and has another character zero or more times, has a number from 0-9, and ends with any character

``egrep -r "/dev/[a-z]\*[0-9]?" /etc/``

## Regular Expressions

Regular Expressions are text patterns that are used by tools like grep and others.

*Don't confuse regular expressions with globbing!* They may look like expressions with globbing, but are really different.

``grep 'a*' or a*`` 

a* is globbing, 'a*' is a Regular Expression.  

Regular expressions are used with specific tools only like grep, vim, awk and sed. **Extended** regular expressions enhance basic regex features. See man 7 regex for details.

Regular expressions are built around **atoms**; an atom specifies what text is to be matched. Atoms can be single characters, a range of characters, or a dot. 

Atoms can also be a class, such as \[[:alpha:\]], \[[:digit:\]] or \[[:alnum:\]]. 

Second is the repetition operator, specifying how often a character occurs. The third element is indicating where to find the next character.

This searches the regtext file for anything beginning with b and ends with t. The dot is a wildcard charachter.

``grep 'b.t' regtext``  

If we do ``grep 'b.\*t' sometextfile``. The * functions as a repitition operator.

We use \\ to escape special characters so that Bash doesn't interpret them.
Let's say we want to find all periods in "/etc/login.defs". If we did ``grep "." /etc/login.defs`` grep would return the whole document since the period operator functions as "match any one character" as we can see below. We would fix this by escaping the period.

If we do this, only the periods in the document are highlighted red. ``grep "\." /etc/login.defs`` 

**Repetition**

Here are some operators we can use for repition.

A regular expression may be followed by one of several repetition operators:

- ? The preceding item is optional and matched at most once.
- \* The preceding item will be matched zero or more times.
- \+ The preceding item will be matched one or more times.
- {n} The preceding item is matched exactly n times.
- {n,} The preceding item is matched n or more times.
- {,m} The preceding item is matched at most m times. This is a GNU extension.
- {n,m} The preceding item is matched at least n times, but not more than m times.

**Other operators**

-   ^ beginning of the line.  
-   $ end of line. 
-   \< beginning of word. 
-   \> end of word.  
-   .  match any ONE character.
-   | is an or operator.

## Grep options

|   |   |
|---|---|
|-i   | Not case sensitive. Matches upper- and lowercase letters.  |
|-v   | Shows only lines that do not contain the regular expression.   |
|-r   | Searches files in the current directory and all subdirectories.    |
| -e | Searches for lines matching more than one regular expression. |
| -A \<number> | Shows \<number> of lines after the matching regular expression. |  
| -B \<number> | Shows \<number> of lines before the matching regular expression. |

### Examples

``grep '\<root\>' * 2>/dev/null``

Show us all files in the working directory that have only three characters in their name.  

``grep '^...$' *``   

Count how many numbers begin with 2 in "textfile".

``grep -c '^2' textfile``


## CUT and SORT

### Examples

#### CUT

Pipe the output of the third field in "/etc/passwd" to less. The delimeter between fields is ":"".

``cut -f 3 -d : /etc/passwd | less``

Here we use space as the delimiter.

``cut -d ' ' -f 1 userinfo.txt``

This command cuts the first field and changes everything in lowercase to uppercase.

``cut -d : -f 1 /etc/passwd | tr [:lower:] [:upper:]`` 

#### SORT

Cut out the first field in the "passwd" file and sort it alphabetically.

``cut -f 1 -d : /etc/passwd | sort``

How to remove duplicate lines when using sort.

``sort oscarwinners.txt | uniq``


## AWK

In AWK, the logic is: pattern { action }.

``awk 'NR==1 {print $1}' text.txt``
Prints out the first field of the first line in text.txt.

``awk -F : '/armann/ { print $4 }' /etc/passwd``
Searches for lines with /armann/ and prints out field number 4.

``awk '{gsub("Armann Jakob Palsson", "The Armanator"); print}' text.txt > tmp.txt && mv tmp.txt``
Changes my name, Armann Jakob Palsson to The Armanator.

``ps aux | awk '{print $NF }'``
Prints out the last field in each line outputted by ps aux. 

## SED

Switch out four for FOUR globally in the file "sedfile".

``sed -i s/four/FOUR/g sedfile``

Shows the fith line in passwd.

``sed -n 5p /etc/passwd/``

Deletes line 2, 20 and 25 from myfile.

``sed -i -e '2d;20,25d' ~/myfile``

Change enabled to disabled for lines from 500 to 2000.

``sed -i "-e 500,2000 s/enabled/disabled/g" /home/bob/values.conf``

Change disabled to enabled globally and ignore the case for disabled with the "i" operator. Will switch out "disabled", "Disabled", and "DISABLED" to "enabled."

``sed -i "s/disabled/enabled/gi" /home/bob/values.conf``

Replace all occurrence of string #%$2jh//238720//31223 with $2//23872031223 in "/home/bob/data.txt" file.

``sed -i 's~#%$2jh//238720//31223~$2//23872031223~g' /home/bob/data.txt``

## Other commands

### DIFF
To see the differences between two files.

``diff file1 file2``

To see context around lines that differ.

``diff -c file1 file2``

Easiest way is to use a side-by-side comparison of two files. You can use ``diff -y`` or ``sdiff`` both accomplish the same thing.

``diff -y file1 file2``

### GREP

Search a directory for "centos". -i stands for case insensitive. -r is recursive.

``grep -ir "centos" /etc/``

To match only whole words not parts of words use the -w option.

``grep -iw "red" /etc/`` This would not output anything that says "redhat". Only "red" and it also ignores the case because of the -i option.

# Find / Search

How to search.

find \[/path/to/directory] \[search_parameters]
If you don't specify a directory, it searches the working directory.

## General commands

### Or operator
``find -name stallone -o -name brucewillis``

Use -o as an "or" operator to find either stallone or brucewillis.

## Search for by permissions

Find files with exactly 664 permissions.
``find -perm 664``

Find files with at least 664 permissions.
``find -perm -664``

Find files where the "owner" has at least execute permissions.
``find -perm -100``

Find files where "others" don't have read permission.
``find \! -perm -o=r``

Find files where at least "owner", "group" and "others" had read permissions.
``find -perm /u=r,g=r,o=r``

Find files with any of these permissions.
``find -perm /664``

Find files where the "group" has at least write permission and "others" don't have read and write access.

``sudo find /var/log/ -perm -g=w ! -perm /o=rw``

## By size 

The following options exist for size.
- Bytes = c
- Kilobytes = k
- Megabytes = M
- Gigabytes = G

Find files that are larger than 100M.
``find / -type f -size +100M``

Find tiles that are smaller than 1000 bytes.
``find /etc/ -type f -size -1000c`` 

## Folder or file 

Find a directory named "Music" in your home directory.
``find ~ -type d -name "Music"``

This searches everything for a file named "linux.conf".
``find / -name linux.conf``

Find both "bear" and "Bear".
``find -iname bear``
   
## Copy found items

Find all files under "/etc" named hosts and copy them to "/tmp".
``find /etc/ -name "hosts" -exec cp {} /tmp \;``

## By date and time

### Find modified within a timeframe
Find all files that have been modified in the last five minutes in the "dev" directory. Think about "mm" as "modified minute". The latter command finds everything that was modified **more** than five minutes ago.

``find /dev/ -mmin -5``

``find /dev/ -mmin +5``

To search for modified files in 24 hour blocks. This command finds all files modified in my "Documents" directory in the last 24 hours. 0 stands for the last 24 hours. 1 stands for between 24-48 hours and so on.

``find Documents/ -mtime 0``

## By change time - metadata e.g., permissions

Modification time reflects when something is created or edited.
**Change time is not modified time.** 

Change time reflects changed Metadata. For example if someone changes the permission. We can find that information with the following command. This finds all changed metadata within the last 5 minutes in the Documents directory.

``find Documents/ -cmin -5``

## Locate/updatedb

Install the `mlocate` package to use the `locate` and `updatedb` commands.

`locate `
```shell
dnf install -y mlocate
updatedb
locate sshd_config
#outputs the following
/etc/ssh/sshd_config
/etc/ssh/sshd_config.d
/etc/ssh/sshd_config.d/01-permitrootlogin.conf
/etc/ssh/sshd_config.d/50-redhat.conf
/usr/share/man/man5/sshd_config.5.gz
```

# Man Pages

``apropos -s 1,8 cp``
This command searches sections 1 and 8 for the cp command.

You can also find information like this.  ``man 5 passwd``
This displays only section 5 for passwd. 

### Man page sections

1. Section # 1 : User command (executable programs or shell commands)
2. Section # 2 : System calls (functions provided by the kernel)
3. Section # 3 : Library calls (functions within program libraries)
4. Section # 4 : Special files (usually found in /dev)
5. Section # 5 : File formats and conventions eg /etc/passwd
6. Section # 6 : Games
7. Section # 7 : Miscellaneous (including macro packages and conventions)
8. Section # 8 : System administration commands (usually only for root)
9. Section # 9 : Kernel routines [Non standard]

## PInfo 

``pinfo '(coreutils) ls invocation'``

## Other 

A third source of information consists of files that are sometimes copied to the /usr/share/doc directory.

``whatis {command}``
Searches regularly updated documentation database

``info {command}``
Additional or recent information about a command

``{command} --help``
Quick summary of command usage and other resources


