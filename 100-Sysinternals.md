Hello all! Today i would be doing a walkthru one of the Blue Team CTF challenge in cyberdefenders.org [100-Sysinternals](https://cyberdefenders.org/blueteam-ctf-challenges/sysinternals/)! This challenge is about analysing a laggy windows system after downloading a possibly malicious Sysinternals suite!

![main](https://github.com/user-attachments/assets/544da146-0ce2-42f7-9dbb-942c964993c1)

### Question 1: What was the malicious executable file name that the user downloaded?
This is pretty straightforward, we can just proceed to navigate to User's Downloads, Desktop or even Documents directory ANDDD we found the answer under **C:\Public\Downloads**!!!
![qn1](https://github.com/user-attachments/assets/70b164dd-38b4-4e45-a13f-ad82961a8018)

### Question 2: When was the last time the malicious executable file was modified? 12-hour format?
This is a giveaway question, since you already found the malicsious executable file! *(Please do take note that the answer format is in (MM-DD-YYYY XX:XX:XX PM)*

### Question 3: What is the SHA1 hash value of the malware?
We can't just export the malware out and hash it as the malware was modified therefore we should use **AmcacheParser** by our favourite Eric Zimmerman to parse the data from **C:\Windows\appcompat\Prrograms\Amcache.hve**! The reason is because Amcache.hve is an artifact that contains useful metadata such as to program execution and installation, and the most importantly is that..... is **contains SHA1 hash for all program that was executed**!!!
```bash
> AmcacheParser.exe -f Amcache.hve -i --csv 101amcache.csv
```
After parsing the amcache.hve, you would have all files below
![qn3a](https://github.com/user-attachments/assets/e520805d-1fef-4eda-8e85-c0cd66e77e16)

Therefore you just have to view the data in **UnassociatedFileEntries** ANDDDD we found the answer!
![qn3](https://github.com/user-attachments/assets/4397c0c0-181f-4927-aba9-0c0daba807db)

### Question 4: What is the malware's family?
Since you already have the SHA1 hash of the malware we should just utilise open-source research by inputing the hash into virustotal ANDDD we found the answer! *(Malware family normally is a unique name/title to group malwares based on their common characteristics and tactics, techniques and procedures (TTP))*
![qn4](https://github.com/user-attachments/assets/3222c1d3-0a08-4f8a-bdf5-c56d88efbeca)

*(Just to add on, one of my favourite method to lookup for malware family is using https://bazaar.abuse.ch/browse/ ! This website contains malware database which you can map your malware or submit hash to look for more information about that malware or search for malware family in it!)*

### Question 5: What is the first mapped domain's Fully Qualified Domain Name (FQDN)?

Continue to dig using virustotal, you can go to **RELATIONS** tab and look for **Contacted Domains** ANDDD we found the answer!

![qn5](https://github.com/user-attachments/assets/74c63c9c-169c-4dc8-9939-5d9b6e506025)

### Question 6: The mapped domain is linked to an IP address. What is that IP address?
I tried to continue to dig in virustotal however it only shows us Contacted IP Address but do not have the mapping of domain to our IP Address. Then i pivot to **C:\Windows\System32\drivers\etc\hosts** as this is the file in Windows that map IP Address to hostname ANDDD we found the answer!

![qn6](https://github.com/user-attachments/assets/1cbc3236-83e9-4701-a1db-63251541c7fe)

### Question 7: What is the name of the executable dropped by the first-stage executable?
Continue to dig in virustotal as virustotal did provide a simple dynamic analysis on the malware therefore you just need to look for executable (.exe) created in **BEHAVIOR** tab ANDDD we found the answer!
![qn7](https://github.com/user-attachments/assets/7d49f6e6-46c4-43bd-b493-13284ce5273e)


### Question 8: What is the name of the service installed by 2nd stage executable?
Just reference from the screenshot above!

### Question 9: What is the extension of files deleted by the 2nd stage executable?
At first, i digged into the **Files Deleted** section in virustotal which i realised most of the extension which the malware deleted are .txt .csv .cookie files. However those are not the answer :(

I then proceed to look for the 2nd stage executable in C:\Windows and perform simple static analysis to see if able to find the answer.












