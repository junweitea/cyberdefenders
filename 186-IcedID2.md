Hello all! Today i would be doing a walkthru of one of the Blue Team CTF challenge in cyberdefenders.org [186-IceID2](https://cyberdefenders.org/blueteam-ctf-challenges/icedid-2/)!This challenge is about investigating a ransomware attack incident at a company, we are given a memory dump to do our analysis! 

![image](https://github.com/user-attachments/assets/0172053f-f1c0-4069-b643-0eff20f92d1c)

*PS: I had actually came out with a [volatility note](volatility.md) that i referenced when i doing memory forensic, in the note it contains things about what plugin i mainly used and what type of result we can get from different plugin! Definitely had included my approach of solving certain memory forensics related question! =)*

### Question 1: Understanding the entry point of the malware is crucial for analyzing the attack vector. Can you specify the filename of the .iso file that was used to deliver the malicious payload?
Since we are looking for a particular file with format of ".iso", we can just use the **filescan plugin and ctrl+f** to look for the malicious payload ANDDD we found the answer!
```bash
> python vol.py -f memory.mem windows.findscan.FileScan > 186allfiles.txt
```
### Question 2: The initial delivery of the malware is crucial for understanding the attack vector. What is the link used to view the malicious malware? 
My approach to this question is using **pstree plugin** to view which browser was running and dump the file out for further analysis.
```bash
> python vol.py -f memory.mem windows.pstree.PsTree > 186pstree.txt
```
We found out that there are 2 browser process running: msedge.exe (PID 7068) and chrome.exe (PID 8464)

![image](https://github.com/user-attachments/assets/abfacb63-c391-4281-90f2-00291343044b)

We then proceed dump out both process for further analysis and ctrl+f to look for possible link.
```bash
> python vol.py -f memory.mem windows.pslist.PsList --pid 7068 --dump
> strings64.exe -a 7068.msedge.exe.0x7ff6fff50000.dmp > edge.txt
```
```bash
> python vol.py -f memory.mem windows.pslist.PsList --pid 8464 --dump
> strings64.exe -a 8464.chrome.exe.0x7ff76a020000.dmp > chrome.txt
```
However we unable to find the answer :(

Therefore i strings the whole memory dump and tried to **ctrl+f "https://"** ANDDD we found the answer!
```bash
> strings64.exe -a memory.dump > 189strings.txt
```

### Question 3: Identifying the storage location of a rogue process is critical for assessing its origin and purpose within a compromised system. What is the directory path where this process is located on the workstation?
This is a giveaway question! Since we already found the file in our Filescan string file in question 1!

### Question 4: To track the timeline of the attack, it is essential to know when the malware was dropped on the system. What is the download date and time in UTC of the malicious file on the affected device?
My first approach is to look in MFT to see the file creation date which we i used the MFTScan Plugin
```bash
> python vol.py -f memory.mem windows.mftscan.MFTScan > 186mftscan.txt
```
However in the MFTscan result, i only able to find **docs_invoice_173.iso.lnk** and not the iso file directly :(
![image](https://github.com/user-attachments/assets/e3db52a1-36cc-4354-a3d3-553ce0ab7a3b)
I also went to look for the ADS as it contains Identifier information like the source of the file for their downloaded files however still unable to find the answer.

I continue to research on the possible way of how we can find the answer and with the help of the other cyberdefenders, they introduce this tool **[MemProcFS](https://github.com/ufrisk/MemProcFS)** by ufrisk! **AND TO MY AMAZEMENT, THIS TOOL IS A GAME CHANGER FOR MEMORY FORENSICS!!!!!** Basically with MemProcFS, it will mount your memory dump in a virtual file system with all processes info and it's handle, file in the memory, registry hives, $MFT or web connections! It's like you are browsing the memory with File Explorer!
```bash
> MemProcFS.exe -f memory.dmp -forensic 1
```
![image](https://github.com/user-attachments/assets/f4606d92-2de0-4d5f-a615-e3623f09168b)
![image](https://github.com/user-attachments/assets/99a046d4-a6d7-4936-b8e8-3182d0950309)

Since we already know the filepath of the malware and with the help of MemProcFS, we can just navigate to the malware's file property ANDDDD we found the answer!

![image](https://github.com/user-attachments/assets/91632e53-e521-4abf-8e7f-0d8e7b6d6c81)

### Question 5: Determining the root of the malicious activity is essential for comprehending the extent of the intrusion. What is the malicious command that triggered this malicious behavior?
Since the question already dropped us a hint that we are looking for command, we just have to use the **CmdLine plugin** ANDDDD we found the answer!
```bash
> python vol.py -f memory.mem windows.cmdline.CmdLine > 186cmdline.txt
```
*PS: You must have some windows background knowledge in order to find the answer as certain process with certain argument will trigger certain action! And if you don't understand the process and it's respective argument just Google!*
*Hint: https://stmxcsr.com/micro/rundll-parse-args.html*

### Question 6: identifying file indicators is crucial for a comprehensive forensic analysis. What is the SHA256 hash of the DLL associated with the last execution of the malware?
If you had found the answer in question 5, we would already know what is the DLL associated with the malware! And with the help of MemProcFS, we are able to see all the DLL used for each process in File Explorer ANDDD we found the answer!
![image](https://github.com/user-attachments/assets/e9756be6-8c18-4b8d-886c-9b8436b29c38)

*PS: Actually we can also use **ldrmodules plugin** in volatility to list all DLLs in each process and dump the respective DLL to hash for the question!

### Conclusion
I would rate the difficulty of this challenge as 8/10. Some of the questions are not straightforward, and not all plugins can provide a direct answer. The individual must apply critical thinking and make connections to solve them effectively. This challenge also requires a solid understanding of Windows and its native processes. Using MemProcFS is extremely helpful for answering the questions. Overall, this challenge is engaging and encourages deeper analysis and investigation, making it enjoyable for analysts!

### Learning Point
1) MemProcFS really change how a analyst can perform memory forensic!
