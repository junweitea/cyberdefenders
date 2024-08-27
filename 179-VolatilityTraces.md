Hello all! Today i would be doing a walkthru of one of the Blue Team CTF challenge in cyberdefenders.org [179-VolatilityTraces](https://cyberdefenders.org/blueteam-ctf-challenges/volatility-traces/)!This challenge is about analysing the action of a malware from a cyber incident in a multinational corporation, we are given a memory dump to do our analysis!
![details](https://github.com/user-attachments/assets/ec4230b3-8ad9-4589-b756-43d6417952c2)

### Question 1: Identifying the parent process reveals the source and potential additional malicious activity. What is the name of the suspicious process that spawned two malicious PowerShell processes?
Very straightforward question, just have to use **PsTree Plugin** ANDDDD we managed to find out the PID (4596) that spawn the two malicious PowerShell processes however we unable to find the process in pslist. 
```bash
> python vol.py -f memdump.mem windows.pslist.Pslist > 178pstree.txt
```
Therefore, we proceed to use **PsScan Plugin** and we managed to find out the process name however it was not in full therefore we just take a look at the *Path of the powershell.exe process* at the Pstree text file ANDDD we found the answer!
```bash
> python vol.py -f memdump.mem windows.psscan.PsScan > 178psscan.txt
```
_PS: The reason why PsScan is able to find the process because it directly scan the physical or virtual process blocks in memory which also has the ability to find terminated or disconnected (hidden) processes as well._

### Question 2: By determining which executable is utilized by the malware to ensure its persistence, we can strategize for the eradication phase. Which executable is responsible for the malware's persistence?
You can find the answer just by identifying what process that PID 4596 spawning ANDDD we found the answer!
![qn2](https://github.com/user-attachments/assets/451c1113-e063-402f-9052-a43e922fa6f0)

### Question 3: Understanding child processes reveals potential malicious behavior in incidents. Aside from the PowerShell processes, what other active suspicious process, originating from the same parent process, is identified?
Giveaway answer! Just have to use the same approach from Question 2!

### Question 4: Analyzing malicious process parameters uncovers intentions like defense evasion for hidden, stealthy malware. What PowerShell cmdlet used by the malware for defense evasion?
Just analysing the *Path of the powershell.exe process* at the Pstree text file ANDDDD we found the answer!
![qn4](https://github.com/user-attachments/assets/b78fd8f7-0bd7-43cc-908d-957081aca7ba)

### Question 5: Recognizing detection-evasive executables is crucial for monitoring their harmful and malicious system activities. Which two applications were excluded by the malware from the previously altered application's settings?
Giveawy answer again! Reference to Question 4!
![qn5](https://github.com/user-attachments/assets/e9672347-d558-43a4-bc24-a17aa225c496)

### Question 6: Mapping each technique to MITRE provides clarity and aids effective response during incident analysis. What is the MITRE sub-technique ID the PowerShell commands aim to achieve?
Straightforward! A hint for you! Impair Defense T1562 - https://attack.mitre.org/techniques/T1562/

### Question 7: SIDs uniquely identify accounts, reveal type, domain/local status, and correlate malicious activities. What's the Security ID (SID) of the user account the malicious processes are running under?
From Question 4-5, based on the path we already know the malware is residing at which **Lee User** folder and we have to use **GetSIDs Plugin** to view which user and their SIDs executed the process ANDDDD **ctrl+f powershell.exe** we found the answer!
![qn7](https://github.com/user-attachments/assets/63dbd0d8-aaeb-45bf-954d-6fe3b1f5f84d)

### Conclusion
I will rate the difficulty of this challenge 2/10, 30mins for me to complete it! It’s quite straightforward, with most questions being simple and requiring just one or two plugin to find the answers. The questions are mostly independent, with minimal need for cross-referencing.
