# Windows Users

**Administrators**	These users have the most privileges. They can change any system configuration parameter and access any file in the system.

**Standard Users**	These users can access the computer but only perform limited tasks. Typically these users can not make permanent or essential changes to the system and are limited to their files.

# Built-In Win Accounts

**SYSTEM / LocalSystem**  An account used by the operating system to perform internal tasks. It has full access to all files and resources available on the host with even higher privileges than administrators.

**Local Service** Default account used to run Windows services with "minimum" privileges. It will use anonymous connections over the network.

**Network Service** Default account used to run Windows services with "minimum" privileges. It will use the computer credentials to authenticate through the network.

# Harvesting Passwords from Usual Spots

## Unattended Windows Installations

When installing Windows on a large number of hosts, administrators may use Windows Deployment Services, which allows for a single operating system image to be deployed to several hosts through the network. These kinds of installations are referred to as unattended installations as they don't require user interaction. Such installations require the use of an administrator account to perform the initial setup, which might end up being stored in the machine in the following locations:

    C:\Unattend.xml
    C:\Windows\Panther\Unattend.xml
    C:\Windows\Panther\Unattend\Unattend.xml
    C:\Windows\system32\sysprep.inf
    C:\Windows\system32\sysprep\sysprep.xml

As part of these files, you might encounter credentials:

    <Credentials>
        <Username>Administrator</Username>
           <Domain>thm.local</Domain>
         <Password>MyPassword123</Password>
    </Credentials>

## Powershell History

`type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

**Note**: The command above will only work from `cmd.exe`, as Powershell won't recognize `%userprofile%` as an environment variable. To read the file from Powershell, you'd have to replace `%userprofile%` with `$Env:userprofile`

## Saved Windows Credentials

`cmdkey /list`

While you can't see the actual passwords, if you notice any credentials worth trying, you can use them with the `runas` command and the `/savecred` option, as seen below.

`runas /savecred /user:admin cmd.exe`

## IIS Configuration

Internet Information Services (IIS) is the default web server on Windows installations. The configuration of websites on IIS is stored in a file called web.config and can store passwords for databases or configured authentication mechanisms. Depending on the installed version of IIS, we can find web.config in one of the following locations:

    C:\inetpub\wwwroot\web.config
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config

Here is a quick way to find database connection strings on the file:

`type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString`

## Retrieve Credentials from Software: PuTTY

PuTTY is an SSH client commonly found on Windows systems. While PuTTY won't allow users to store their SSH password, it will store proxy configurations that include cleartext authentication credentials.

To retrieve the stored proxy credentials, you can search under the following registry key for ProxyPassword with the following command:

`reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s`

**Note**: Simon Tatham is the creator of PuTTY (and his name is part of the path), not the username for which we are retrieving the password. The stored proxy username should also be visible after running the command above.

# Easy tricks for Priv Esc

## Scheduled Tasks

Command to check scheduled tasks `schtasks`

To get detailed info `schtasks /query /tn vulntask /fo list /v`

You will get lots of information about the task, but what matters for us is the "Task to Run" parameter which indicates what gets executed by the scheduled task, and the "Run As User" parameter, which shows the user that will be used to execute the task.

To check the file permissions on the executable, we use `icacls`

## AlwaysInstallElevated

Windows installer files (also known as .msi files) are used to install applications on the system. They usually run with the privilege level of the user that starts it. However, these can be configured to run with higher privileges from any user account (even unprivileged ones). This could potentially allow us to generate a malicious MSI file that would run with admin privileges.

Make a malicious .msi file using msfvenom, and send it over and run the installer.

**Need to do more research on This**

# Abusing Service Misconfigurations

## Windows Services

Windows services are managed by the Service Control Manager (SCM). The SCM is a process in charge of managing the state of services as needed, checking the current status of any given service and generally providing a way to configure services.

Each service on a Windows machine will have an associated executable which will be run by the SCM whenever a service is started. It is important to note that service executables implement special functions to be able to communicate with the SCM, and therefore not any executable can be started as a service successfully. Each service also specifies the user account under which the service will run.

**Command to check for service configuration `nc qc SERVICE_CONFIG_NAME`**

**Steps to enumerate this**

If the executable associated with a service has weak permissions that allow an attacker to modify or replace it, the attacker can gain the privileges of the service's account trivially.

1.  Check where the executable is created for the service with the command `sc qc SERVICE_NAME` also, Check `BINARY_PATH_NAME` & `SERVICE_START_NAME` to know the location of executable & to know who the service run's as

2. Check the permissions for the `.exe` with the command `icacls EXE_PATH`

3. Create the payload using `msfvenom` share it over to the victime, replace it with the real exe

4.  Grant the permission to all group using `icacls .exe /grant Everyone:F`

5. wait for the service to restart & start the lister

## Unquoted Service Paths

When working with Windows services, a very particular behaviour occurs when the service is configured to point to an "unquoted" executable. By unquoted, we mean that the path of the associated executable isn't properly quoted to account for spaces on the command.

Example : C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe

Instead of failing as it probably should, SCM tries to help the user and starts searching for each of the binaries in the order shown in the table:

1.  First, search for `C:\\MyPrograms\\Disk.exe`. If it exists, the service will run this executable.

2. If the latter doesn't exist, it will then search for `C:\\MyPrograms\\Disk Sorter.exe`. If it exists, the service will run this executable.

3. If the latter doesn't exist, it will then search for `C:\\MyPrograms\\Disk Sorter Enterprise\\bin\\disksrs.exe`. This option is expected to succeed and will typically be run in a default installation.

**Method to exploit it**

1. create a .exe file with the any word before space Eg: Disk.exe

2. and listen for the shell

3. Grant the file permission to everyone `icacls C:\MyPrograms\Disk.exe /grant Everyone:F`

## Insecure Service Permissions

Should the service DACL (not the service's executable DACL) allow you to modify the configuration of a service, you will be able to reconfigure the service. This will allow you to point to any executable you need and run it with any account you prefer, including SYSTEM itself.

Checking for a service DACL with `accesschk64.exe -qlc SERVICE_NAME` (Research about the accesschk command/ Installation)

**Create a reverse/tcp payload using msfvenom & transfer it to the victime**

**Grant permission to everyone**

To change the service's associated executable and account, we can use the following command (mind the spaces after the equal signs when using sc.exe)

`C:\> sc config SERVICE_NAME binPath= "OUR_PAYLOAD_PATH" obj= LocalSystem`

Notice we can use any account to run the service. We chose LocalSystem as it is the highest privileged account available.

        





























