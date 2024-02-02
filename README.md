# <img src="https://github.com/Lifailon/WinAPI/blob/rsa/Screen/ico/x128.png" width="25" /> WinAPI

![GitHub release (with filter)](https://img.shields.io/github/v/release/lifailon/WinAPI?color=<green>)
![GitHub top language](https://img.shields.io/github/languages/top/lifailon/WinAPI)
![GitHub last commit (by committer)](https://img.shields.io/github/last-commit/lifailon/WinAPI)
![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/lifailon/WinAPI)
![GitHub License](https://img.shields.io/github/license/lifailon/WinAPI?color=<green>)
![GitHub Repo stars](https://img.shields.io/github/stars/Lifailon/WinAPI)
![GitHub downloads](https://img.shields.io/github/downloads/lifailon/WinAPI/total?color=<green>)

**📝 Publication on [Habr](https://habr.com/ru/articles/783022/)**

**🔎 Test version: [README](https://github.com/Lifailon/WinAPI/blob/rsa/Test/README.md)**

REST API and Web server on base **.NET HttpListener** and backend only **PowerShell Core**. Using WinAPI, you can quickly set up remote communication with Windows OS using API, without the need to configure WinRM or OpenSSH and get control from any platform, including **Linux** via browser or any REST client.

**🔗 Implemented**:

- [✅ More than 20 unique endpoints](#-implemented-endpoints)
- [✅ Authentication](#-authorization)
- [✅ Converting to 4 data types](#-change-data-type)
- [✅ Response codes handling](#-response-code)
- [✅ Error handling](#-operational-stability)

🍿 The server functionality is implemented in the [Kinozal-Bot](https://github.com/Lifailon/Kinozal-Bot) project.

🐧 Since the server is built on .NET, this implementation is **cross-platform**, you can try managing Linux services from a Windows system: **[dotNET-Systemd-API](https://github.com/Lifailon/dotNET-Systemd-API)**.

➡️ [🚀 Install](#-install)

## 🎉 Web server

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Example-0.4.2.gif)

- Process management:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Process.jpg)

- Service management:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Service.jpg)

- Viewer and filtering event:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Event.gif)

- Hardware statistics:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Hardware.jpg)

- Physical and logical disk statistics, metrics **IOps**, **SMART** (via embedded module **Storage 2.0**) and current network stats.

To give you an example, a file download from the Internet was taking place on a remote machine:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Metrics-Disk-Network.jpg)

- Sensors from **LibreHardwareMonitor**:

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Web/Web-Sensors.jpg)

## 📚 Implemented endpoints

All GET requests can be output in one of the following formats: **JSON (default), HTML, XML, CSV**. When using a browser for GET requests, by default the response is processed in table format using HTML markup.

- **GET**

`/api/service` - Get list **all services** \
`/apt/service/service_name` - Get list service by the specified name passed in URL (using **wildcard** format) \
`/apt/process` - Get a list **all running processes** in an easy-to-read format \
`/apt/process/process_name` - Get list running processes by the specified name passed in URL (using **wildcard** format) \
`/api/files` - Get a list of files and directories at the specified path in the **Path header** with the size, number of child files and directories, date of creation, access and modification

- **GET metrics from WMI/CIMv2 (Common Information Model)**

`/api/hardware` - Output of summary statistics of metrics close to **Task Manager** \
`/api/performance` - Output metrics from **Counter** \
`/api/cpu` - CPU use to procent for all core and total \
`/api/memory` - Memory use to GB and procent \
`/api/memory/slots` - Number of memory slots and their frequency \
`/api/disk/physical` - List of all physical disks, model, size, operation status and errors \
`/api/disk/logical` - List of all logical disks, model and size \
`/api/disk/partition` - List partitions of all physical disks \
`/api/disk/smart` - Temperature, health status, power on hours, start and stop cycle count \
`/api/disk/iops` - Read and write speed, queue, Input and Output operations per second for all physical disks \
`/api/video` - List of all video adapters, video memory size and resolution \
`/api/network` - List of all network adapters and their settings \
`api/network/ipconfig` - Settings for all network interfaces \
`api/network/stat` - Network connection statistics (ports) \
`api/network/interface/stat/current` - Current statistics of the active network interface (formatted dat) \
`api/network/interface/stat/total` - General statistics of the active network interface since system boot (raw data)

- **GET metrics from Open Hardware Monitor via WMI/CIM**

`/api/sensor` - Summary table of all sensors

Source module: [PowerShellHardwareMonitor](https://github.com/Lifailon/PowerShellHardwareMonitor) (module installation is not required)

- **Web**

**HTTP server. Endpoints only via Web Browser**.

`/service` - Checking status, starting and stopping services \
`/process` - Characteristics as well as starting and stopping processes \
`/events/list` - List of all Windows event providers \
`/events/<Event_Name>` - List of all events of the selected log with the ability to filter by content

💡 Use the text entry form to **filter messages** through the browser

- **POST**

`/apt/service/<Service_Name>` - Stop, start and restart services by name (only one at a time, not wildcard format), status is transmitted in the request header (**Status: <Stop/Start/Restart>**). Upon execution, the service status is returned in the format of a GET request. \
`/apt/process/<Process_Name>` - Check the number of running processes (**Status: Check**), stop a process by name (**Status: Stop**) and start a process (**Status: Start**). To start a process, you can use the function to search for an executable file in the file system by its name, but you can also pass the path to the executable file through the request header (e.g. **Path: C:\Program Files\qBittorrent\qbittorrent.exe**). \
`/api/file-delete` - Deleting the file or directory specified in the header **Path** one at a time

To find an executable file, the `Find-Process` function is used to scan the following directories:

```PowerShell
"C:\Program Files",
"C:\Program Files (x86)",
"C:\Users\<UserName>\AppData\Roaming",
"C:\Users\<UserName>\Documents"
```

## 🚀 Install

Use in **PowerShell Core**.

To install or update the process scripts and latest server side (path default: `$home/Documents/WinAPI`), run the command in your console:
```PowerShell
Invoke-Expression(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/WinAPI/rsa/WinAPI/Deploy/Deploy-WinAPI-Server.ps1")
```
When the server first starts up, a default **configuration file (winapi.ini)** is created at the path: `$home/Documents/WinAPI/winapi.ini`. Configure it yourself.

The following variables to configure **port, login and password** for connect to the server:

```PowerShell
port = 8443
user = rest
pass = api
```

And open a port on your firewall:

```PowerShell
New-NetFirewallRule -DisplayName "WinAPI" -Profile Any -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8443
```

If you want output to **log** requests to a console and/or write file, enable and set the path.

```PowerShell
Log_Console = True
Log_File    = True
Log_Path    = C:/Users/lifailon/Documents/WinAPI/winapi.log
```

Specify the data source for the `/api/sensor` endpoint (default: **LibreHardwareMonitor**):

```PowerShell
SensorSource = OpenHardwareMonitor
```

💡 For the endpoint to work, you must download the portable version of the [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) (Default) or [OpenHardwareMonitor](https://openhardwaremonitor.org/downloads/) program in path `C:\Users\<UserName>\Documents\LibreHardwareMonitor` and `C:\Users\<UserName>\Documents\OpenHardwareMonitor\OpenHardwareMonitor`.

For a quick installation LibreHardwareMonitor, use this command in your terminal:

```PowerShell
Invoke-Expression(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/PowerShellHardwareMonitor/rsa/Install/Install-LibreHardwareMonitor.ps1")
```

Install OpenHardwareMonitor:

```PowerShell
Invoke-Expression(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Lifailon/PowerShellHardwareMonitor/rsa/Install/Install-OpenHardwareMonitor.ps1")
```

## Running. The 1st option (stable, process, added in version 0.3.2)

> 💡 **Administrator rights are required to run**

The scripts are already included in the files when using server part [deployment](https://github.com/Lifailon/WinAPI#-install).

**Run the server part in background process mode**:

```PowerShell
PS C:\Users\Lifailon\Documents\WinAPI> .\winapi-process-start.ps1
```

**Stop process**:

```PowerShell
PS C:\Users\Lifailon\Documents\WinAPI> .\winapi-process-stop.ps1
```

To check the status server (port):

```PowerShell
PS C:\Users\Lifailon\Documents\WinAPI> .\test-port.ps1
```

## Running. The 2nd option (service, added in version 0.3.1)

> 💡 **For reason unknown to me, the service doesn't process all the code on startup** (doesn't create an ini file and hangs at POST request to stop the process).

To install the server part as a **service (used NSSM)**, download scripts to **automatically [deployument](https://github.com/Lifailon/WinAPI/tree/rsa/WinAPI/Service), start, stop and remove**.

## Running. The 3rd option (executable, added in version 0.3.0)

> 💡 **PowerShell 5.1 acts as the default handler (limitations of the ps2exe module)**, which prevents all endpoints from working correctly

There are two options for launching using an **[executable file](https://github.com/Lifailon/WinAPI/tree/rsa/WinAPI/Bin)** ([build script](https://github.com/Lifailon/WinAPI/blob/rsa/WinAPI/Bin/compiling-ps2exe.ps1)).

**winapi-console.exe** - process startup in a window with logging output of connections to the server

**winapi-process.exe** - background process startup

To stop the background process, use the command: `Get-Process *winapi* | Stop-Process`

## 🔒 Authorization

Base authorization has been implemented (based on **Base64**).

Default login and password:
```PowerShell
$user = "rest"
$pass = "api"
```
- Example 1.

```PowerShell
$SecureString = ConvertTo-SecureString $pass -AsPlainText -Force
$Credential = New-Object System.Management.Automation.PSCredential($user, $SecureString)
Invoke-RestMethod -Credential $Credential -AllowUnencryptedAuthentication -Uri http://192.168.3.99:8443/api/service
```
- Example 2.

```PowerShell
$EncodingCred = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("${user}:${pass}"))
$Headers = @{"Authorization" = "Basic ${EncodingCred}"}
Invoke-RestMethod -Headers $Headers -Uri http://192.168.3.99:8443/api/service
```
- Example 3. cURL client. Receiving data in different formats.

```Bash
user="rest"
pass="api"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/service | jq  -r '.[] | {data: "\(.Name) - \(.Status)"} | .data'
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/service/win
curl -s -X GET -u $user:$pass -H 'Content-Type: application/json' http://192.168.3.99:8443/api/service/winrm
curl -s -X GET -u $user:$pass -H 'Content-Type: application/html' http://192.168.3.99:8443/api/service/winrm
curl -s -X GET -u $user:$pass -H 'Content-Type: application/xml' http://192.168.3.99:8443/api/service/winrm
curl -s -X GET -u $user:$pass -H 'Content-Type: application/csv' http://192.168.3.99:8443/api/service/winrm
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/process
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/process/torrent
```

> It is possible to configure filtering by IP address or subnet. It is possible to configure other authentication methods to HttpListener.

## 📢 Response code

**200. Request completed successfully.**

**400. Bad Request.** Invalid header and service or process could not be found.

**401. Unauthorized.** Login or password is invalid.

**404. Not found endpoint.** Response to the lack of endpoints.

**405. Method not allowed.** Response to other methods.

## ⚠️ Operational stability

Processing of simultaneous requests from several clients (managed to test three simultaneous connections).

If the previous request has not been processed yet and a new request from the same client arrives, the server terminates with an error, this behavior was detected in the browser when quickly refreshing pages, to handle this event is implemented **automatic restart of the server**. For Web-client such restart is processed without error and in the process of page refresh the response of the last request is returned.

In Invoke-RestMethod and curl, interrupting the previous request and sending a new one to another endpoint works without error and the response of the last request is returned (taking into account the processing time of the previous one).

## 🐧 Examples POST request from Linux client

- Stop and start service **WinRM**:

First find the service to pass its full name to the to url for POST request (example, using part of the name in GET request).

```Bash
user="rest"
pass="api"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/service/win | jq -r .[].Name
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/service/winrm -H "Status: Stop"
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/service/winrm -H "Status: Start"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/service/winrm | jq -r .Status
```

- Stop and start process **qBittorrent**:

First find the process by its name in wilcard format using a GET request. Using **Check** in the **Status** header, we display the number of running processes. To stop the process, use header **Status: Stop**. To run the process, two examples are given using the name to find the executable and the second option, specify the full path to the executable.

```Bash
user="rest"
pass="api"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/process/torrent
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Check"
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Stop"
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Start"
```

- Stop and start process **Plex Media Server**:

> If the service in the **name contains spaces**, pass the name in the url request using the **underscore (_) delimiter**.

```Bash
user="rest"
pass="api"
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/plex_media_server -H "Status: Stop"
curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/plex_media_server -H "Status: Start" -H "Path: C:\Program Files\Plex\Plex Media Server\Plex Media Server.exe"
```

- Delete file

First, we look through the parent directories and look for the required file. In the **Path header we pass the FullName** of the desired file (or directory).

```Bash
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies/The-Flash"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies/The-Flash/4 sezon"
curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies/The-Flash/4 sezon/The.Flash.S04E23.1080p.rus.LostFilm.TV.mkv"
curl -s -X POST -u $user:$pass -data '' http://192.168.3.99:8443/api/file-delete -H "Path: D:/Movies/The-Flash/4 sezon/The.Flash.S04E23.1080p.rus.LostFilm.TV.mkv"
```

## 📬 Change data type

Examples:

```bash
user=rest
pass=api

curl -s -X GET -u $user:$pass -H 'Content-Type: application/json' http://192.168.3.99:8443/api/service/win | jq
[
  {
    "Name": "WinAPI",
    "DisplayName": "WinAPI",
    "Status": "Stopped",
    "StartType": "Manual"
  },
  {
    "Name": "WinDefend",
    "DisplayName": "Служба антивирусной программы Microsoft Defender",
    "Status": "Running",
    "StartType": "Automatic"
  },
  {
    "Name": "WinHttpAutoProxySvc",
    "DisplayName": "Служба автоматического обнаружения веб-прокси WinHTTP",
    "Status": "Running",
    "StartType": "Manual"
  },
  {
    "Name": "Winmgmt",
    "DisplayName": "Инструментарий управления Windows",
    "Status": "Running",
    "StartType": "Automatic"
  },
  {
    "Name": "WinRM",
    "DisplayName": "Служба удаленного управления Windows (WS-Management)",
    "Status": "Stopped",
    "StartType": "Manual"
  }
]

curl -s -X GET -u $user:$pass -H 'Content-Type: application/xml' http://192.168.3.99:8443/api/service/winrm | xq
<?xml version="1.0" encoding="utf-8"?>
<Objects>
  <Object Type="System.Management.Automation.PSCustomObject">
    <Property Name="Name" Type="System.String">WinRM</Property>
    <Property Name="DisplayName" Type="System.String">Служба удаленного управления Windows (WS-Management)</Property>
    <Property Name="Status" Type="System.String">Stopped</Property>
    <Property Name="StartType" Type="System.String">Manual</Property>
  </Object>
</Objects>

curl -s -X GET -u $user:$pass -H 'Content-Type: application/csv' http://192.168.3.99:8443/api/service/winrm
"Name","DisplayName","Status","StartType" "WinRM","Служба удаленного управления Windows (WS-Management)","Stopped","Manual"

curl -s -X GET -u $user:$pass -H 'Content-Type: application/html' http://192.168.3.99:8443/api/service/winrm
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> <html xmlns="http://www.w3.org/1999/xhtml"> <head> <title>HTML TABLE</title> </head><body> <table> <colgroup><col/><col/><col/><col/></colgroup> <tr><th>Name</th><th>DisplayName</th><th>Status</th><th>StartType</th></tr> <tr><td>WinRM</td><td>Служба удаленного управления Windows (WS-Management)</td><td>Stopped</td><td>Manual</td></tr> </table> </body></html>
```

## 🔌 Windows client

```PowerShell
$user = "rest"
$pass = "api"
$EncodingCred = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("${user}:${pass}"))
```

- Stop service **WinRM**:

```PowerShell
$Headers = @{"Authorization" = "Basic ${EncodingCred}"}
$Headers += @{"Status" = "Stop"}
Invoke-RestMethod -Headers $Headers -Uri http://192.168.3.99:8443/api/service/winrm
Invoke-RestMethod -Headers $Headers -Method Post -Uri http://192.168.3.99:8443/api/service/winrm
```
- Start service **WinRM**:

```PowerShell
$Headers = @{"Authorization" = "Basic ${EncodingCred}"}
$Headers += @{"Status" = "Start"}
Invoke-RestMethod -Headers $Headers -Method Post -Uri http://192.168.3.99:8443/api/service/winrm
```

### 🔌 Module Get-Hardware

> 💡 The Get-Hardware function uses the ThreadJob module, the script provides automatic installation in case of its absence. This is the only function which execution time was reduced by half due to threads.

For an example, import the **[Get-Hardware](https://github.com/Lifailon/WinAPI/tree/rsa/WinAPI/Modules/Get-Hardware)** module from the script directory or copy it to your modules directory. For local retrieval of information, use the command without parameters, for remote launch via API, use the parameter **ComputerName**.

```PowerShell
Import-Module $home\Documents\WinAPI\Modules\Get-Hardware\Get-Hardware.psm1
Get-Hardware
Get-Hardware -ComputerName 192.168.3.99 -Port 8443 -User rest -Pass api
```

> You can add endpoints to the module yourself for fast remote communication via API.

Comparison of module operation with and without threads (on average 3.3 seconds versus 1.4 seconds):

![Image alt](https://github.com/Lifailon/WinAPI/blob/rsa/Screen/Console/Get-Hardware-Threads-Diff.jpg)

## 📊 GET data (output examples)

### Service and process management

```Bash
lifailon@hv-devops-01:~$ user="rest"
lifailon@hv-devops-01:~$ pass="api"
lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/service/win
[
  {
    "Name": "WinDefend",
    "DisplayName": "Служба антивирусной программы Microsoft Defender",
    "Status": "Running",
    "StartType": "Automatic"
  },
  {
    "Name": "WinHttpAutoProxySvc",
    "DisplayName": "Служба автоматического обнаружения веб-прокси WinHTTP",
    "Status": "Running",
    "StartType": "Manual"
  },
  {
    "Name": "Winmgmt",
    "DisplayName": "Инструментарий управления Windows",
    "Status": "Running",
    "StartType": "Automatic"
  },
  {
    "Name": "WinRM",
    "DisplayName": "Служба удаленного управления Windows (WS-Management)",
    "Status": "Stopped",
    "StartType": "Manual"
  }
]

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass -H 'Content-Type: application/json' http://192.168.3.99:8443/api/service/winrm
{
  "Name": "WinRM",
  "DisplayName": "Служба удаленного управления Windows (WS-Management)",
  "Status": "Stopped",
  "StartType": "Manual"
}

lifailon@hv-devops-01:~$ curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/service/winrm -H "Status: Start"
{
  "Name": "winrm",
  "DisplayName": "Служба удаленного управления Windows (WS-Management)",
  "Status": "Running",
  "StartType": "Manual"
}

lifailon@hv-devops-01:~$ curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Check"
Number active qbittorrent processes: 0

lifailon@hv-devops-01:~$ curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Start"
Number active qbittorrent processes: 1

lifailon@hv-devops-01:~$ curl -s -X POST -u $user:$pass --data '' http://192.168.3.99:8443/api/process/qbittorrent -H "Status: Stop"
Number active qbittorrent processes: 0
```

### Hardware metrics (general summary, performance, cpu, memory and video)

```Bash
lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/hardware
{
  "Host": "HUAWEI-BOOK",
  "Uptime": "17:17",
  "BootDate": "01.02.2024 09:05:04",
  "Owner": "lifailon",
  "OS": "Майкрософт Windows 10 Pro",
  "Motherboard": "HUAWEI MRGF-XX-PCB M1010",
  "Processor": "12th Gen Intel(R) Core(TM) i7-1260P",
  "Core": 12,
  "Thread": 16,
  "CPU": "14 %",
  "ProcessCount": 305,
  "ThreadsCount": 4641,
  "HandlesCount": 157065,
  "MemoryAll": "16 GB",
  "MemoryUse": "11,00 GB",
  "MemoryUseProc": "70 %",
  "WorkingSet": "11,06 GB",
  "PageMemory": "12,54 GB",
  "MemorySlots": 8,
  "PhysicalDiskCount": 1,
  "PhysicalDiskAllSize": "954 Gb",
  "LogicalDiskCount": 3,
  "LogicalDiskAllSize": "1053 Gb",
  "DiskTotalTime": "2 %",
  "DiskTotalIOps": 3,
  "DiskTotalRead": "0,015 MByte/Sec",
  "DiskTotalWrite": "0,000 MByte/Sec",
  "VideoCardCount": 3,
  "VideoCardAllSize": "1 Gb",
  "NetworkAdapterEnableCount": 3,
  "NetworkReceivedCurrent": "0,154 MByte/Sec",
  "NetworkSentCurrent": "0,044 MByte/Sec",
  "NetworkReceivedTotal": "4,13 GByte",
  "NetworkSentTotal": "0,34 GByte",
  "PortListenCount": 50,
  "PortEstablishedCount": 54
}

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/performance
{
  "CPUTotalTime": "1 %",
  "MemoryUse": "30 %",
  "DiskTotalTime": "12 %",
  "AdapterName": "intel[r] wi-fi 6e ax211 160mhz",
  "AdapterSpeed": "0,093 MByte/Sec"
}

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/cpu
[
  {
    "Name": "0",
    "ProcessorTime": "8 %",
    "PrivilegedTime": "6 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "79 %"
  },
  {
    "Name": "1",
    "ProcessorTime": "0 %",
    "PrivilegedTime": "0 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "99 %"
  },
  {
    "Name": "2",
    "ProcessorTime": "2 %",
    "PrivilegedTime": "0 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "98 %"
  },
  {
    "Name": "3",
    "ProcessorTime": "8 %",
    "PrivilegedTime": "18 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "76 %"
  },
  {
    "Name": "4",
    "ProcessorTime": "0 %",
    "PrivilegedTime": "0 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "99 %"
  },
  {
    "Name": "5",
    "ProcessorTime": "8 %",
    "PrivilegedTime": "18 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "76 %"
  },
  {
    "Name": "6",
    "ProcessorTime": "0 %",
    "PrivilegedTime": "0 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "100 %"
  },
  {
    "Name": "7",
    "ProcessorTime": "8 %",
    "PrivilegedTime": "18 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "76 %"
  },
  {
    "Name": "8",
    "ProcessorTime": "14 %",
    "PrivilegedTime": "12 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "68 %"
  },
  {
    "Name": "9",
    "ProcessorTime": "33 %",
    "PrivilegedTime": "18 %",
    "UserTime": "18 %",
    "InterruptTime": "0 %",
    "IdleTime": "60 %"
  },
  {
    "Name": "10",
    "ProcessorTime": "27 %",
    "PrivilegedTime": "12 %",
    "UserTime": "18 %",
    "InterruptTime": "0 %",
    "IdleTime": "68 %"
  },
  {
    "Name": "11",
    "ProcessorTime": "27 %",
    "PrivilegedTime": "18 %",
    "UserTime": "12 %",
    "InterruptTime": "0 %",
    "IdleTime": "62 %"
  },
  {
    "Name": "12",
    "ProcessorTime": "27 %",
    "PrivilegedTime": "6 %",
    "UserTime": "24 %",
    "InterruptTime": "0 %",
    "IdleTime": "71 %"
  },
  {
    "Name": "13",
    "ProcessorTime": "14 %",
    "PrivilegedTime": "12 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "76 %"
  },
  {
    "Name": "14",
    "ProcessorTime": "21 %",
    "PrivilegedTime": "18 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "69 %"
  },
  {
    "Name": "15",
    "ProcessorTime": "0 %",
    "PrivilegedTime": "0 %",
    "UserTime": "0 %",
    "InterruptTime": "0 %",
    "IdleTime": "94 %"
  },
  {
    "Name": "_Total",
    "ProcessorTime": "11 %",
    "PrivilegedTime": "9 %",
    "UserTime": "6 %",
    "InterruptTime": "0 %",
    "IdleTime": "80 %"
  }
]

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/memory
{
  "MemoryAll": "15,73 GB",
  "MemoryUse": "10,98 GB",
  "MemoryUseProc": "70 %",
  "PageSize": "48,00 GB",
  "PageUse": "7,92 GB",
  "PageUseProc": "17 %",
  "PagePath": "D:\\pagefile.sys",
  "MemoryVirtAll": "63,73 GB",
  "MemoryVirtUse": "18,90 GB",
  "MemoryVirtUseProc": "30 %",
  "ProcWorkingSet": "11,02 GB",
  "ProcPageMemory": "12,62 GB"
}

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/memory/slots
[
  {
    "Tag": "Physical Memory 0",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller0-ChannelA",
    "Bank": "BANK 0"
  },
  {
    "Tag": "Physical Memory 1",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller0-ChannelB",
    "Bank": "BANK 1"
  },
  {
    "Tag": "Physical Memory 2",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller0-ChannelC",
    "Bank": "BANK 2"
  },
  {
    "Tag": "Physical Memory 3",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller0-ChannelD",
    "Bank": "BANK 3"
  },
  {
    "Tag": "Physical Memory 4",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller1-ChannelA",
    "Bank": "BANK 0"
  },
  {
    "Tag": "Physical Memory 5",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller1-ChannelB",
    "Bank": "BANK 1"
  },
  {
    "Tag": "Physical Memory 6",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller1-ChannelC",
    "Bank": "BANK 2"
  },
  {
    "Tag": "Physical Memory 7",
    "Model": "5200 Mhz  ",
    "Size": "2048 Mb",
    "Device": "Controller1-ChannelD",
    "Bank": "BANK 3"
  }
]

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/video
[
  {
    "Model": "Intel(R) Iris(R) Xe Graphics",
    "Display": "3120x2080",
    "VideoRAM": "1 Gb"
  },
  {
    "Model": "Virtual Display Device",
    "Display": "3840x2560",
    "VideoRAM": "0 Gb"
  },
  {
    "Model": "Citrix Indirect Display Adapter",
    "Display": "x",
    "VideoRAM": "0 Gb"
  }
]
```

### Disk metrics (Physical, Logical, SMART and IOps)

```json
lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/disk/physical
{
  "Model": "WD PC SN740 SDDPNQD-1T00-1027",
  "Size": "954 Gb",
  "PartitionCount": 4,
  "Interface": "SCSI",
  "Status": "OK",
  "ConfigManagerErrorCode": 0,
  "LastErrorCode": null
}

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/disk/logical
[
  {
    "Logical_Disk": "C:",
    "FileSystem": "NTFS",
    "VolumeName": "",
    "AllSize": "153 Gb",
    "FreeSize": "44 Gb",
    "Free": "29 %"
  },
  {
    "Logical_Disk": "D:",
    "FileSystem": "NTFS",
    "VolumeName": "",
    "AllSize": "800 Gb",
    "FreeSize": "413 Gb",
    "Free": "52 %"
  },
  {
    "Logical_Disk": "G:",
    "FileSystem": "FAT32",
    "VolumeName": "Google Drive",
    "AllSize": "100 Gb",
    "FreeSize": "42 Gb",
    "Free": "42 %"
  }
]

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/disk/iops
[
  {
    "Name": "0 D: C:",
    "ReadWriteTime": "0 %",
    "ReadTime": "0 %",
    "WriteTime": "0 %",
    "IdleTime": "99 %",
    "QueueLength": 0,
    "BytesPersec": "0,000 MByte/Sec",
    "ReadBytesPersec": "0,000 MByte/Sec",
    "WriteBytesPersec": "0,000 MByte/Sec",
    "IOps": 0,
    "ReadsIOps": 0,
    "WriteIOps": 0
  },
  {
    "Name": "_Total",
    "ReadWriteTime": "0 %",
    "ReadTime": "0 %",
    "WriteTime": "0 %",
    "IdleTime": "99 %",
    "QueueLength": 0,
    "BytesPersec": "0,000 MByte/Sec",
    "ReadBytesPersec": "0,000 MByte/Sec",
    "WriteBytesPersec": "0,000 MByte/Sec",
    "IOps": 0,
    "ReadsIOps": 0,
    "WriteIOps": 0
  }
]

lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/disk/smart
{
  "DiskName": "WD PC SN740 SDDPNQD-1T00-1027",
  "Temperature": 61,
  "HealthStatus": "Healthy",
  "OperationalStatus": "OK",
  "MediaType": "SSD",
  "BusType": "NVMe",
  "PowerOnHours": null,
  "StartStopCycleCount": null
}
```

## File system

```Bash
lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies"
[
  {
    "Name": "Adventure-Time",
    "FullName": "D:\\Movies\\Adventure-Time",
    "Type": "Directory",
    "Size": "61.73 GB",
    "Files": 292,
    "Directory": 11,
    "CreationTime": "04.05.2023 10:05:23",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "04.05.2023 10:06:22"
  },
  {
    "Name": "Lupin-S03-1080",
    "FullName": "D:\\Movies\\Lupin-S03-1080",
    "Type": "Directory",
    "Size": "13.401 GB",
    "Files": 7,
    "Directory": 0,
    "CreationTime": "25.10.2023 03:51:41",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "25.10.2023 03:57:34"
  },
  {
    "Name": "Shaman-King",
    "FullName": "D:\\Movies\\Shaman-King",
    "Type": "Directory",
    "Size": "15.123 GB",
    "Files": 64,
    "Directory": 0,
    "CreationTime": "10.07.2023 10:03:10",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "10.07.2023 10:07:01"
  },
  {
    "Name": "The-Cleaner-S02",
    "FullName": "D:\\Movies\\The-Cleaner-S02",
    "Type": "Directory",
    "Size": "7.829 GB",
    "Files": 6,
    "Directory": 0,
    "CreationTime": "25.10.2023 03:47:32",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "25.10.2023 03:51:10"
  },
  {
    "Name": "The-Flash",
    "FullName": "D:\\Movies\\The-Flash",
    "Type": "Directory",
    "Size": "76.569 GB",
    "Files": 43,
    "Directory": 2,
    "CreationTime": "30.07.2023 01:13:20",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "30.07.2023 03:22:09"
  }
]
lifailon@hv-devops-01:~$ curl -s -X GET -u $user:$pass http://192.168.3.99:8443/api/files -H "Path: D:/Movies/The-Flash"
[
  {
    "Name": "3 sezon",
    "FullName": "D:\\Movies\\The-Flash\\3 sezon",
    "Type": "Directory",
    "Size": "41.01 GB",
    "Files": 23,
    "Directory": 0,
    "CreationTime": "30.07.2023 01:13:20",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "30.07.2023 01:14:37"
  },
  {
    "Name": "4 sezon",
    "FullName": "D:\\Movies\\The-Flash\\4 sezon",
    "Type": "Directory",
    "Size": "35.559 GB",
    "Files": 22,
    "Directory": 0,
    "CreationTime": "30.07.2023 01:22:15",
    "LastAccessTime": "19.12.2023 12:12:24",
    "LastWriteTime": "18.12.2023 12:00:37"
  }
]
```
## 📑 Server log

Example of logging different clients: `Google Chrome`, `PowerShell (Invoke-RestMethod)` and `curl`.

```PowerShell
PS C:\Users\lifailon\Documents\Git\WinAPI> . 'C:\Users\lifailon\Documents\Git\WinAPI\WinAPI\WinAPI-0.3.ps1'
Running on port 8443
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/service => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/process => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET / => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /service => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => POST /api/service/WinRM => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /service => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => POST /api/service/WinRM => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /service => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /process => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => POST /api/process/qbittorrent => 200
192.168.3.99:49829 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => POST /api/process/ => 405
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /process => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => POST /api/process/qbittorrent => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /process => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/hardware => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/performance => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/performance => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/cpu => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/cpu => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/cpu => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/cpu => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/cpu => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/memory => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/memory => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/memory => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/memory => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/physical => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/logical => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/iops => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/iops => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/iops => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/iops => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/disk/iops => 200
192.168.3.99:49843 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 => GET /api/video => 200
192.168.3.100:55137 Mozilla/5.0 (Windows NT 10.0; Microsoft Windows 10.0.19045; ru-RU) PowerShell/7.3.7 => GET /api/service => 200
192.168.3.100:55147 Mozilla/5.0 (Windows NT 10.0; Microsoft Windows 10.0.19045; ru-RU) PowerShell/7.3.7 => POST /api/service/winrm => 200
192.168.3.100:55152 Mozilla/5.0 (Windows NT 10.0; Microsoft Windows 10.0.19045; ru-RU) PowerShell/7.3.7 => POST /api/service/winrm => 200
192.168.3.100:55175 Mozilla/5.0 (Windows NT 10.0; Microsoft Windows 10.0.19045; ru-RU) PowerShell/7.3.7 => GET /api/service => 200
192.168.3.100:55181 Mozilla/5.0 (Windows NT 10.0; Microsoft Windows 10.0.19045; ru-RU) PowerShell/7.3.7 => POST /api/service/winrm => 200
192.168.3.101:44112 curl/7.81.0 => GET /api/service/win => 200
192.168.3.101:44120 curl/7.81.0 => GET /api/service/winrm => 200
192.168.3.101:39642 curl/7.81.0 => POST /api/process/qbittorrent => 200
192.168.3.101:46296 curl/7.81.0 => POST /api/process/qbittorrent => 200
```