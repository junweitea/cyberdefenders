Hello all! Today i would be doing a walkthru of my solution for 119-KrakenKeyLogger! This challenge is about a digital forensics team was called in to investigate a data breach for a company which seems interesting =)

<img width="627" alt="description" src="https://github.com/user-attachments/assets/d7742022-9905-443f-b704-78a5cada777f">


### Question 1: What is the web messaging app the employee used to talk to the attacker?
Normally, when asked about the program they used we can dive in these areas: 1) Desktop, 2) Downloads, 3) Program Files, 4) ProgramData and 5) AppData/Roaming or AppData/Local

After browsing all those locations and even deep-diving into browser artefacts, yet still unable to find the answer. Therefore the hint told us to look for **Windows Notification** for the answer and we proceed to read **wpndatabase.db** using DB Browser for SQLite at **"OMEN\AppData\Local\Microsoft\Windows\Notifications"** ANDDDDD we found the answer under **Notification Table**

<img width="800" alt="q1" src="https://github.com/user-attachments/assets/15316a22-9202-4cea-8635-ff36ccb298a7">


