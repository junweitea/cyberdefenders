Hello all! This is just my note that i referenced when doing memory forensic using volatility! My current setup is running **Volatility 3 Framework 2.7.0** in Windows 10 Environment~

### Windows Plugin (Prioritising the one i often used, starting from the top)
- windows.info.Info **(all information regarding the image of the memory dump e.g Windows Build)**
- windows.pslist.PsList
- windows.psscan.PsScan
- windows.pstree.PsTree
- windows.malfind.Malfind **(find code injection in processes)**
- windows.ldrmodules.LdrModules **(find injected DLL in processes)**
- windows.cmdline.CmdLine **(find command entered in cmd)**
- windows.handles.Handles **(list all file, mutex, key or process referenced to each process)**
- windows.netscan.NetScan
- windows.netstat.NetStat 
- windows.svcscan.SvcScan **(view all service info)**
- windows.getsids.GetSIDs **(view which user/sid executed what process)**
- windows.dlllist.DllList 
- windows.hashdump.Hashdump **(To extract and decrypt cached domain credentials)**
- windows.lsadump.Lsadump
- windows.registry.printkey.PrintKey **(Print Registry Key value)**
- windows.mftscan.ADS **(View all ADS)**
- windows.devicetree.DeviceTree = **(list attached devices and its driver)**
- windows.verinfo.VerInfo = **(view the version number of the exe)**

- ------------------------------------------------------------------------
- **(Least Used Plugins)**
<Least Important>
- windows.bigpools.BigPools
- windows.cachedump.Cachedump
- windows.callbacks.Callbacks
- windows.crashinfo.Crashinfo
- windows.devicetree.DeviceTree
- windows.driverirp.DriverIrp
- windows.drivermodule.DriverModule
- windows.driverscan.DriverScan
- windows.dumpfiles.DumpFiles
- windows.envars.Envars
- windows.filescan.FileScan
- windows.getservicesids.GetServiceSIDs
- windows.iat.IAT
- windows.joblinks.JobLinks
- windows.mbrscan.MBRScan
- windows.memmap.Memmap
- windows.mftscan.MFTScan
- windows.modscan.ModScan
- windows.modules.Modules
- windows.mutantscan.MutantScan
- windows.poolscanner.PoolScanner
- windows.privileges.Privs
- windows.registry.certificates.Certificates
- windows.registry.hivelist.HiveList
- windows.registry.hivescan.HiveScan
- windows.registry.printkey.PrintKey
- windows.registry.userassist.UserAssist
- windows.sessions.Sessions
- windows.skeleton_key_check.Skeleton_Key_Check
- windows.ssdt.SSDT
- windows.statistics.Statistics
- windows.strings.Strings
- windows.symlinkscan.SymlinkScan
- windows.thrdscan.ThrdScan
- windows.truecrypt.Passphrase
- windows.vadinfo.VadInfo
- windows.vadwalk.VadWalk
- windows.vadyarascan.VadYaraScan
- windows.verinfo.VerInfo
- windows.virtmap.VirtMap

### Command to be run:
```bash
> python vol.py -f memdump.mem windows.pstree.PsTree > 23pstree.txt
```
```bash
> python vol.py -f mem.dump windows.pslist.PsList --pid 880 --dump
```
```bash
> python vol.py -f mem.dump windows.ldrmodules.LdrModules --pid 880
```
```bash
> python vol.py -f mem.dump windows.dlllist.DLLlist --pid 880
```
```bash
> python vol.py -f mem.dump windows.registry.printkey.PrintKey --key "Microsoft\Windows\NT\CurrentVersion\Run"
```
```bash
> strings64.exe -a mem.dump > 23strings.txt
```

### Approach for solving certain questions:
1) look for volatility profile = **(Info)** plugin and look for NtBuildLab
2) Odd file referenced to a process = **(Handles)** plugin to look for any related data to that process
3) base address of injected DLL = **(Malfind)** plugin to look for the base address of the orignal process that was used for DLL injection
4) Looking for file in ADS = **(mftscan.ADS)** plugin for all MFT entries
5) Looking for certain keywords or program = string the whole memory file and find it!
6) Looking for MD5 Hash or a certain length of text = using string function (*strings64.exe -n 32 XXXX.svchost.exe.0x7ff65b2d0000.dmp > 32string.txt*)

