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























