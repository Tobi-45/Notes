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

13. 



