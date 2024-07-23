Hello all! Today i would be doing a walkthru of my solution for 119-KrakenKeyLogger! This challenge is about a digital forensics team was called in to investigate a data breach for a company which seems interesting =)

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
<img width="606" alt="q3a" src="https://github.com/user-attachments/assets/f17dc603-27ec-48f9-80fb-4961afdc5540">


