Hello all! Today i would be doing a walkthru the cyberdefenders.org blue team CTF challenge for [23-Injector](https://cyberdefenders.org/blueteam-ctf-challenges/injector/)! This challenge is about a company's web server was compromised and IR team was activated to determine how the system was compromised! This challenge is quite interesting as we are given both Disk Image and Memory dump to investigate, what a privilege! =)
![image](https://github.com/user-attachments/assets/ca2f5acc-6fb8-46d1-a359-ebf464d06505)

### Question 1: What is the computer's name?
This is pretty straightforward, we just have to extract the **SYSTEM** hive then load into **RegistryExplorer** and navigate to **HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName** ANDDD we found the answer!

*Some of my favourite things about the RegistryExplorer which is the Available Bookmarks, it contains all the useful information you need!*
<img width="627" alt="qn1" src="https://github.com/user-attachments/assets/57dfb223-5e94-4ce1-ae08-f85625026449">

### Question 2: What is the Timezone of the compromised machine? Format: UTC+0 (no-space)?
Since we already have SYSTEM Hive loaded in RegistryExplorer, we just have to navigate to **HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation** and you have these timezone data
<img width="1027" alt="qn2" src="https://github.com/user-attachments/assets/8c2cbbb6-d1c1-42d4-9384-789216ebc594">
Using the capability of AI, we just have to input **ActiveTimeBias** & **TimeZoneKeyName** into ChatGPT ANDDD we found the answer!
<img width="570" alt="qn2a" src="https://github.com/user-attachments/assets/87ab6076-c1bf-479b-b7d6-9764090f9cf9">

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
<img width="905" alt="qn3b" src="https://github.com/user-attachments/assets/aba99c62-7ce1-4d16-9b2a-681947d6a262">

### Question 4: What is the OS build number?
Instead of the usual way which is finding the build number **HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\CurrentBuildNumber** in **SOFTWARE** hive, i looked for the file properties of **ntroskrnl.exe** in **C:\Windows\System32** folder ANDDD we found the answer! *<ntoskrnl.exe is responsible for a large number of Windows system operations. It’s involved in the execution of many system actions as well.>*

<img width="600" alt="qn4" src="https://github.com/user-attachments/assets/c0adcef0-ee74-48ef-9591-ad4d4aa2b7bf">

### Question 5: How many users are on the compromised machine?
Similar approach to the questions above, we have to load **SAM** hive in RegistryExplorer and look for **SAM\SAM\Domains\Account\Users** ANDDD we found the answer!
*The number of subkeys is equivalent to how many users are created.*
<img width="400" alt="qn5" src="https://github.com/user-attachments/assets/736de9ac-3351-420d-a88c-87f57a761053">
