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
This is a giveaway question! Since we already found the file in our string file in question 1!


