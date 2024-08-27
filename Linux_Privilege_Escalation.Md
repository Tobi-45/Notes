# Enumeration

1. The `hostname` command will return the hostname of the target machine. Sometimes useful as it can show the devices role on the network

2. `uname -a` Will print system information giving us additional detail about the kernel used by the system. helpful to find potential kernel vulnerabilities for priv. esc.

3. `proc/version` The proc filesystem (procfs) provides information about the target system processes. This may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed

4. `etc/issue` This file usually contains some information about the operating system but can easily be customized or changed. 

5. `ps` (Process status) command
   Typing `ps` on your terminal will show processes for the current shell.

Output :
    *PID*: The process ID (unique to the process)
    *TTY*: Terminal type used by the user
    *Time*: Amount of CPU time used by the process (this is NOT the time this process has been running for)
    *CMD*: The command or executable running (will NOT display any command line parameter)

`ps -A`: View all running processes

`ps axjf`: View process tree (see the tree formation until ps axjf is run below)

`ps aux`: view processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x)

6. `env` command will show environmental variables.
       The PATH variable may have a compiler or a scripting language (e.g. Python) that could be used to run code on the target system or leveraged for privilege escalation.

7. The `sudo -l` command can be used to list all commands your user can run using sudo

8. The `id` command will provide a general overview of the user’s privilege level and group memberships, `id <username>`

9. Reading the `/etc/passwd` file can be an easy way to discover users on the system
    `cat /etc/passwd | cut -d ":" -f 1` to display only users on system
    ` cat /etc/passwd | grep home` real users will most likely have their folders under the “home” directory.

10. `history` command can give us some idea about the target system and, albeit rarely, have stored information such as passwords or usernames.

11. The `ifconfig` command will give us information about the network interfaces of the system. The target system may be a pivoting point to another network
       can use `ip route` to confirm 

12. Following an initial check for existing interfaces and network routes, it is worth looking into existing communications. The `netstat` command can be used 
with several different options to gather information on existing connections. 

    `netstat -a`: shows all listening ports and established connections.

    `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.

    `netstat -l`: list ports in “listening” mode. These ports are open and ready to accept incoming connections. This can be used with the “t” option to list only ports that are listening using the TCP protocol

    `netstat -s`: list network usage statistics by protocol This can also be used with the -t or -u options to limit the output to a specific protocol. 

    `netstat -tp`: list connections with the service name and PID information. This can also be used with the `-l` option to list listening ports

    `netstat -i`: Shows interface statistics. We see below that “eth0” and “tun0” are more active than “tun1”.

     The netstat usage you will probably see most often in blog posts, write-ups, and courses is netstat `-ano` which could be broken down as follows;

    `-a`: Display all sockets
    `-n`: Do not resolve names
    `-o`: Display timers

13. `find` a build-in tool

Find files 

    `find . -name flag1.txt`: find the file named “flag1.txt” in the current directory

    `find /home -name flag1.txt`: find the file names “flag1.txt” in the /home directory

    `find / -type d -name config`: find the directory named config under “/”

    `find / -type f -perm 0777`: find files with the 777 permissions (files readable, writable, and executable by all users)

    `find / -perm a=x`: find executable files

    `find /home -user` frank: find all files for user “frank” under “/home”

    `find / -mtime 10`: find files that were modified in the last 10 days

    `find / -atime 10`: find files that were accessed in the last 10 day

    `find / -cmin -60`: find files changed within the last hour (60 minutes)

    `find / -amin -60`: find files accesses within the last hour (60 minutes)

    `find / -size 50M`: find files with a 50 MB size

This command can also be used with `(+)` and `(-)` signs to specify a file that is larger or smaller than the given size. 

the `find` command tends to generate errors which sometimes makes the output hard to read. This is why it would be wise 
to use the “find” command with `-type f 2>/dev/null` to redirect errors to `/dev/null` and have a cleaner output


Folders and files that can be written to or executed from

    `find / -writable -type d 2>/dev/null`: Find world-writeable folders

    `find / -perm -222 -type d 2>/dev/null`: Find world-writeable folders

    `find / -perm -o w -type d 2>/dev/null`: Find world-writeable folders

    `find / -perm -o x -type d 2>/dev/null` : Find world-executable folders

    Find development tools and supported languages:

    `find / -name perl*`

    `find / -name python*`

   `find / -perm -u=s -type f 2>/dev/null`: Find files with the SUID bit, which allows us to run the file with a higher privilege level than the current user.

# Priv Esc Automation Tool

1. [LinPeass](https://github.com/peass-ng/PEASS-ng/tree/master)

2. [LinEnum](https://github.com/rebootuser/LinEnum)

3. [Linux Exploit Suggester](https://github.com/The-Z-Labs/linux-exploit-suggester)

4. [LinuxPrevChecker](https://github.com/linted/linuxprivchecker)

5. [Linux Smart Enum](https://github.com/diego-treitos/linux-smart-enumeration)

#  Kernel Exploits

The Kernel exploit methodology is simple;

1. Identify the kernel version

2. Search and find an exploit code for the kernel version of the target system

3. Run the exploit












