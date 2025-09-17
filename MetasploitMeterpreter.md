---

# Metasploit: Meterpreter - Try Hack Me room

---

## Objectives

- see how in-memory payloads can be used for post-exploitation
- interact with the target operating system and files and use Meterpreter's specialized commands

---

<details>
<summary>Introduction to Meterpreter</summary>

- Meterpreter = Metasploit payload that supports the penetration testing process
- meterpreter will run on target system and act as an agent within a command and control architecture


**How does Meterpreter work?**

- meterpreter runs on target system but not installed on it
- runs in memory and doesn't write itself to the disk on the target
- feature aims to avoid being detected during antivirus scans
- by default most antivirus software will scan new files on the disk (e.g. when you download a file from the internet)
- Meterpreter runs in memory (RAM - Random Access Memory) to avoid having a file that has to be written to the disk on the target system (e.g. meterpreter.exe)
- so Meterpreter will be seen as a process and not have a file on the target system

- meterpreter also aims to avoid being detected by network-based IPS (intrusion prevention system) and IDS (intrusion detection system) solutions by using encrypted communication with the server where metasploit runs (typically our attacking machine)
- If the target organization does not decrypt and inspect encrypted traffic (e.g. HTTPS) coming to and going out of the local network, IPS and IDS solutions will not be able to detect its activities
- While Meterpreter is recognized by major antivirus software, this feature provides some degree of stealth


<img width="191" height="328" alt="image" src="https://github.com/user-attachments/assets/85fec60b-b015-4841-bd5f-60fb54ca3c95" />

--- 

- example below = shows target Windows machine exploited using the MS17-010 vulnerability
- `getpid` -> returns process ID that meterpreter is running
- process ID -> used by OS to identify running processes
- all processes running in Linux or Windows have unique ID number
- this number is used to interact with the process when the need arises (e.g. if it needs to be stopped)

<img width="238" height="62" alt="image" src="https://github.com/user-attachments/assets/0066d2db-f706-4469-ad78-abbe8d970474" />

- if we use `ps` to process running on the target system - we see PID 1304 is spoolsv.exe and not meterpreter.exe

<img width="565" height="278" alt="image" src="https://github.com/user-attachments/assets/992744d5-64c5-47fe-a90c-3ec025a36c3e" />

- if we look at DLLs (dynamic-link libraries) used by meterpreter process (PID 1304 in this case)
- still would not find anything really significant (e.g. no meterpreter.dll)

<img width="341" height="427" alt="image" src="https://github.com/user-attachments/assets/6b3db10a-3100-4ea3-b9d1-b57ced01770d" />

- `tasklist /m /fi "pid eq 1304"`
    
     - `tasklist` lists running processes on windows
     - `/m` shows the modules (DLLs) loaded by the process
     - `/fi "pid eq 1304"` filters the output to only the process with PID 1304
     - so output tells us which process is PID 1304 and which DLLs that process has loaded
 
---

- this section aimed to show us how stealthy meterpreter is runninh
- most antivirus software will detect it
- meterpreter will establish an encrypted (TLS) communication channel with the attacker's system


</details>

<details>
<summary>Meterpreter Flavors</summary>

# Exploit → Meterpreter Flow

```text
         +----------------------+
         |    Exploit Module    |
         |  (e.g., EternalBlue)|
         +----------+-----------+
                    |
                    v
     +-----------------------------+
     | Compatible Payloads         |
     | - windows/x64/meterpreter   |
     |   /reverse_tcp (staged)     |
     | - windows/x64/meterpreter   |
     |   /reverse_https            |
     +-----------------------------+
                    |
                    v
     +-----------------------------+
     | Meterpreter Session Created |
     | - getpid                    |
     | - sysinfo                   |
     | - ps                        |
     +-----------------------------+
```



- Staged payloads

   - Sent to the target in two steps.
   - The stager is installed first and requests the rest of the payload.
   - Advantage: smaller initial payload size.

- Inline payloads

  - Sent to the target in a single step.
  - No separate stage is required.

- Meterpreter payloads

   - Can be staged or inline.
   - Many different versions exist to match different target systems and architectures.


---

- List available Meterpreter versions using msfvenom.
- Command: `msfvenom --list payloads | grep meterpreter`

   - `--list payloads` shows all payloads in Metasploit.
   - `| grep meterpreter` filters the list to only show Meterpreter payloads.

- Can be run directly on the AttackBox to see options for your target system.
   

- <img width="177" height="11" alt="image" src="https://github.com/user-attachments/assets/a0e5403f-911a-434d-a114-575f5c29bff8" />

<img width="884" height="450" alt="image" src="https://github.com/user-attachments/assets/0a9031af-ca0e-41a8-8561-5adce5890cbe" />

---

**Platforms / Operating Systems**

- the list will show meterpreter versions available for following platforms
- Meterpreter has versions for different platforms, because binaries and runtime environments differ across devices. Examples:

   - Windows – most common target for labs and real-world scenarios.
   - Linux – can use staged Meterpreter for servers.
   - macOS (OSX) – desktop/mac devices.
   - Android / iOS – mobile devices.
   - PHP / Python / Java – for web apps or interpreted environments.
 

**Components available on the target system**
- Some Meterpreter versions require software already installed on the target:

     - Python Meterpreter → works if Python is installed.
     - PHP Meterpreter → works if the server runs PHP.
     - Java Meterpreter → works if Java is installed.

- Why it matters: If you try to use a Python Meterpreter on a system with no Python, it won’t execute. Always check the target environment.


**Network connection types**

- Meterpreter can communicate with your attacking machine in different ways. Your choice depends on what the network allows:

     - Raw TCP (reverse_tcp) → simplest, fast, but might be blocked by firewalls.
     - HTTPS (reverse_https) → encrypted, often bypasses firewalls/IDS.
     - IPv6 → sometimes less monitored than IPv4, useful in certain labs.

- Why it matters: Even if the payload matches the OS, it will fail if it cannot connect back to your listener. You need to choose a payload that fits the allowed network protocols

**How to decide which Meterpreter to use**

1. Check the target OS → pick the matching platform (Windows, Linux, Android, etc.).
2. Check installed components → use Python, PHP, or Java versions only if the environment supports it.
3. Check network constraints → choose reverse_tcp, reverse_https, or another transport based on what will successfully reach your listener.

- Only if all three match will the Meterpreter session succeed

---

- use an exploit module in Metasploit (like ms17_010_eternalblue), the exploit itself may dictate or limit which payloads we can use
- This happens because some exploits only work with certain payload types or architectures

**Explanation step by step**

1. Standalone payload vs exploit-delivered payload

     - standalone payload -> generate a payload with `msfvenom` (e.g. `windows/meterpreter/reverse_tcp`) and deliver it manually -> full control over which payload you use
     - exploit-delivered payload ->

       - often comes with a default payload optimised for that exploit
       - e.g. `use exploit/windows/smb/ms17_010_eternalblue show options'
       - we'll see see a default payload like `windows/x64/meterpreter/reverse_tcp`
       - exploit may only support staged Meterpreter, a specific architecture, or certain transport types.
      
2. Why payload choice is limited by exploit

- Exploit code may rely on specific memory structures, registers, or behaviors of the target OS
- Only certain payloads will fit into the exploit’s “shellcode slot” without crashing the target
- For example, `ms17_010_eternalblue` works only with Windows x64 or staged Meterpreter payloads.

3. What this means for us in labs

- can still change the payload in Metasploit if the exploit allows it (`set payload <payload>`)
- cannot choose any random payload — the exploit may reject it, fail to execute, or crash the target
- Always check the exploit documentation or `show payloads` in msfconsole to see compatible Meterpreter payloads.

**summary**

1. Standalone attack: you have full payload flexibility.
2. Exploit-based attack: you’re mostly limited to the payloads that are compatible with the exploit’s requirements



--- 

<img width="567" height="305" alt="image" src="https://github.com/user-attachments/assets/eaa626b0-de76-4142-8e88-841cfafa5004" />

</details>

<details>
<summary>Meterpreter Commands</summary>

- `help` on any meterpreter session lists all available commands

<img width="362" height="192" alt="image" src="https://github.com/user-attachments/assets/c0383b6e-3489-4948-85fd-78f3fcf41d86" />


- every version of meterpreter have different command options -> so running `help` is useful
- Commands are built-in tools available on Meterpreter
- They will run on the target system without loading any additional script or executable files

--- 

- Meterpreter provides three primary categories of tools;

1. Built-in commands
2. Meterpreter tools
3. Meterpreter scripting

---

- `help` command -> see Meterpreter commands are listed under different categories (this list is the output from help command on the Windows version of Meterpreter (`windows/x64/meterpreter/reverse_tcp`) -> will be different for other Meterpreter versions

     - core commands
     - file system commands
     - networking commands
     - system commands
     - user interface commands
     - webcam commands
     - audio output commands
     - elevate commands
     - password database commands
     - timestomp commands
 
---

**Meterpreter commands**

- core commands helpful to navigate and interact with the target system
- below are some of the most commonly used
- remember -> check all available commands running `help` command once a Meterpreter session has started

CORE COMMANDS

| Command     | Description                                           |
|------------|-------------------------------------------------------|
| background | Backgrounds the current session                       |
| exit       | Terminates the Meterpreter session                    |
| guid       | Gets the session GUID (Globally Unique Identifier)   |
| help       | Displays the help menu                                |
| info       | Displays information about a Post module             |
| irb        | Opens an interactive Ruby shell on the current session|
| load       | Loads one or more Meterpreter extensions             |
| migrate    | Allows you to migrate Meterpreter to another process |
| run        | Executes a Meterpreter script or Post module         |
| sessions   | Quickly switch to another session                    |

FILE SYSTEM COMMANDS
  
| Command   | Description                                         |
|-----------|-----------------------------------------------------|
| cd        | Changes the current directory                       |
| ls        | Lists files in the current directory (dir also works) |
| pwd       | Prints the current working directory               |
| edit      | Allows you to edit a file                           |
| cat       | Shows the contents of a file on the screen         |
| rm        | Deletes the specified file                          |
| search    | Searches for files                                  |
| upload    | Uploads a file or directory                         |
| download  | Downloads a file or directory                       |

NETWORKING COMMANDS 

| Command   | Description                                           |
|-----------|-------------------------------------------------------|
| arp       | Displays the host ARP (Address Resolution Protocol) cache |
| ifconfig  | Displays network interfaces available on the target system |
| netstat   | Displays the network connections                     |
| portfwd   | Forwards a local port to a remote service           |
| route     | Allows you to view and modify the routing table     |

SYSTEM COMMANDS 

| Command   | Description                                               |
|-----------|-----------------------------------------------------------|
| clearev   | Clears the event logs                                     |
| execute   | Executes a command                                        |
| getpid    | Shows the current process identifier                     |
| getuid    | Shows the user that Meterpreter is running as            |
| kill      | Terminates a process                                      |
| pkill     | Terminates processes by name                              |
| ps        | Lists running processes                                   |
| reboot    | Reboots the remote computer                               |
| shell     | Drops into a system command shell                         |
| shutdown  | Shuts down the remote computer                            |
| sysinfo   | Gets information about the remote system (e.g., OS)      |

OTHER COMMANDS (these will be listed under different menu categories in the help menu)

| Command          | Description                                                   |
|-----------------|---------------------------------------------------------------|
| idletime         | Returns the number of seconds the remote user has been idle  |
| keyscan_dump     | Dumps the keystroke buffer                                    |
| keyscan_start    | Starts capturing keystrokes                                   |
| keyscan_stop     | Stops capturing keystrokes                                    |
| screenshare      | Allows you to watch the remote user's desktop in real time   |
| screenshot       | Grabs a screenshot of the interactive desktop                |
| record_mic       | Records audio from the default microphone for X seconds      |
| webcam_chat      | Starts a video chat                                           |
| webcam_list      | Lists webcams                                                 |
| webcam_snap      | Takes a snapshot from the specified webcam                   |
| webcam_stream    | Plays a video stream from the specified webcam               |
| getsystem        | Attempts to elevate your privilege to that of local system   |
| hashdump         | Dumps the contents of the SAM database                        |

</details>

<details>
<summary>Post-Exploitation with Meterpreter</summary>

- meterpreter provides us with useful commands that facilitate the post-exploitation phase

---

**help**

- `help` gives list of all available commands in meterpreter
- Meterpreter has many versions, and each version may have different options available
- Typing help once you have a Meterpreter session will help you quickly browse through available commands

<img width="365" height="199" alt="image" src="https://github.com/user-attachments/assets/3a940f42-4e66-4541-9df4-ba06374f1dc1" />

---

**meterpreter commands**

- `getuid` -> displays the user with which meterpreter us currently running
- will give us ideas of our possible privilege level on the target system (e.g. are you an admin level user like NT AUTHORITY\SYSTEM or a regular user?)

<img width="491" height="464" alt="image" src="https://github.com/user-attachments/assets/14dcc535-6b1a-4d45-bb37-34e9b3938696" />

---

**migrate**

- migrating to another process help meterpreter interact with it
- e.g. if we see word processor running on the target (e.g. word.exe, notepad.exe, etc.) -> we can migrate to it and start capturing keystrokes sent by the user to this process
- some meterpreter versions offer `keyscan_start`, `keyscan_stop`, `keyscan_dump` command options to make meterpreter act like a keylogger
- migrating to another process may also help us to have a more stable meterpreter session

<img width="550" height="92" alt="image" src="https://github.com/user-attachments/assets/bed18f93-fbb5-491a-b278-bf0e2e20410f" />

- may lose your user privileges if you migrate from a higher privileged (e.g. SYSTEM) user to a process started by a lower privileged user (e.g. webserver). You may not be able to gain them back

---

**hashdump**

- `hashdump` list the content of SAM database
- SAM (security account manager) database stores user's passwords on window systems
- passwords are stored in the NTLM (New Technology LAN Manager) format

<img width="380" height="88" alt="image" src="https://github.com/user-attachments/assets/ebdae586-4d30-4010-996a-45ddc8d09216" />

- not mathematically possible to "crack" these hashes
- may still discover the cleartext password using online NTLM databases or a rainbow table attack
- these hashes can also be used in Pass-the-Hash attacks to authenticate to other systems that these users can access the same network

---

**search**

- `search` useful to locate files with potentially juicy info
- in CTF context this can be used to quickly find a flag or proof file
- in actual pen testing engagements we may need to search for user-generated files or configuration files that may contain password or account info 

<img width="339" height="84" alt="image" src="https://github.com/user-attachments/assets/51755a77-5643-4f32-b9b6-49390f602a3e" />

---

**shell**

- shell command will launch a regular command-line shell on the target system
- pressing CTRL+Z will help you go back to the Meterpreter shell

<img width="361" height="111" alt="image" src="https://github.com/user-attachments/assets/34f756ba-45ca-48be-974f-5e285a7bb110" />

</details>

<details>
<summary>Post-Exploitation Challenge PRACTICAL</summary>

- `getsystem` and `hashdump` will provide important leverage and information for privilege escalation and lateral movement
- meterpreter -> good base we can use to run post-exploitation modules available on the Metasploit framework
- can also use the load command to leverage additional tools such as Kiwi or even the whole Python language

<img width="347" height="101" alt="image" src="https://github.com/user-attachments/assets/0057e45b-ebc4-4fdd-8f03-f1d81ccf613f" />

---

- post-exploitation phase have several goals; Meterpreter has functions that can assist all of them

     -  Gathering further information about the target system.
     -  Looking for interesting files, user credentials, additional network interfaces, and generally interesting information on the target system.
     -  Privilege escalation.
     -  Lateral movement.
 
--- 


- Once any additional tool is loaded using the `load` command ->  will see new options on the `help` menu
- example below shows commands added for the Kiwi module (using the `load kiwi` command)

<img width="406" height="436" alt="image" src="https://github.com/user-attachments/assets/3c34b8b8-28df-495c-ad0b-ae4b19a3d414" />

</details>

## practical 

- <img width="425" height="64" alt="image" src="https://github.com/user-attachments/assets/cd4720b3-1d3c-470f-b2e5-32ad61527ff6" />

- use exploit provided

- <img width="305" height="38" alt="image" src="https://github.com/user-attachments/assets/c54b622d-ccc3-4fe3-95ec-186b8d37e380" />

- ensure LHOST = attack machine (10.10.74.220) and set RHOST = target machine (10.10.234.132)

- <img width="475" height="395" alt="image" src="https://github.com/user-attachments/assets/5993d89f-1099-4560-b177-4f3ffebad01c" />

- as seen above the smbuser and smbpass are not set yet so we set them with the login credentials provided

- <img width="468" height="285" alt="image" src="https://github.com/user-attachments/assets/a396f6b0-1e9b-44fa-97ac-74e951ede474" />

- type `run` command

- <img width="446" height="80" alt="image" src="https://github.com/user-attachments/assets/f4de9be4-085c-4354-b151-4bc7740126f6" />

- can see meterpreter session successfully open 

**what is the computer name? hint = use `sysinfo` command**

- <img width="275" height="69" alt="image" src="https://github.com/user-attachments/assets/20eb49bf-c57a-4f3a-9851-43c260a984ff" />

- <img width="149" height="65" alt="image" src="https://github.com/user-attachments/assets/80113687-05da-46f7-b547-b8c4df9c11dc" />

**What is the target domain? hint = Use the "post/windows/gather/enum_domain" module. You will need to background Meterpreter first and set the SESSION parameter.**

- first we background meterpreter session parameter -> `background`

- <img width="149" height="32" alt="image" src="https://github.com/user-attachments/assets/15a093c8-9073-4f30-a2b0-465471c07909" />

- look at our different sessions `session -i`

- <img width="470" height="104" alt="image" src="https://github.com/user-attachments/assets/b1aec598-b865-423c-bce9-bb14fe56a009" />

- for this task we are asked to find domain so we need different module than the one we just made 
- we're going to `use post/windows/gather/enum_domain` whilst keeping current session active
- we already backgrounded our current session - we can switch back to it using `session 1` (we know the id from when we checked out active sessions)
- then we press run and can see the domain name 

- <img width="464" height="185" alt="image" src="https://github.com/user-attachments/assets/fb8ad452-5c77-4269-839d-946af81ce72d" />

- <img width="95" height="62" alt="image" src="https://github.com/user-attachments/assets/433b10a5-dc30-49e9-a1c5-a80e19aac17d" />


**What is the name of the share likely created by the user? hint = Use the "post/windows/gather/enum_shares" module. You will need to background Meterpreter first and set the SESSION parameter.**

- <img width="332" height="317" alt="image" src="https://github.com/user-attachments/assets/3bdf56f0-e1e8-4976-bbe8-b690316f7db3" />

- <img width="116" height="73" alt="image" src="https://github.com/user-attachments/assets/3146af11-4317-4515-900c-b22aed937d83" />

---

**What is the NTLM hash of the jchambers user? hint = In the Meterpreter prompt: You will need to migrate to the "lsass.exe" process first (ps will list its PID), then run "hashdump".**

- <img width="182" height="56" alt="image" src="https://github.com/user-attachments/assets/5c0d47f5-d6b3-44dc-ab96-4b6fb8bb1f4a" />

- not many processes

- looking for lsass process so we move to active meterpreter session

- <img width="232" height="41" alt="image" src="https://github.com/user-attachments/assets/1db2781d-b837-430d-abaa-cf0c8b070758" />

- can see more processes are running

- <img width="306" height="375" alt="image" src="https://github.com/user-attachments/assets/b1ef991d-27b0-4f7f-8434-2c30083a80d3" />

- PID = 772
- so we migrate `migrate PID`

- <img width="180" height="44" alt="image" src="https://github.com/user-attachments/assets/e3293902-4565-4af6-92ca-da7114baf284" />

- then we do `hashdump`

- <img width="291" height="58" alt="image" src="https://github.com/user-attachments/assets/d258dff5-8e1d-44d2-b26e-63a72778de10" />

- mine didnt work so i think its because i did x84 not x64

- <img width="339" height="399" alt="image" src="https://github.com/user-attachments/assets/089c36c8-6ca6-42e4-9e8b-cdb6fd2c5277" />

- <img width="308" height="417" alt="image" src="https://github.com/user-attachments/assets/f4d2434f-3478-4386-9243-2c5ea2cb01a0" />

- now we migrate again

- <img width="338" height="199" alt="image" src="https://github.com/user-attachments/assets/77ecf080-d9d1-43e2-b8be-fa8407265a36" />

- worked this time 

- <img width="122" height="74" alt="image" src="https://github.com/user-attachments/assets/b97b47e1-fbe6-4955-ae44-2e3e9a9d5154" />

**What is the cleartext password of the jchambers user? hint = You can use an online hash checker like crackstation.net**

- <img width="455" height="295" alt="image" src="https://github.com/user-attachments/assets/f9aa28e4-66b3-46b3-9846-a889d611b088" />

- <img width="116" height="73" alt="image" src="https://github.com/user-attachments/assets/4e426d3d-21a3-4f7f-a74c-1350ceebfe23" />

**Where is the "secrets.txt"  file located? (Full path of the file)** hint =  You can use any of these commands: search -f *.txt search -f secrets.txt

- * = anything with .txt we want to see

- <img width="332" height="460" alt="image" src="https://github.com/user-attachments/assets/c2e1420c-5532-4d0f-af0a-9ade9bd588d3" />

- <img width="317" height="110" alt="image" src="https://github.com/user-attachments/assets/54438bff-c8ce-4ec4-8520-82783d163e40" />

- <img width="119" height="76" alt="image" src="https://github.com/user-attachments/assets/b303c5f0-a43c-4582-aaf3-b99c9d5b6fa3" />

**What is the Twitter password revealed in the "secrets.txt" file? hint = Use the cat command to see the content of the file**

- change directories until we have file then cat to read it 

- <img width="311" height="226" alt="image" src="https://github.com/user-attachments/assets/53b63954-578a-4e5d-b7b8-920b417e70e5" />

- <img width="187" height="62" alt="image" src="https://github.com/user-attachments/assets/c8349fbe-f29a-4f32-b5b8-114b5db7362d" />


**Where is the "realsecret.txt" file located? (Full path of the file)** hint =  You can use any of these commands: search -f *.txt search -f realsecret.txt

- <img width="319" height="90" alt="image" src="https://github.com/user-attachments/assets/356760c9-c13b-42a9-9b8c-f05525eae522" />

- <img width="128" height="83" alt="image" src="https://github.com/user-attachments/assets/6ca444ab-95ed-420e-93bb-a54ba86c8369" />

**What is the real secret? hint =  Use the cat command to see the content of the file**

- <img width="332" height="193" alt="image" src="https://github.com/user-attachments/assets/db58a991-f69d-4151-9076-411a265e3067" />

- <img width="219" height="68" alt="image" src="https://github.com/user-attachments/assets/e92b49d6-f869-46fa-8fd8-b40111bbc618" />


## Summary/ reflection

In the Meterpreter premium room I gained practical experience with in-memory payloads and post-exploitation workflows using Metasploit. 
I learned to select payloads that match the target’s OS, architecture and network constraints, to configure and debug handlers, and to interact with Meterpreter sessions for enumeration and lateral movement.
I also experienced real pitfalls -> notably a session crash caused by migrating a 32-bit payload into a 64-bit system process (lsass.exe) and then running hashdump, which taught me the importance of architecture checks, safe migration targets, and using post modules. 
Going forward I’ll focus on practicing exact payload selection, safer credential harvesting techniques, and defensive detection methods -> all within authorized lab environments.




