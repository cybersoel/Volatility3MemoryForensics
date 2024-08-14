<h1 align="center">Summary Diagram</h1>


<p align="center">
<br/>
<img width="780" alt="Portfolio" src="https://i.imgur.com/u9Zhx57.png">
<br />
</p>


<br />
<br />

<h1 align="center">Project walk-through</h1>

<br />
<br />

---
<a name="toc"></a>
**Table of Contents:**


- [Introduction](#introduction)
- [Installation and Setup](#installation-and-setup)
- [Memory Acquisition ](#memory-acquisition)
- [Basic Volatility 3 Usage](#basic-volatility-3-usage)
- [Analyzing Processes](#analyzing-processes)
- [Investigating File Handles](#investigating-file-handles)
- [Examining Command Line Usage](#examining-command-line-usage)
- [Network Activity Analysis](#network-activity-analysis)
- [Password Hash Extraction](#password-hash-extraction)
- [Registry UserAssist Analysis](#registry-userassist-analysis)
- [Registry Hive Examination](#registry-hive-examination)
- [Specific Registry Key Analysis](#specific-registry-key-analysis)



---
**Credit to:**

- *YT DFIRScience, Joshua I. James, Digital Forensic Scientist*
- *YT CyDig Cyber Security Digital Forensics Education*




---

<h4>Tip: Any configuration options not mentioned in the walkthrough can be left at their default settings</h4>

---

[back to top](#toc)
## Introduction

<br/>

In this lab, we will dive into Volatility 3 using some basic features to understand the program. We will use various filters to extract information that might help us to reconstruct user activity.


<br/>


---

[back to top](#toc)
## Installation and Setup 

<br />
<br />


 - Install Python 3 on your system.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/XX2KGD9.png">
<br />
<br />
<br />
<br />




 - Navigate to https://volatilityfoundation.org/the-volatility-framework/
 - Click on the "Volatility 3" link.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/nRx1K4F.png">
<br />
<br />
<br />
<br />


 - You will be redirected to the GitHub page. Click on the latest version.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/ycDndFE.png">
<br />
<br />
<br />
<br />

 - Download the "Source code (.zip)" file.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/2HUsPOd.png">
<br />
<br />
<br />
<br />

 - Extract the .zip file to your desired location (in this lab, we're extracting it to the desktop).
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/hKYgGB1.png">
<br />
<br />
<br />
<br />

 - Open PowerShell and navigate to the Volatility 3 directory.
 - You should see a Python file named "vol.py" in this directory.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/iHEtKRx.png">
<br />
<br />
<br />
<br />

 - Return to the Volatility 3 GitHub page and scroll down until you see the "Symbol Tables" section.
 - Download the Symbol table packs for Windows.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/XwyyAD9.png">
<br />
<br />
<br />
<br />

 - The .zip file is approximately 800MB. Place it in the following directory: volatility3-x.x.x\volatility3\symbols
 - Do not unzip this file.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/frAWefC.png">
<br />
<br />
<br />
<br />

 - To run Volatility 3 smoothly, we need to install all essential files. You can find a file named "requirements.txt" in the Volatility 3 folder.
 - Use the command `pip install -r <FilePath\requirements.txt>` to install the required files.
 - However, you need to have Microsoft C++ Build Tools installed before running this command, otherwise you'll encounter an error at this stage.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/VoXmsJp.png">
<br />
<br />
<br />
<br />

 - You can download the C++ Build Tools from visualstudio.microsoft.com
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/3UNgJot.png">
<br />
<br />
<br />
<br />

 - In this screenshot, I've selected some optional features. However, you can also proceed with installing only the minimum C++ core features.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/vcdBFIK.png">
<br />
<br />
<br />
<br />


 - We also need to install the setuptools module for Python in case we encounter the error: `Volatility could not import a necessary module: Capstone`
 - This occurs because the capstone module relies on the distutils module, which is deprecated in Python 3.10 and removed from the standard library.
 - We can use the Setuptools module instead, which provides distutils. Run `pip install setuptools`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/JRnnD2d.png">
<br />
<br />
<br />
<br />


 - Use the command `py vol.py -v` (lowercase v). If no error message appears, we're good to go!
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/Y5HC6gt.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Memory Acquisition

<br />
<br />

 - Before we dive into Volatility 3, we need a memory dump file for Volatility to analyze. We can use FTK Imager to capture the memory of the current system. (https://www.exterro.com/downloads/search-results?q=imager&category=)
 - Open FTK Imager and go to File > Capture Memory
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/6JVq9bj.png">
<br />
<br />
<br />
<br />

 - Choose a destination path and filename. We can also include capturing the pagefile (memory written to the hard drive) and AD1 file, however, we will exclude these in this lab.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/oa0EDtN.png">
<br />
<br />
<br />
<br />



 - I encountered an error when capturing memory with FTK Imager :( I discovered that some Dell laptop models (the one I'm using for this lab) face BSOD (Blue Screen of Death) issues when using FTK Imager.
 - I decided to use "Belkasoft Live RAM Capturer" instead of FTK Imager. (There are many other options like DumpIt and Magnet RAM Capture)
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/Lve8GSm.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Basic Volatility 3 Usage

<br />
<br />

 - We are now ready to analyze a memory dump file with Volatility 3.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/kPQJmhP.png">
<br />
<br />
<br />
<br />

 - Let's start by getting information about the memory image. Use the following command in PowerShell: `py vol.py -f "C:<FilePath>\20240712.mem" windows.info`
 - `-f` specifies which file to analyze. In Volatility 3, the plugin we're using is specified at the end.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/RdeoLuf.png">
<br />
<br />
<br />
<br />

 - After a few minutes, you will see output similar to this:
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/neG0Jup.png">
<br />
<br />
<br />
<br />


 - It shows useful information about the system from which the RAM dump was taken, such as the number of processors, the system time at the time of imaging, system root, and major operating system version.
 - Retrieving memory image system information is usually the first thing forensic investigators do. Let's save this output as "20240712.mem.info.txt" using a 'greater than' sign.
 - Run the command: `py vol.py -f "C:<FilePath>\20240712.mem" windows.info > 20240712.mem.info.txt`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/iIkfaPL.png">
<br />
<br />
<br />
<br />

 - Check the folder to ensure the file has been dumped properly.

<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/kvm9VpX.png">
<br />
<br />
<br />
<br />


 - Useful tip: If you want to see all the commands that are available, you can type: `py vol.py -f "C:<FilePath>\20240712.mem" windows`
 - By not specifying the entire command and only typing the plugin `windows`, Volatility 3 will give you an error and list all the available modules.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/mmYwyq6.png">
<br />
<br />
<br />
<br />


 - You can also use the `-h` flag to get help with all the commands. `py vol.py -h`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/RlfSFsw.png">
<br />
<br />
<br />
<br />


 - You can see all the commands available.

<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/0POCs7S.png">
<br />
<br />
<br />
<br />



---

[back to top](#toc)
## Analyzing Processes

<br />
<br />


 - Let's use the command: `py vol.py -f "C:<FilePath>\20240712.mem" windows.pslist | more` (This prints out all the processes that were running in Windows at the time of imaging.)
 - we're using PowerShell's pipeline `|` with the `more` command for a human-friendly interface (`more` is a Linux command but PowerShell also supports it)
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/4zU5ax4.png">
<br />
<br />
<br />
<br />

 - Once we get the process list, we can see 1- process ID, 2- Parent Process ID, 3- executable name, 4- offset (location it was found in the memory image), 5- number of threads that are currently running, 6- number of handles (for example, file handles), and more.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/T2u6Evk.png">
<br />


Using PPID and PID, we can reconstruct how applications were opened and where they came from. For example, the process "system" has a PPID of 0 because the system is one of the first processes that are started. You can see processes like "registry" and "smss.exe" have a PPID of 4, which means they were opened by the system process (PID: 4). Handles can give us a clue about what files the process was accessing at the time. (This is very useful especially when we're doing malware analysis) With ImageFileName and PID, we can filter out the noise and focus on specific programs we're interested in.

<br />
<br />
<br />
<br />


 - For example, let's say we're interested in all the "chrome" browser processes. We can use the ImageFileName we want to filter, and the PowerShell cmdlet Select-String (in Linux, we would use the 'grep' command): `py vol.py -f "C:<FilePath>\20240712.mem" windows.pslist | Select-String "chrome"`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/PVlxkKG.png">
<br />
<br />
<br />
<br />




 - We see a lot of chrome.exe processes. We can see the first chrome.exe has a PID of 1328 and is a parent of all the other chrome.exe processes. Let's dig deeper into the chrome.exe with the PID 1328.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/hcYlk7V.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Investigating File Handles 

<br />
<br />



 - Let's find out what files chrome (PID:1328) is currently accessing. Run: `py vol.py -f "C:<FilePath>\20240712.mem" windows.handles --pid 1328 | more`
 - We now see all the handles of the PID 1328.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/L5MwRVX.png">
<br />
<br />
<br />
<br />


 - We have all different types of handles, such as event types, IoCompletion (for I/O Operation) types, Key types (Registry), and file types. Though these are all interesting, we will focus on "File Types."
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/ne3gozk.png">
<br />
<br />
<br />
<br />



 - We can use the same Select-String PowerShell cmdlet using the keyword "File" `py vol.py -f "C:<FilePath>\20240712.mem" windows.handles --pid 1328 | Select-String File | more`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/nA8B7S1.png">
<br />
<br />
<br />
<br />

 - We have a list of all the files that Chrome was accessing. We also see some registry keys in the output because they have a file path that contains the string "File."

<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/EijD8nJ.png">
<br />
<br />
<br />
<br />


 - Let's change our query again. Filter with the string "history" to see chrome history files: `py vol.py -f "C:<FilePath>\20240712.mem" windows.handles --pid 1328 | Select-String File | Select-String history | more`
 - By adding more filters, we are narrowing down our search result!
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/vJaRVae.png">
<br />
<br />
<br />
<br />


 - We have three results; each file is associated with "history", "media history", and "history-journal"
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/FGiv8yI.png">
<br />
<br />
<br />
<br />



 - As we want to understand the Chrome browser history, we will analyze the first one. Copy the offset value 0xbf0f6abe9740.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/4PxYLbR.png">
<br />
<br />
<br />
<br />


 - We are going to extract (carve) the Chrome history file to the new directory "dump" that is located in the Volatility 3 folder, using the `windows.dumpfile` plugin with a `-o` (output) option.
 - `py vol.py -f "C:<FilePath>\20240712.mem" -o "dump" windows.dumpfile --pid 1328 --virtaddr 0xbf0f6abe9740`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/4ic1QQc.png">
<br />
<br />
<br />
<br />


 - Let's take a look at the file. Open the .dat file with HxD Hex Editor. https://mh-nexus.de/en/downloads.php?product=HxD20
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/3BRUPQs.png">
<br />
<br />
<br />
<br />


 - At the beginning, we see "SQLite format 3" which means this is a SQLite file; that means, in order to dig deeper, we will need to use a SQLite viewer
<p align="center">
<br/>
<img width="500" alt="Portfolio" src="https://i.imgur.com/EhNUJ1u.png">
<br />
<br />
<br />
<br />



 - Seeing a lot of URLs in the file, we can confirm this is a chrome history file.
<p align="center">
<br/>
<img width="500" alt="Portfolio" src="https://i.imgur.com/3NAedk2.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Examining Command Line Usage

<br />
<br />


 - Let's go back to PowerShell. Another interesting plugin we can experiment with is `windows.cmdline`.
 - The `windows.cmdline` plugin shows command line usage and switches (options) that were used whenever programs started. (With Windows Registry, we can only see the entire executable, but not the switches)
 - Run: `py vol.py -f "C:<FilePath>\20240712.mem" windows.cmdline | more`

<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/viR7SKt.png">
<br />
<br />
<br />
<br />



 - You can see the command line and also switches used.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/CwpDxHY.png">
<br />
<br />
<br />
<br />



 - We can see the last row shows that FTK imager.exe was run to capture the memory.
 - Using the `windows.cmdline` plugin is a really quick way to see what's been run, where things were run from, and if they had any special options while they were running.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/vnnVz6Q.png">
<br />
<br />
<br />
<br />



---

[back to top](#toc)
## Network Activity Analysis

<br />
<br />



 - Another important plugin we need to know is `windows.netstat`
 - Run: `py vol.py -f "C:<FilePath>\20240712.mem" windows.netstat | more`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/waIuvu4.png">
<br />
<br />
<br />
<br />


 - windows.netstat shows network activity information associated with memory processes. Let's take a look:
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/pJRcU66.png">
<br />

The list shows 1 - offset in the memory image, 2 - protocol that was used (e.g., TCPv4), 3 - local IP address, 4 - local port, 5 - external IP address, 6 - external port, 7 - connection (TCP) state, 8 - process ID, 9 - process name, and finally, a timestamp.

<br />
<br />
<br />
<br />



 - Let's say we are interested in where chrome.exe was connecting to.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/olPtEMz.png">
<br />
<br />
<br />
<br />


 - We can easily search for the foreign IP address 142.250.190.14. We know that Chrome was either opening or closing the connection to 1e100.net (a Google-owned domain used for Google server identification purposes)
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/YEPfYT8.png">
<br />
<br />
<br />
<br />


 - Don't forget we can also narrow down the list with PowerShell's `Select-String` cmdlet.
 - We can use the `windows.netstat` plugin to see if there is a malicious entity on the computer that is communicating with a malicious server.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/WmOBYb4.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Password Hash Extraction

<br />
<br />



 - Another plugin that is useful when we are trying to investigate a certain suspect and we want to know their password and maybe how that password is used on other systems, is `windows.hashdump`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/ajhICwU.png">
<br />
<br />
<br />
<br />



 - `windows.hashdump` will show all the hash values of the passwords of all the accounts used in the system. Once we know these hash values, we can try to crack them, and use them to gain access to the suspect's other local systems or online accounts.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/rLqCFOd.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Registry UserAssist Analysis

<br />
<br />


 - When performing a registry analysis, the `windows.registry.userassist` plugin can be useful. The UserAssist feature in Windows tracks the usage of executable files and applications launched by the user.
 - Let's run: `py vol.py -f "C:<FilePath>\20240712.mem" windows.registry.userassist | more`
 - Let's analyze user behavior associated with a program, PowerShell.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/FdaGKKk.png">
<br />

- (1) is a registry hive offset value
- (2) tells you what user account is being used.
- (3) is the last time that an action took place.
- (4) is the name of a program
- (5) is how many times the program was run
- (6) is a focus count: how many times a user focused on this particular window for the program
- (7) is the total amount of time the user was focusing on the window for the program.

<br />
<br />

So, according to the information we have, we can say, the last time the user John Doe ran PowerShell was 2021-04-30 17:52:18; the user John Doe ran PowerShell a total of 9 times, and went away from the PowerShell window and came back to it 31 times. The total amount of time he spent on PowerShell is about 16 mins.




<br />
<br />
<br />
<br />


 - Like any other plugins, we can use the `Select-String` PowerShell cmdlet to narrow down search results.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/lOJIIsS.png">
<br />
<br />
<br />
<br />


---

[back to top](#toc)
## Registry Hive Examination

<br />
<br />



 - If we want to get all of the registry files that are available in the system (in order to carve them out) `py vol.py -f "C:<FilePath>\20240712.mem" windows.registry.hivelist` will print out all the registry files that are available in the system.
 - Let's take a look at which options we can use for this particular plugin.
 - Run: `py vol.py -f "C:<FilePath>\20240712.mem" windows.registry.hivelist -h`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/YQiJfFt.png">
<br />

We can see there's a string filtering option we can use (that means we don't need to use the PowerShell cmdlet Select-String). We also have the `--dump` option for extracting the registry hive files.




 - Let's say we are interested in the user John Doe, and also want to see the file "ntuser.dat"
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/Txrr6No.png">
<br />
<br />
<br />
<br />



 - We can use the `--filter` option with the string "John Doe\ntuser.dat"
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/qUmdKC4.png">
<br />
<br />
<br />
<br />


 - We can add the `--dump` option to extract this particular file to the folder "dump"
 - Run: `py vol.py -f "C:<FilePath>\20240712.mem" -o "dump" windows.registry.hivelist --filter "John Doe/ntuser.dat" --dump`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/bTbgvua.png">
<br />
<br />
<br />
<br />


 - Check the file to see if it is dumped correctly.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/lqq8Kcs.png">
<br />
<br />
<br />
<br />



---

[back to top](#toc)
## Specific Registry Key Analysis

<br />
<br />


 - Let's say we have one specific key we want to check instead of dumping the entire registry file. We can use the `windows.registry.printkey` plugin.
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/YqEOXi0.png">
<br />
<br />
<br />
<br />


 - We can use the `--key` option with the value we want to search.
 - An example would be: `py vol.py -f "C:<FilePath>\20240712.mem" windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion" | more`
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/gNUKYdj.png">
<br />
<br />
<br />
<br />


 - We can see all of the keys inside "CurrentVersion" (If we want to also see subkey values, we can simply add the `--recurse` option.)
<p align="center">
<br/>
<img width="597" alt="Portfolio" src="https://i.imgur.com/VReZRFn.png">
<br />
<br />
<br />
<br />






\<end\>

[back to top](#toc)

<br />
<br />
<br />

