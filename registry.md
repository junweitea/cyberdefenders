Hello all! This is just my note that i referenced to look for artefacts/information in Registry! And of course using [Registry Explorer](https://ericzimmerman.github.io/#!index.md/)! by the awesome Eric Zimmerman!

Registry is a very useful artefact that Incident Responder or Analyst can use to obtain important information from the system! *Registry is basically a database that contains alot of system information which using the approach can navigate to the data you finding!*  

I am going to categorise the artefacts/information in Registry using the category below:
1) Persistency
2) User Activity
3) System Information

## Persistency
*The most important area to dive in as persistency are techniques most commonly used by Attacker/ Threat Actor to ensure their malware or programs was executed/ran on the system everytime the system was turn on!*
#### Run/RunOnce Key
> SOFTWARE: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
> 
> SOFTWARE: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
> 
> SOFTWARE: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServices
> 
> SOFTWARE: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce
>
> SOFTWARE: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run
>
> NTUSER.dat: Software\Microsoft\Windows\CurrentVersion\Run

#### Startup-Folder
> SOFTWARE: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders

#### Scheduled Tasks
> SOFTWARE: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache

## User Activity
*Puzzle to piece up the incident or timeline!*
#### User Assist (Tracks the files and executable ran by the user)
> NTUSER.dat: Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist

#### Typed Path/URLs (Word that user typed into the Start Menu or Explorer bar)
> NTUSER.dat: Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths
> 
> NTUSER.dat: Software\Microsoft\Internet Explorer\TypedURLs

#### Recent Docs
> NTUSER.dat: Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs

#### WordWheelQuery (Word that searched in File Explorer)
> NTUSER.dat: Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery

#### Shellbag Forensics (Store metadata about folders and directories that a user has accessed, modified, or interacted with)
> Load up USRClass.dat/NTUser.dat into ShellBag Explorer

## System Information
*Important/useful information you might need!*
#### ComputerName
> SOFTWARE: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\ComputerName\

#### Timezone
> SYSTEM: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\ActiveTimeBias
>
> SYSTEM: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\TimeZoneKeyName

#### IP Address
> SYSTEM: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces

#### Network Adapter & Mac Address
> SYSTEM: HKEY_LOCAL_MACHINE\SYSTEM\Control\NetworkSetup2\Interfaces\\{NetworkCard}\Kernel\IfDescr
>
> SYSTEM: HKEY_LOCAL_MACHINE\SYSTEM\Control\NetworkSetup2\Interfaces\\{NetworkCard}\Kernel\CurrentAddress

#### Connected Network
> SOFTWARE: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profile

#### List of Users
> SAM: SAM\Domains\Accounts\Users\Names


