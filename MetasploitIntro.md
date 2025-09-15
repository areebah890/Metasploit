---

# Introduction to Metasploit - Try Hack Me room

---

- most widely used exploitation framework
- powerful tool that can support all phases of a pen test engagement, from info gathering to post-exploitation

**Metasploit has 2 main versions:**
- Metasploit pro: commercial version -> facilitates the automation and management of tasks, this version has graphical user interface (GUI)
- Metasploit Framework: open-source version -> works from command line

- this room focuses on metasploit framework, installed on AttackBox and most commonly used pen testing Linux distributions

---

main components of Metasploit Framework can be summarised follows:

- **msfconsole:** main command-line interface
- **modules:** supporting modules such as exploits, scanners, payloads, etc.
- **tools:** stand-alone tools -> help vulnerability research, vulnerability assessment, or pen testing
- Some of these tools are msfvenom, pattern_create and pattern_offset.

## Objective 

- cover main components of Metasploit while providing us with a solid foundation on how to find

    - relevant exploits
    - set parameters
    - exploit vulnerable services on the target system
    - be able to navigate and use the Metasploit command line comfortably

--- 

<details>
<summary>Main Components of Metasploit</summary>

- when using Metasploit Framework - primarily interact with the Metasploit console
-  launch it from the AttackBox terminal using the `msfconsole` command

<img width="688" height="368" alt="image" src="https://github.com/user-attachments/assets/c0a3b04d-9c23-49d6-a8ad-d242d5edb45c" />


-  console will be main interface to interact w/ different modules of Metasploit Framework
-  modules -> small components within Metasploit framework built to perform a specific task like

      - exploit a vulnerability
      - scanning a target
      - performing a brute-force attack
 
- recurring concepts:

    -  **exploit:** piece of code that uses a vulnerability present in the target system
    -  **vulnerability:** a design, coding, or logic flaw affecting target system. exploitation of a vulneraability can result in disclosing confidential info or allowing the attacker to execute code on the target system
    -  **payload:**  An exploit will take advantage of a vulnerability but if we want the exploit to have the result we want (gaining access to the target system, read confidential information, etc.), we need to use a payload ->  Payloads are the code that will run on the target system.
 
  ---

  - Modules and categories under each one are listed below
---

  **auxiliary**

  - Any supporting module, such as scanners, crawlers and fuzzers, can be found here

- <img width="323" height="49" alt="image" src="https://github.com/user-attachments/assets/dd998744-1536-473c-9f5f-9063bc005fad" />

- <img width="404" height="288" alt="image" src="https://github.com/user-attachments/assets/e4280114-ea2d-4655-ab03-f8aef155b0fe" />

- <img width="701" height="422" alt="image" src="https://github.com/user-attachments/assets/3f663061-5631-48de-aed0-3833d4b9fb4d" />

---
**encoders**

- allow us to encode the exploit and payload in the hope that a signature=based antivirus solution may miss them
- signature-based antivirus and security solutions have database of known threats
- detect threats by comparing suspicious files to this databse and raise an alert if there is a match
- thus encoders have limited success rate as antivirus solutions can perform additional checks

<img width="647" height="265" alt="image" src="https://github.com/user-attachments/assets/fec8b63b-e2a2-45a0-9aea-475d41dcdb10" />

---
**evasion**

- encoders will encode the payload, they should not be considered a direct attempt to evade antivirus software
- “evasion” modules will try that, with more or less success.

<img width="580" height="419" alt="image" src="https://github.com/user-attachments/assets/cc88fbd3-e825-45df-9bc0-9f1c55e229ab" />

---
**exploits**

- neatly organized by target system.

<img width="626" height="416" alt="image" src="https://github.com/user-attachments/assets/e4a0a356-5bfe-4b96-98df-e974f364a09a" />

---
**NOPs**

- No OPeration) do nothing
- represented in the Intel x86 CPU family with 0x90, following which the CPU will do nothing for one cycle.
- often used as a buffer to achieve consistent payload sizes.

<img width="449" height="238" alt="image" src="https://github.com/user-attachments/assets/08eb73db-ada6-4a85-ab86-8a1861290b29" />

---
**payloads**

-  codes that will run on the target system
-  Exploits will leverage a vulnerability on the target system
-   but to achieve the desired result, we will need a payload

-   examples:

    - getting a shell
    - loading a malware or backdoor to the target system
    - running a command
    - launching a calc.exe as proof of concept to add to the pen test report
    - Starting the calculator on the target system remotely by launching the calc.exe application is a benign way to show that we can run commands on the target system

 - running command on the target system is already an important step
 - but having an interactive connection that allows us to type commands that will be executed on the target system are better
 - Such an interactive command line is called a "shell"
 -  Metasploit offers the ability to send different payloads that can open shells on the target system.


<img width="422" height="131" alt="image" src="https://github.com/user-attachments/assets/674e5948-7eac-4fc4-9f4a-3d20a4205974" />

 4 different directories

- **adaptors:** wraps single payloads to convert them into different formats. e.g  a normal single payload can be wrapped inside a Powershell adapter, which will make a single powershell command that will execute the payload.
- **singles:** Self-contained payloads (add user, launch notepad.exe, etc.) that do not need to download an additional component to run.
- **stagers:** Responsible for setting up a connection channel between Metasploit and the target system -> Useful when working with staged payloads

    - “Staged payloads” will first upload a stager on the target system then download the rest of the payload (stage)
    - provides some advantages -> as the initial size of the payload will be relatively small compared to the full payload sent at once
 
- **stages:** downloaded by the stager -> allows use of larger sized payloads

- metasploit helps identify single (a.k.a inline) payloads and stages payloads

   - generic/shell_reverse_tcp
   - windows/x64/shell/reverse_tcp
     
- both are reverse window shells
- former is an inline payload as indicated by the “_” between “shell” and “reverse”
- latter is a staged payload -> indicated by the “/” between “shell” and “reverse”.

---
**post**

- Post modules will be useful on the final stage of the penetration testing process listed above, post-exploitation.

<img width="427" height="260" alt="image" src="https://github.com/user-attachments/assets/14f24927-60e0-4d69-aeb6-0b699f4ae4c5" />


---

<img width="415" height="272" alt="image" src="https://github.com/user-attachments/assets/ef2868ef-7b61-408e-a436-4f547dd9db24" />

</details>

<details>
<summary>Msfconsole</summary>

- 'msfconsole'

<img width="701" height="406" alt="image" src="https://github.com/user-attachments/assets/8c920c97-96cf-419c-818f-57659a7b443c" />

- can see command line changes to `msf6`
- `ls` lists the contents of the folder

<img width="445" height="81" alt="image" src="https://github.com/user-attachments/assets/534067c7-bd1c-443e-bb8b-629ee3608682" />

- followed by `ping` sent to google's DNS IP address (8.8.8.8)
- As we operate from the AttackBox, which is Linux we had to add the -c 1 option, so only a single ping was sent. Otherwise, the ping process would continue until it is stopped using CTRL+C

<img width="416" height="131" alt="image" src="https://github.com/user-attachments/assets/7d2286af-f812-4d50-8396-4de9622fae44" />

- it'll support most Linux command including 'clear' (clears terminal screen)
- will not allow us to use some features of a regular command line (e.g. does not support output redirection)

<img width="296" height="102" alt="image" src="https://github.com/user-attachments/assets/dc4b879d-92cb-4e36-b30a-2c9577657682" />

- help command can be used on its own or for a specific command

<img width="502" height="242" alt="image" src="https://github.com/user-attachments/assets/d35deaa5-6924-49de-bd2f-88e51e1398c8" />

- history command to see commands you have typed earlier

<img width="301" height="229" alt="image" src="https://github.com/user-attachments/assets/1a17b720-c7d3-44f3-92f8-9760f6daffbe" />

---

- important feature of msfconsole is the support of tab completion
- come in handy later when using Metasploit commands or dealing with module
- e.g. if you start typing `he` and press the tab key, you will see it auto-completes to `help`

**msfconsole context & variables**

- `msfconsole` changes prompt to show current module context (e.g., `msf6` exploit(...))
- Settings (e.g., `RHOSTS`, `LHOST`) are stored in the module's context by default
- If you use a different module, those module-specific settings are not preserved
- To keep a setting across module switches, set it as a global option:

   - `setg RHOSTS 10.10.10.5` (global) vs `set RHOSTS 10.10.10.5` (module-only)

- Use `show options` (module) and `show global` to view current settings
- One-liners are useful for automation:

    - `msfconsole -q -x "use exploit/...; setg RHOSTS 10.10.10.5; run; exit"`

**EternalBlue (MS17-010) — quick facts**

- Exploit targeting SMBv1 on Windows (MS17-010).
- Allegedly developed by the NSA; leaked by Shadow Brokers (April 2017).
- Widely used in the May 2017 WannaCry ransomware outbreak.
- Common Metasploit module: `exploit/windows/smb/ms17_010_eternalblue`.
- Typical workflow: `use exploit/...` → `show options` → `set RHOSTS/set LHOST` → `run`

---

<img width="476" height="98" alt="image" src="https://github.com/user-attachments/assets/7bcf23b5-c5d8-458a-b916-0ae8206f95c9" />

- module to be used can also be selected with the `use` command followed by the number at the beginning of the search result line
- prompt has changed, you will notice we can still run the commands previously mentioned
- means we did not "enter" a folder as you would typically expect in an operating system command line

<img width="391" height="156" alt="image" src="https://github.com/user-attachments/assets/dd4b9769-ff72-403f-bc47-f84c2693c718" />

- prompt tells us we now have a context set in which we will work. You can see this by typing the show options command

<img width="617" height="424" alt="image" src="https://github.com/user-attachments/assets/800ac969-5100-4963-aa48-95cacb099c53" />

- will print options related to the exploit we have chosen earlier
- shows options command will have different outputs depending on the context is is used in
- example above shows that this exploit require we set variable like RHOSTS and RPORT
- a post-exploitation module may only need us to set a SESSION ID (see the screenshot below). A session is an existing connection to the target system that the post-exploitation module will use


<img width="365" height="198" alt="image" src="https://github.com/user-attachments/assets/f6ddc6bc-e8fb-41b8-bc7c-cf95464545b2" />

- `show` command can be used in any context followed by a module type (auxiliary, payload, exploit, etc.) to list available modules
- example below lists payloads that can be used with the ms17-010 Eternalblue exploit

<img width="692" height="256" alt="image" src="https://github.com/user-attachments/assets/87bb0ee2-795f-4d07-92de-e6d1439473e9" />

- if used from msfconsole prompt, the `show` command will liist all modules
- the `use` and `show options` commands we have seen so far are identical for all modules in metasploit
- can leave the context using `back` command

<img width="302" height="78" alt="image" src="https://github.com/user-attachments/assets/d3daa532-76ca-42c6-9044-3aaf6e73437c" />

- further info on any module can be obtained typing `info` command within its context

<img width="710" height="364" alt="image" src="https://github.com/user-attachments/assets/afe83c65-b05c-4fd2-98dc-bcd4eae0a3b4" />

- can use the `info` command followed by the module’s path from the msfconsole prompt (e.g. `info exploit/windows/smb/ms17_010_eternalblue`)
- Info is not a help menu; it will display detailed information on the module such as its author, relevant sources, etc.

**search**

- command will search the Metasploit Framework database for modules relevant to the given search parameter.
- can conduct searches using CVE numbers, exploit names (eternalblue, heartbleed, etc.), or target system.

<img width="695" height="279" alt="image" src="https://github.com/user-attachments/assets/b821c1a4-9850-4aa0-b425-2110137deb9f" />

- output of `search` command provides an overview of each returned module
- “name” column already gives more information than just the module name.
- can see the type of module (auxiliary, exploit, etc.) and the category of the module (scanner, admin, windows, Unix, etc.)
- can use any module returned in a search result with the command use followed by the number at the beginning of the result line. (e.g. use 0 instead of use auxiliary/admin/smb/ms17_010_command)

<img width="372" height="220" alt="image" src="https://github.com/user-attachments/assets/4e441a6d-cd21-407f-a171-9befaee439a9" />

- can direct the search function using keywords such as type and platform
- e.g.  if we wanted our search results to only include auxiliary modules, we could set the type to auxiliary
- output of the search `type:auxiliary` telnet command

<img width="706" height="404" alt="image" src="https://github.com/user-attachments/assets/f54a0720-d9c3-4b53-ade2-e3b475fd50f5" />

- remember the exploits take advantage of a vulnerability on the target system and may always show unexpected behaviour
-  low-ranking exploit may work perfectly, and an excellent ranked exploit may not, or worse, crash the target system.

  ---

<img width="675" height="172" alt="image" src="https://github.com/user-attachments/assets/70dcc704-da15-40e3-ad81-df64ebbed205" />


  - load the module (this changes the prompt):

       - msf6 > `use auxiliary/scanner/ssh/ssh_login`
       - msf6 auxiliary(scanner/ssh/ssh_login) >

  - now run the commands
  
       - msf6 auxiliary(scanner/ssh/ssh_login) > `info`
   
<img width="641" height="403" alt="image" src="https://github.com/user-attachments/assets/7ac45327-ed93-48aa-86c6-60d2e6c3c472" />


</details>

<details>
<summary>Working with modules</summary>

<img width="314" height="149" alt="image" src="https://github.com/user-attachments/assets/468bc64d-4ec0-4a15-b91f-63291fe74ca5" />

- after using `use` command followed by module name we need to set parameters
- based in the module being used additional or different paramters may need to be set
- 'show options' command to list required parameters

- all parameters are set using same command syntax: `set PARAMETER_NAME VALUE`

---

- context prompt once module is decided and we used the set commands to choose it the msfconsole will show the contect
- can use context-specific commands (e.g. set RHOSTS 10.10.x.x) here

<img width="314" height="77" alt="image" src="https://github.com/user-attachments/assets/96cdf633-d8cc-4c8f-bc57-d3bb035ea300" />

---

**Meterpreter prompt**

- means a Meterpreter agent was loaded to the target system and connected back to you. You can use Meterpreter specific commands here.

<img width="306" height="67" alt="image" src="https://github.com/user-attachments/assets/8d1a7dd8-c1bf-499b-8fde-9565dce09c0d" />

a shell on the target system: once exploit completed may have access to a command shell on target system 
this is a regular command line - all commands typed here run on the target system

<img width="330" height="75" alt="image" src="https://github.com/user-attachments/assets/36aa6f0f-0f73-4e90-8257-3f6562f25495" />

---

<img width="445" height="357" alt="image" src="https://github.com/user-attachments/assets/e00ad62f-eaba-4d59-9bdb-1be81eb69f08" />

- some parameters require a value for the exploit to work
- Some required parameter values will be pre-populated, make sure you check if these should remain the same for your target.

- example

     -   web exploit could have an RPORT (remote port: the port on the target system Metasploit will try to connect to and run the exploit) value preset to 80, but your target web application could be using port 8080.
 
---

- we will set the RHOSTS parameter to the IP address of our target system using the `set` command

<img width="518" height="343" alt="image" src="https://github.com/user-attachments/assets/bfd5d05d-af64-460b-b205-45369d405825" />

- use `show options` command to check value was set correctly

**parameters will often use are**

- RHOSTS

    - 'remote host' the IP address of the target system
    - . A single IP address or a network range can be set.
    - will support the CIDR (Classless Inter-Domain Routing) notation (/24, /16, etc.) or a network range (10.10.10.x – 10.10.10.y). You can also use a file where targets are listed, one target per line using file:/path/of/the/target_file.txt


<img width="1843" height="633" alt="image" src="https://github.com/user-attachments/assets/4c3b5b9f-c6e2-4584-94d7-dbefb9a76581" />

- RPORT – Remote port on the target where the vulnerable service is running.
- PAYLOAD – The code executed on the target after exploitation (e.g., reverse shell).
- LHOST – Local host: your attacking machine’s IP (where the payload connects back).
- LPORT – Local port on your machine for the reverse shell; choose an unused port.
- SESSION – ID of an established connection to the target; used in post-exploitation modules.

---

- can override any set parameter using the set command again with a different value
- can also clear any parameter value using `unset` command or clear all set parameters with the `unset all` command

<img width="515" height="345" alt="image" src="https://github.com/user-attachments/assets/cfda6c70-217f-4fc4-bad0-d0de4adf5857" />

---

- use `setg` command to set values that will be used for all modules
- `setg` command is used like the set command
- difference is that if you use `set` command to set a value using a module and you switch to another module, you will need to set the value again.
- `setg`  command allows you to set the value so it can be used by default across different modules

- can clear any value set with `setg` using `unsetg`

- example below

1. use the ms17_010_eternalblue exploitable
2. set the RHOSTS variable `setg` command instead of the set command
3. use `back` command to leave the exploit context
4. use an auxiliary  (this module is a scanner to discover MS17-010 vulnerabilities)
5. `show options`  shows the RHOSTS parameter is already populated with the IP address of the target system.

<img width="449" height="429" alt="image" src="https://github.com/user-attachments/assets/8e59d597-81c3-4b92-a6a1-ec2ace7251fc" />

- `setg` command sets global value that will be used until we exit metasploit or clear using `unsetg` command

---

**using modules**

- once all module parameters are set we can launch using the `exploit`
- metasploit also supports `run` command -> alias created for `exploit` command
- `exploit` command can be used without any parameters or using the `“-z”` parameter
- `exploit -z` -> run the exploit and background the session as soon as it opens.

- <img width="441" height="400" alt="image" src="https://github.com/user-attachments/assets/8ddb15e6-1629-44a6-8261-eb835dd75424" />

= will return the context prompt from which we have run the exploit 
some modules support `check` 
will check if target system is vulnerable without exploiting it 

---

**sessions**

- once vulnerability been successfully exploited a session will be created
-  This is the communication channel established between the target system and Metasploit.
-  can use the `background` command to background the session prompt and go back to msfconsole prompt

<img width="320" height="93" alt="image" src="https://github.com/user-attachments/assets/1d4da7c4-bff9-4079-a4c5-3f0d6280fd4b" />

- CTRL+Z can be used to background sessions.
- `sessions` command can be used from the msfconsole prompt or any context to see the existing sessions.

<img width="734" height="397" alt="image" src="https://github.com/user-attachments/assets/7367907e-f24a-4c3c-bbf6-9cc2dd5f5a34" />

<img width="902" height="292" alt="image" src="https://github.com/user-attachments/assets/8ff3589b-6795-46d1-8d46-b68f26e68e44" />

---

<img width="719" height="279" alt="image" src="https://github.com/user-attachments/assets/b0c0fe5d-3582-4daa-9a61-1d240d140e9f" />

</details>

## summary

- Learned to launch and navigate msfconsole.
- Understood module types: exploits vs auxiliary.
- Learned key options: RHOSTS, LHOST, RPORT, LPORT, PAYLOAD, SESSION.
- Practiced setting options, running modules, and verifying results.
- Recognized module context: settings are module-specific unless made global.
- Gained hands-on experience with the workflow of penetration testing in Metasploit.
- Built confidence in selecting and using modules safely and effectively.
