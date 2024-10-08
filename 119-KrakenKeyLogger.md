Hello all! Today i would be doing a walkthru the cyberdefenders.org blue team CTF challenge for [119-KrakenKeyLogger](https://cyberdefenders.org/blueteam-ctf-challenges/krakenkeylogger/)! This challenge is about a digital forensics team was activated to investigate a data breach for a company which seems interesting =)

<img width="627" alt="description" src="https://github.com/user-attachments/assets/d7742022-9905-443f-b704-78a5cada777f">


### Question 1: What is the web messaging app the employee used to talk to the attacker?
Normally, when asked about the program they used we can dive in these areas: 1) Desktop, 2) Downloads, 3) Program Files, 4) ProgramData and 5) AppData/Roaming or AppData/Local

After browsing all those locations and even deep-diving into browser artefacts, yet still unable to find the answer. Therefore the hint told us to look for **Windows Notification** for the answer and we proceed to read **wpndatabase.db** using *DB Browser for SQLite* at **"OMEN\AppData\Local\Microsoft\Windows\Notifications"** ANDDDDD we found the answer under **Notification Table**!

<img width="800" alt="q1" src="https://github.com/user-attachments/assets/bc9a7cb7-14ba-45e8-8dd0-a5c90ab4e19c">

### Question 2: What is the password for the protected ZIP file sent by the attacker to the employee?
We proceed to look for the protected ZIP file sent by the attacker which can be found in the Downloads Folder called **our project templet test.zip**, and referencing and correlating from our finding above from the notification screenshot we are able to identify the password.

### Question 3: What domain did the attacker use to download the second stage of the malware?
Postulating that the ZIP file sent by the attacker is malicious, we proceed to look what is inside the zip file
![image](https://github.com/user-attachments/assets/a472f2ef-a373-483a-949f-cdc75ca2fccd)

We identified a shortcut file **templet.lnk** which we used **LECmd** by EricZimmerman to decode and parse all available information in **templet.lnk**
```bash
> LECmd.exe -f "C:\Users\Junweitea\Desktop\119-KrakenKeyLogger\challenge\Users\OMEN\Downloads\project templet test\templet.lnk"
```
After parsing the data, we saw a powershell function:
<img width="808" alt="q3a" src="https://github.com/user-attachments/assets/f17dc603-27ec-48f9-80fb-4961afdc5540">

From this screenshot, we able to identify that the shortcut is using powershell to **wget a file from an encoded address** therefore we just have to put in PowerShell ISE and Write-Output of the encoded address ANDDD we found the answer!

<img width="724" alt="question 3" src="https://github.com/user-attachments/assets/773a2156-1201-4b36-8288-3bb5d7ef8a13">

### Question 4: What is the name of the command that the attacker injected using one of the installed LOLAPPS on the machine to achieve persistence?

First we have to understand what is LOLAPPS which is **Living Off The Land Applications** which i googled and basically is referring to a combination of applications that can be used to carry out day-to-day exploitation. And as you can see below is the applications used by Attacker/Hacker to exploit for either persistence, lateral movement or data exfiltration
![image](https://github.com/user-attachments/assets/8c39a4eb-8978-4c12-86ac-090ffde83636)

We then proceed to look for the LOLAPPS in the victim's machine and we found out that the victim used "Greenshot" which we found **Greenshot.ini** in **AppData/Roaming/Greenshot** and **ctrl+f Command** (Reason why we search command because the question is asking about the name of the command) ANDDDD we found the answer!

![image](https://github.com/user-attachments/assets/5b00ccab-2c90-41d0-be58-fe55516b8db9)

### Question 5: What is the complete path of the malicious file that the attacker used to achieve persistence?
The answer is basically in the screenshot above! 

### Question 6: What is the name of the application the attacker utilized for data exfiltration?
As mentioned above in Question 1, we can proceed to look for programs used in **AppData\Roaming** which we will see the program used for data exfiltration and do some open-research ANDDD we found the answer!
![image](https://github.com/user-attachments/assets/3c6acb4d-5bae-4391-a396-69c7ef64edce)

### Question 7: What is the IP address of the attacker?
Since we know the software used for data exfiltration, we can proceed to investigate in that particular folder and we saw **ad.trace** file which we open and **ctrl+f "\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b"** as this regex is to look for any IP Address ANDDDDD we found the answer!
![image](https://github.com/user-attachments/assets/520bd2b0-e7ab-4270-8652-c8483641fd18)

### Conclusion
I will rate the difficulty of this challenge 8/10, 2hr30mins for me to complete it! The reason is that due to question 1, I am unable to find the answer using the usual methods, such as looking in the AppData folder, Prefetch, or browser artefacts! Only with the hint did I realise that I might be able to find the answer through the notification banner, which makes sense because messaging apps normally have notifications. The rest of the questions are logical and manageable!

### Learning Point
1) Identify that notification banner might able to give clue on the user's action
2) The existence of LOLAPPS and their vulnerability that allows attackers/threat actors to leverage and exploit for their next course of action
