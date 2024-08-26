# Enumeration

1. The `hostname` command will return the hostname of the target machine. Sometimes useful as it can show the devices role on the network

2. `uname -a` Will print system information giving us additional detail about the kernel used by the system. helpful to find potential kernel vulnerabilities for priv. esc.

3. `proc/version` The proc filesystem (procfs) provides information about the target system processes. This may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed

4. `etc/issue` This file usually contains some information about the operating system but can easily be customized or changed. 

5. `ps` command
   


