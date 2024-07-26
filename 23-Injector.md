Hello all! Today i would be doing a walkthru the cyberdefenders.org blue team CTF challenge for [23-Injector](https://cyberdefenders.org/blueteam-ctf-challenges/injector/)! This challenge is about a company's web server was compromised and IR team was activated to determine how the system was compromised! This challenge is quite interesting as we are given both Disk Image and Memory dump to investigate, what a privilege! =)
![image](https://github.com/user-attachments/assets/ca2f5acc-6fb8-46d1-a359-ebf464d06505)

### Question 1: What is the computer's name?
This is pretty straightforward, we just have to extract the **SYSTEM** hive then load into **RegistryExplorer** and navigate to **HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName** ANDDD we found the answer!

*Some of my favourite things about the RegistryExplorer which is the Available Bookmarks, it contains all the useful information you need!*

<img width="900" alt="qn1" src="https://github.com/user-attachments/assets/57dfb223-5e94-4ce1-ae08-f85625026449">

### Question 2: What is the Timezone of the compromised machine? Format: UTC+0 (no-space)?
Since we already have SYSTEM Hive loaded in RegistryExplorer, we just have to navigate to **HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation** and you have these timezone data
<img width="1027" alt="qn2" src="https://github.com/user-attachments/assets/8c2cbbb6-d1c1-42d4-9384-789216ebc594">
Using the capability of AI, we just have to input **ActiveTimeBias** & **TimeZoneKeyName** into ChatGPT ANDDD we found the answer!
<img width="820" alt="qn2a" src="https://github.com/user-attachments/assets/87ab6076-c1bf-479b-b7d6-9764090f9cf9">

### Question 3: What was the first vulnerability the attacker was able to exploit?
My first approach was to utilise the memory dump in volatility to see all the existing processes running *<I am using Volatility 3 Framework 2.7.0 in Windows Environment>*

```bash
> python vol.py -f memdump.mem windows.pstree.PsTree > 23pstree.txt
```

In the pstree result, i identified the process "mysqld.exe" is running and therefore assumed the vulnerability is SQL injection as this is a web-related attack.

![image](https://github.com/user-attachments/assets/b6a33dcb-1402-4838-94df-407be6679376)

Unfortunately i am wrong :(
Then i proceeded to navigate into **C:\inetpub** & **C:\xampp** directory as there two are web-hosting folder to see if there are any useful artefacts or logs. After like 1hr of digging, i managed to find something indicative in **C:\xampp\apache\logs\access.log**

If you do some online research on the most common web-security vulnerabilities, you will see things like SQL Injection, Cross-Site Request Forgery (CSRF), Cross-Site Scripting (XSS) and many more. Digging further on each vulnerability and the method to detect them, i came upon this article [What is XSS](https://portswigger.net/web-security/cross-site-scripting) from PortSwigger (*company that sells BurpSuite, the most widely used web application security testing software*) ANDDD we found the answer! - *<You have to refer the screenshot below to see what type of attack is it!>*
<img width="1405" alt="qn3b" src="https://github.com/user-attachments/assets/aba99c62-7ce1-4d16-9b2a-681947d6a262">

### Question 4: What is the OS build number?
Instead of the usual way which is finding the build number **HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\CurrentBuildNumber** in **SOFTWARE** hive, i looked for the file properties of **ntroskrnl.exe** in **C:\Windows\System32** folder ANDDD we found the answer! *<ntoskrnl.exe is responsible for a large number of Windows system operations. It’s involved in the execution of many system actions as well.>*

<img width="600" alt="qn4" src="https://github.com/user-attachments/assets/c0adcef0-ee74-48ef-9591-ad4d4aa2b7bf">

### Question 5: How many users are on the compromised machine?
Similar approach to the questions above, we have to load **SAM** hive in RegistryExplorer and look for **SAM\SAM\Domains\Account\Users** ANDDD we found the answer!
*The number of subkeys is equivalent to how many users are created.*
<img width="400" alt="qn5" src="https://github.com/user-attachments/assets/736de9ac-3351-420d-a88c-87f57a761053">

### Question 6: What is the webserver package installed on the machine?
This is a giveaway question which we already have the answer from our digging for Question 3..... SOOOOOOOO YESSSSS

### Question 7: What is the name of the vulnerable web app installed on the webserver?
To tackle this question, first we have to understand where do web application reside their files and after researching, we found out that the directory we should look into here is **"C:\xampp\htdocs** ANDD we found the answer! 

<img width="450" alt="qn7" src="https://github.com/user-attachments/assets/a04774eb-77af-4b61-8e3e-b066b3d41ffb">

### Question 8: What is the user agent used in the HTTP requests sent by the SQL injection attack tool?
This is pretty simple, you just have to research online for SQL Injection attack tools which you will find out there are jSQl, BBQSQL, SQLMap and many more tools out there. Then **ctrl+f** those keyword in the access.log (Referenced to Qn 3) ANDDD we found the answer!
<img width="700" alt="qn8" src="https://github.com/user-attachments/assets/0fd42e20-95d1-4ccb-a652-b17c4c541a97">

### Question 9: The attacker read multiple files through LFI vulnerability. One of them is related to network configuration. What is the filename?
LFI stands for Local File Intrusion, this happened when web application includes a file as user input without properly validating it. This vulnerability allowed attackers to include malicious files by manipulating the input. Example "https://example.com/?page=test.php" is vulnerable to LFI attack. Normally attackers just need to replace the filename by using **"../"**, therefore we just have to **ctrl+f "../"** in access.log ANDDD we found the answer!

<img width="1200" alt="qn9" src="https://github.com/user-attachments/assets/429f4bdd-4afb-4255-97d1-c5a2e28f4a2f">

### Question 10: The attacker tried to update some firewall rules using netsh command. Provide the value of the type parameter in the executed command?
The approach is pretty straightforward, just run "cmdline" in volatility to see all the command history
```bash
> python vol.py -f memdump.mem windows.cmdline.CmdLine > 23cmdline.txt
```
<img width="760" alt="qn10" src="https://github.com/user-attachments/assets/5de7fea3-7662-47ac-9227-d75c22ab66d0">

However using cmdline plugin, we unable to see any netsh command, therefore i had strings the memdump and **ctrl+f netsh firewall** ANDDD we found the answer!
```bash
> strings64.exe memdump.mem > 23strings.txt
```

<img width="450" alt="10b" src="https://github.com/user-attachments/assets/39fddae1-fe31-422d-8337-4902e4464353">

### Question 11: How many users were added by the attacker?
Referencing from Qn 5, we just have to differentiate datetime of the key creation of the user ANDDD we found the answer! *(Everytime a user was created, they will create a key therefore if we noticed that the datetime of the key creation of a certain user is different from the first user this means that the user was created subsequently!)*

<img width="450" alt="qn11" src="https://github.com/user-attachments/assets/2718b791-5d11-40fd-9601-624affcb6799">

### Question 12: When did the attacker create the first user?
The answer is in the screenshot above, look for the filecreation of the key! (Reference to Question 11 process!)

### Question 13: What is the NThash of the user's password set by the attacker?
Pretty straightforward, just run "hashdump" in volatility to dump all the lmhash and nthash of the users ANDDD we found the answer!
```bash
> python vol.py -f memdump.mem windows.hashdump.Hashdump > 23hashdump.txt
```
<img width="570" alt="qn13" src="https://github.com/user-attachments/assets/573c6ca4-3672-4329-a30d-3c938e605f40">

### Question 14: What is The MITRE ID corresponding to the technique used to keep persistence?
Referencing to questions above, we know what is the technique used by the attacker for persistency which is creating account to maintain access to victim's system and referencing for the [MITRE website](https://attack.mitre.org/tactics/TA0003/) ANDDD we found the answer!

### Question 15: The attacker uploaded a simple command shell through file upload vulnerability. Provide the name of the URL parameter used to execute commands?
Since the question already told us is a command shell thru file upload, i proceed to **ctrl+f shell** in **access.log** and we found a file named **phpshell.php** that located in **"dvwa/hackable/uploads/"** in **C:\XAMPP**.
<img width="880" alt="qn15" src="https://github.com/user-attachments/assets/dd266497-760a-47c3-bbbe-8382c1a53661">






