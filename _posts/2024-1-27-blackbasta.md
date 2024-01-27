---
layout: post
title: "Black Basta Ransomware"
categories: ["Threat Intel Reports"]
---

Trend Micro analyzed recent Black Basta ransomware group campaigns and observed it using the banking trojan QakBot to gain initial access and exploiting PrintNightmare vulnerability (CVE-2021-34527) to perform privileged file operations.

![](/images/manjusaka.PNG)

## Distribution and Initial Access

Black Basta has been observed using the banking trojan QakBot to gain initial access. QakBot is distributed using spear phishing emails attached with Excel files containing Excel 4.0 macros.

Once the recipient enables macros, QakBot DLL files are downloaded onto the host system, which is then executed via regsvr32.exe.


### Installation and Maintaining Persistence

The QakBot DLL proceeds to perform process injection using explorer.exe, after which the injected process creates a scheduled task to maintain persistence.

Qakbot then installs a Cobeacon, a Cobalt Strike beacon backdoor variant, establishing a named pipe for communication.

### Network Discovery and Defense Evasion

After QakBot has been installed, it proceeds to download and execute Cobeacon via a fileless PowerShell script containing several layers of obfuscation:
- It's first layer of obfuscation is a Base64-encoded PowerShell command, establishing a pipe for communication.
- The second layer of obfuscation involves loading and reading of an archive file in memory
- The third layer of obfuscation contains the decoded script for running the Base64-encoded shellcode

### Privilege Escalation

In addition to OakBot and Cobeacon, Black Basta has also been observed exploiting the PrintNightmare vulnerability which targets the Windows Print Spooler Service (spoolsv.exe) to deliver its payload (spider.dll) and perform escalated

### Lateral Movement

Black Basta threat actors were observed using the Coroxy backdoor alongside the network utility tool Netcat to move laterally across the network.

### Data Exfiltration

The Cobeacon backdoor establishes a pipe for communication which may be possibly used for data exfiltration purposes once information has been collected from a targeted system.
The Coroxy backdoor in conjunction with netcat is also another possible communication channel.

### Operational Details

Once the attackers gained a wide foothold in the target network, they executed the Black Basta ransomware to encrypt files while the infected system is in safe mode, appending the encrypted files with the .basta extension. Black Basta ransomware injects into an existing Windows service and launches its decryptor executable.

The ransomware then utilizes the ChaCha20 algorithm to encrypt files. Each folder on the encrypted drive contains a readme.txt file containing information about the attack and links along with a unique ID to enter a negotiation chat session with the threat actors. The wallpaper is changed to display: “Your network is encrypted by the Black Basta group.”

#### Threat Actor Objectives 
Black Basta ransomware group employs a double extortion scheme that involves stealing confidential data before encrypting it so they can threaten victims with the public release of the stolen data if payment is not made within seven days of the attack.
To fulfil their extortion objectives, they release this information on their Tor website, Basta News, if the victim does not pay the ransom.



## Indicators of Compromise
### Hashes       
|Sha256                                                          | TrendMicro Detection Signature  |
|----------------------------------------------------------------|---------------------------------|
|01fafd51bb42f032b08b1c30130b963843fea0493500e871d6a6a87e555c7bac|Ransom.Win32.BLACKBASTA.YXCEP    | 
|72a48f8592d89eb53a18821a54fd791298fcc0b3fc6bf9397fd71498527e7c0e|Trojan.X97M.QAKBOT.YXCFH         |   
|580ce8b7f5a373d5d7fbfbfef5204d18b8f9407b0c2cbf3bcae808f4d642076a|Backdoor.Win32.COROXY.YACEKT     |    
|130af6a91aa9ecbf70456a0bee87f947bf4ddc2d2775459e3feac563007e1aed|Trojan.Win64.QUAKNIGHTMARE.YACEJT|    
|c7eb0facf612dbf76f5e3fe665fe0c4bfed48d94edc872952a065139720e3166|TrojanSpy.Win32.QAKBOT.YXCEEZ    |
|ffa7f0e7a2bb0edf4b7785b99aa39c96d1fe891eb6f89a65d76a57ff04ef17ab|TrojanSpy.Win32.QAKBOT.YACEJT    |
|2083e4c80ade0ac39365365d55b243dbac2a1b5c3a700aad383c110db073f2d9|TrojanSpy.Win32.QAKBOT.YACEJT    |
|1e7174f3d815c12562c5c1978af6abbf2d81df16a8724d2a1cf596065f3f15a2|TrojanSpy.Win32.QAKBOT.YACEJT    |
|2d906ed670b24ebc3f6c54e7be5a32096058388886737b1541d793ff5d134ccb|TrojanSpy.Win32.QAKBOT.YACEJT    |
|72fde47d3895b134784b19d664897b36ea6b9b8e19a602a0aaff5183c4ec7d24|TrojanSpy.Win32.QAKBOT.YACEJT    |
|2e890fd02c3e0d85d69c698853494c1bab381c38d5272baa2a3c2bc0387684c1|TrojanSpy.Win32.QAKBOT.YACEJT    |
|c9df12fbfcae3ac0894c1234e376945bc8268acdc20de72c8dd16bf1fab6bb70|Ransom.Win32.BLACKBASTA.YACEJ    |
|8882186bace198be59147bcabae6643d2a7a490ad08298a4428a8e64e24907ad|Ransom.Win32.BLACKBASTA.YACEJ    |
|0e2b951ae07183c44416ff6fa8d7b8924348701efa75dd3cb14c708537471d27|Ransom.Win32.BLACKBASTA.YACEJ    |     
|0d3af630c03350935a902d0cce4dc64c5cfff8012b2ffc2f4ce5040fdec524ed|Ransom.Win32.BLACKBASTA.YACEJ    |              
|df35b45ed34eaca32cda6089acbfe638d2d1a3593d74019b6717afed90dbd5f8|Ransom.Win32.BLACKBASTA.YACEJ    |          
|3fe73707c2042fefe56d0f277a3c91b5c943393cf42c2a4c683867d6866116fc|Ransom.Win32.BLACKBASTA.YACEJ    |  
|433e572e880c40c7b73f9b4befbe81a5dca1185ba2b2c58b59a5a10a501d4236|Ransom.Win32.BLACKBASTA.A.note   |
|c4683097a2615252eeddab06c54872efb14c2ee2da8997b1c73844e582081a79|PUA.Win32.Netcat.B               |  

#### IPs 
24[.]178[.]196[.]44:2222
37[.]186[.]54[.]185:995
39[.]44[.]144[.]182:995
45[.]63[.]1[.]88:443
46[.]176[.]222[.]241:995
47[.]23[.]89[.]126:995
72[.]12[.]115[.]15:22
72[.]76[.]94[.]52:443
72[.]252[.]157[.]37:995
72[.]252[.]157[.]212:990
73[.]67[.]152[.]122:2222
75[.]99[.]168[.]46:61201
103[.]246[.]242[.]230:443
113[.]89[.]5[.]177:995
148[.]0[.]57[.]82:443
167[.]86[.]165[.]191:443
173[.]174[.]216[.]185:443
180[.]129[.]20[.]53:995
190[.]252[.]242[.]214:443
217[.]128[.]122[.]16:2222

#### URLs 
elblogdeloscachanillas[.]com[.]mx/S3sY8RQ10/Ophn[.]png
lalualex[.]com/ApUUBp1ccd/Ophn[.]png
lizety[.]com/mJYvpo2xhx/Ophn[.]png

#### MITRE ATT&CK® Techniques
|Tactic                                   |Technique ID          |Technique Name  
|-----------------------------------------|----------------------|----------------------------------------------------------------------------------------|                       
|Execution                                |T1059                 |Command and Scripting Interpreter                                                       |   
|Defence Evasion                          |T1112,T1027,T1562.001 |Modify Registry,Obfuscated Files or Information,Impair Defences: Disable or Modify Tools|          
|Discovery                                |T1082,T1083           |System Information Discovery,File and Directory Discovery                               | 
|Impact                                   |T1490,T1489,T1486     |Inhibit System Recovery,Service Stop,Data Encrypted for Impact                          |