---
layout: post
title: Manjusaka: A new RAT implant malware
categories: Miscellaneous
---

## Manjusaka: A new RAT implant malware (h2)

Researchers with the Cisco Talos Intelligence Group have recently discovered a new family of RAT implant malware called Manjusaka being used in the wild. Advertised as an imitation of the Cobalt Strike framework, Manjusaka is a fully functional command and control (C2) framework written in GoLang with a GUI in Simplified Chinese.

Utilizing malicious Word documents (maldocs) to deliver beacon implants on infected systems, Manjusaka is freely available on GitHub and allows adversaries to easily generate new implants with custom configurations, increasing the likelihood for this framework to be widely adopted by threat actors.

A C2 server binary was also discovered available on GitHub, with features that allow adversaries to monitor and administer an infected endpoint, with additional capabilities to generate Rust implant payloads for Windows and Linux.

## Distribution, Attribution, Initial Access (h2)

It's important to distinguish between the developer of the malware and campaign operators. Since the C2 binary is fully functional, self contained and publicly available on GitHub, any threat actor could have downloaded it and used it in the campaign analyzed by Talos researchers. Thus, no formal attribution to any threat actors have yet been made.  

In the Manjusaka campaign observed by Talos researchers, maldocs were discovered baited with news of a COVID-19 outbreak in Golmud City (Qinghai Province) which included a detailed timeline of the supposed outbreak.

### Installation and Maintaining Persistence (h3)

The maldoc payload is executed in three stages:

Stage 1 is the initial VBA macro contained within the maldoc which executes rundll32.exe and injects Metasploit shellcodes into the process, downloading files from a remote location to proceed with Stage 2. The Stage 1 shellcode observed by Talos researchers reached out to 39[.]104[.]90[.]45/2WYz.

The Stage 2 payload downloaded from the remote location is another shellcode that consists of a XOR-encoded Cobalt Strike executable, along with shellcode to decode the executable and load the Cobalt Strike beacon into memory.

In Stage 3, the Cobalt Strike beacon executable is decoded by the previous stage and then executed. The beacon can reflectively load itself into the memory of the current process.


### Operational Details (h3)

The C2 is an ELF binary file written in GoLang, while the implants are written in the Rust programming language as EXE and ELF versions, which can allow adversaries to remotely control the infected endpoint and execute arbitrary commands. 

The malware implant sample observed by Talos researchers makes HTTP requests to a fixed address http[:]//39[.]104[.]90[.]45/global/favicon.png

The C2 reply is always the same, consisting of five bytes: 0x1a1a6e0429.

Based on the request and accompanying data received from the C2 server, the implant can allow the following functions to be executed on the infected endpoint:

- Execute arbitrary commands on the system using "cmd.exe /c".
- Get file information for a specified file.
- Get information about current network connections (TCP and UDP) established, including local network addresses, remote addresses and  Process IDs (PIDs).
- Collect browser credentials:
- Specifically for Chromium-based browsers using the query: SELECT signon_realm, username_value, password_value FROM logins ;
- Browsers targeted: Google Chrome, Chrome Beta, Microsoft Edge, 360 (Qihoo), QQ Browser (Tencent), Opera, Brave and Vivaldi.
- Collect Wi-Fi SSID information: netsh wlan show profile <WIFI_NAME> key=clear
- Take screenshots of the current desktop.
- Obtain comprehensive system information from the endpoint, including:
- System memory global information.
- Processor power information.
- Current and critical temperature readings from WMI using "SELECT * FROM MSAcpi_ThermalZoneTemperature"
- Information on the network interfaces connected to the system: Names
- Process and System times: User time, exit time, creation time, kernel time.
- Process module names.
- Disk and drive information: Volume serial number, name, root path name and disk free space.
- Network account names, local groups.
- Windows build and major version numbers.
- Activate the file management module to carry out file-related activities.

The file management capabilities of the implant include:
- File enumeration: List files in a specified location on disk. This is essentially the "ls" command.
- Create directories on the file system.
- Get and set the current working directory.
- Obtain the full path of a file.
- Delete files and remove directories on disk.
- Move files between two locations. Copy the file to a new location and delete the old copy.
- Read and write data to and from the file.


### Indicators of Compromise (h3)
#### Hashes (h4)
Maldoc and CS beacon samples
58a212f4c53185993a8667afa0091b1acf6ed5ca4ff8efa8ce7dae784c276927
8e7c4df8264d33e5dc9a9d739ae11a0ee6135f5a4a9e79c354121b69ea901ba6
54830a7c10e9f1f439b7650607659cdbc89d02088e1ab7dd3e2afb93f86d4915

Rust samples
8e9ecd282655f0afbdb6bd562832ae6db108166022eb43ede31c9d7aacbcc0d8
a8b8d237e71d4abe959aff4517863d9f570bba1646ec4e79209ec29dda64552f
3f3eb6fd0e844bc5dad38338b19b10851083d078feb2053ea3fe5e6651331bf2
0b03c0f3c137dacf8b093638b474f7e662f58fef37d82b835887aca2839f529b

C2 binaries
fb5835f42d5611804aaa044150a20b13dcf595d91314ebef8cf6810407d85c64
955e9bbcdf1cb230c5f079a08995f510a3b96224545e04c1b1f9889d57dd33c1

#### IPs (h4)
39[.]104[.]90[.]45

#### URLs (h4)
https[://]39[.]104[.]90[.]45/2WYz
http[://]39[.]104[.]90[.]45/2WYz
http[://]39[.]104[.]90[.]45/IE9CompatViewList.xml
http[://]39[.]104[.]90[.]45/submit.php

#### User Agents (h4)
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
Mozilla/5.0 (Windows NT 8.0; WOW64; rv:58.0) Gecko/20120102 Firefox/58
Mozilla/5.0 (Windows NT 8.0; WOW64; rv:40.0) Gecko
