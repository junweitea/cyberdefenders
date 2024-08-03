Hello all! Today i would be doing a walkthru of one of the Blue Team CTF challenge in cyberdefenders.org [120-NintendoHunt](https://cyberdefenders.org/blueteam-ctf-challenges/nintendohunt/)!This challenge is about investigating a potential security breach at a company, we are given a memory dump to do our analysis!
![details](https://github.com/user-attachments/assets/006ac25a-59e6-4df8-8d95-7a5f63acc644)

### Question 1: What is the process ID of the currently running malicious process?
As usual, first question is the easiest which we have to look for malicious process therefore we just have to run **pslist plugin** to look for the malicious process! However the question also gave us a hint which is **running process** which even streamlined our search!

```bash
> python vol.py -f memdump.mem windows.pslist.Pslist > 120pslist.txt
```

*Hint: You have to first understand basic of windows, like only certain process can only be spawn by a certain process (parent-child process)! **KNOW NORMAL FIND EVIL*** 

*Since the question also mentioned running process, therefore you should filter away process that had alreay exited!*

### Question 2: What is the md5 hash hidden in the malicious process memory?
Since we know the malicious process, we have to dump the process out and strings for further analysis

```bash
> python vol.py -f memdump.mem windows.pslist.Pslist --pid XXXX --dump
> strings64.exe -n 32 XXXX.svchost.exe.0x7ff65b2d0000.dmp > 32string.txt
```
*Hint: You can use the argument of **"-n 32"** in string to only include the minimum string length which md5 hash is 32 characters!*

We would find this chunk of strings **"t.h.e. fl.ag.is. M2ExOTY5N2YyOTA5NWJjMjg5YTk2ZTQ1MDQ2Nzk2ODA="** and obviously is in base64 which we would put in cyberchef with the recipe of **"From Base64"** ANDDDD we found the answer! 

### Question 3: What is the process name of the malicious process parent?
Giveaway question! Since we already know the malicious process just refer to our pslist text file!

### Question 4: What is the MAC address of this machine's default gateway?
This question is tricky as the question is looking for MAC address unlike other CTF challenge which usually asks for IP Address which then you can use netscan or netstat plugin. Therefore i utilised the power of AI which i asked ChatGPT and he mentioned about the printkey plugin which we can have value/data from Registry.

Therefore, i proceed to research online on what is the key we should dive into which i saw this online:
![image](https://github.com/user-attachments/assets/84ca1192-2c16-4bee-a5ef-2847e5f2e504)
```bash
> python vol.py -f mem.dump windows.registry.printkey.PrintKey --key "Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged
```
After running this plugin, we identify 1 subkey which we proceed to print all the information of this subkey
![qn4a](https://github.com/user-attachments/assets/64dfb357-24ad-4bcf-b2ed-b780f7b74331)


```
> python vol.py -f mem.dump windows.registry.printkey.PrintKey --key "Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged\010103000F0000F0080000000F0000F0E3E937A4D0CD0A314266D2986CB7DED5D8B43B828FEEDCEFFD6DE7141DC1D15D > 102mac.txt
```
![qn4](https://github.com/user-attachments/assets/617a094c-477c-4959-bed4-f44cba79580c)



