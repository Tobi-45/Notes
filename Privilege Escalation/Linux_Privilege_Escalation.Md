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


13. `/etc/shadow` is a text-based password file. The shadow file stores the hashed passphrase (or “hash”) 
format for Linux user account with additional properties related to the user password.


14. `find` a build-in tool

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

    `find / -type f -group user's_name -ls 2>/dev/null` : Find user-writeable files

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

**Note** that a failed kernel exploit can lead to a system crash

# Exploitable Functions

1. `LD_PRELOAD` is a function that allows any program to use shared libraries

# SUID 

Much of Linux privilege controls rely on controlling the users and files interactions. This is done with permissions.
By now, you know that files can have read, write, and execute permissions. These are given to users within their privilege 
levels. This changes with **SUID** (Set-user Identification) and **SGID** (Set-group Identification). These allow files to be executed 
with the permission level of the file owner or the group owner, respectively.

You will notice these files have an `s` bit set showing their special permission level.

Eg : -rwsr-wr-rwx root bin/bash/nano

`find / -type f -perm -04000 -ls 2>/dev/null` will list files that have SUID or SGID bits set.

# Adding a new user with root priv in Linux

1. We will need the hash value of the password we want the new user to have. This can be done quickly using the `openssl` tool on Kali Linux.

`openssl passwd -1 -salt THM password1` "password1" is our password

2. We will then add this password with a username to the `/etc/passwd` file.

`hacker:$1$THM$WnbwlliCqxFRQepUTCkUT1:0:0root:/root:/bin/bash`

3. login with user & pass

`su hacker` 

# Privilege Escalation: Capabilities [Article](https://www.hackingarticles.in/linux-privilege-escalation-using-capabilities/)

- Capabilities are those permissions that divide the privileges of kernel user or kernel level programs
into small pieces so that a process can be allowed sufficient power to perform specific privileged tasks.

Suppose the system administrator wants to grant superuser permission for any binary program, let’s say for python3, 
which should only be available to a specific user, and admin doesn’t want to give SUID or sudo permission. The admin
supposed to used capabilities, for the python3 program that should be executed by specific user let’s say for user “demo”. 
This can be accomplished with following commands on the host machine.

   `which python3`

   `cp /usr/bin/python3 /home/demo/`

   `setcap cap_setuid+ep /home/demo/python3`

### Capabilities commands

We can use the `getcap` tool to list enabled capabilities.

When run as an unprivileged user, `getcap -r /` will generate a huge amount of errors, so it is good practice to redirect the error messages to `/dev/null`

`getcap -r / 2>/dev/null`

To check if the file has root permission `ls -l filename` Eg. `ls -l /usr/bin/vim`

# Corn Jobs

Cron jobs are used to run scripts or binaries at specific times. By default, they run with the privilege of their owners and not the current user. While properly configured cron jobs are not inherently vulnerable, they can provide a privilege escalation vector under some conditions.
The idea is quite simple; if there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.

Any user can read the file keeping system-wide cron jobs under `/etc/crontab`

1. The command syntax will vary depending on the available tools. (e.g. nc will probably not support the -e option you may have seen used in other cases)
2. We should always prefer to start reverse shells, as we not want to compromise the system integrity during a real penetration testing engagement.

If the full path of the script is not defined, cron will refer to the paths listed under the PATH variable in the `/etc/crontab` file.

# Priv Esc $PATH [artical](https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/)

If a folder for which your user has write permission is located in the path, you could potentially hijack an application to run a script. PATH in Linux is an **environmental variable** that tells the operating system where to search for executables/files. For any command that is not built into the shell or that is not defined with an absolute path, Linux will start searching in folders defined under PATH.

For example, instead of typing the following to run a Python program:

    /usr/bin/python3

Because the /usr/bin directory is included in the PATH variable, you can type this instead:

    python3


The directories are listed in priority order, so the ones that will be checked first are mentioned first.

Technique to Exploit this vulniribility

Example 

1. Saw that a file which I had permission to execute was running a binary or some file from a diffrent location and giving the output.

2. created a file with the same name as the binary/file that the main file was using lets say the dir where we created the file is `/tmp` & wrote a program to give the `/bin/bash` to me can also pass the simple command `echo "/bin/bash" > filename`

3. **Using the main PrivEsc method** we will add the `/tmp` to the $PATH `export PATH=/tmp:$PATH` Give execute permission to the file created

4. Go back and run the file... 

# NFS

NFS (Network File Sharing) configuration is kept in the `/etc/exports` file. This file is created during the NFS server installation and can usually be read by users.

The critical element for this privilege escalation vector is the “no_root_squash” option. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the “no_root_squash” option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.

Soo what i understood by reading all of it is that, there are few folders that you can mount to your device with real-time syncronization and you can create a C file that will give you a shell on your target

1. check for any shared directories with `cat /etc/exports`

2. If any file there has Read/Write & no_root_squash set you can mount it to your device

3. check the directories by `showmount -e IP`

4. Mount the one with `no_root_squash` by the command `mount -o rw IP:MOUNT_DIR_NAME LOCATION_ONYOUR_MACHINE`

5. create a C program to give you a shell and give it executable permission and SUID

6. Run it on the target machine

------------------------------------------------------------------------------------------------------------------------------

`echo $SHELL` to check who are we as shell































