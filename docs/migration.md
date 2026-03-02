---
sidebar_label: 'Migration'
---

# PowerShell Ease to ACS Ease Migration
The conversion program provides a mechanism to migrate existing PowerShell Ease tasks to the new ACS Ease environment.

One of the major changes is that Ease bundle tasks are no longer a single task and instead will be converted to sub-schedule containing 
seperate tasks executed in the correct sequence. The sub-schedules are installed on the local OpCon system and are instigated by selecting a
BUNDLE-* task type which injects a container job into the local Ease schedule (**EASE-LOCAL**). 

## Install Conversion Utilities
Download the ACS Ease migration software (ACSEaseMigration.zip) from the FTP site **/OpCon Releases/Integrations/Ease/Migration Utility** and extract the ACSEaseMigration.zip file into a directory on a Windows system.

Edit the Conversion.config file setting the information using the **Encrypt.exe** utility to encrypt passwords and tokens.

```
[GENERAL]
DEBUG=OFF

[OPCON]
OPCON_API_ADDRESS=DESKTOP-QMQS7D3:443
OPCON_API_TOKEN=5a4459795a4749335a4749744e6a41354f433030595441344c546868596d51744d6a6b794d32457a4e4445345a574a6b
OPCON_PROFILE_NAME=OPCONXPS
OPCON_DB_SERVER=DESKTOP-QMQS7D3
OPCON_DB=opconxps
OPCON_DB_USER=sa
OPCON_DB_USER_PASSWORD=4d4842444d4735346343513d
OPCON_USER=ocadm
OPCON_USER_PASSWORD=6233426a6232353463484d3d

```

where

Property   |  Description
-------------------------- | ----------------
**[OPCON]**                | Header containing the name of the target OpCon system.
**OPCON_API_ADDRESS**      | The address and port number of the OpCon Rest-API
**OPCON_API_TOKEN**        | An OpCon application token encrypted using the Encrypt utility.
**OPCON_PROFILE_NAME**     | A profile name (default OPCONXPS).
**OPCON_DB_SERVER**        | The address of the OpCon Database server.
**OPCON_DB**               | The OpCon database name.
**OPCON_DB_USER**          | A database user that has the required privileges to interact with the OpCon database.
**OPCON_DB_USER_PASSWORD** | The password of the database user encrypted using the Encrypt utility.
**OPCON_USER**             | An OpCon user that has the required privileges to interact with the schedules.
**OPCON_USER_PASSWORD**    | The password of the OpCon user encrypted using the Encrypt utility.

## Encrypt.exe Utility
The Encrypt.exe utility uses standard 64 bit encryption to encrypt text strings. This utility must be used to encrypt passwords and tokens inserted into the Conversion.config file.

Arguments   |  Description
----------- | ----------------
**-v**      | The information to encrypt. If string includes special characters, placed double quotes around the string

Example on how to encrypt the value abcdefg

```
Encrypt.exe -v "abcdefg"

```

### CreateEaseAgent.exe Utility
The CreateEaseAgent.exe utility is used to create the ACS Agent definition that the tasks will use. 

The process extracts the existing encrypted property connection information from the target OpCon system, decrypts the data and uses the
information to set the Ease Datacenter values.
 
Arguments   |  Description
----------- | ----------------
**-mn**     | The name of the ACS Ease agent to create.
**-opc**    | The name of target OpCon system (matches a header value in the Conversion.config file). 
**-id**     | The customer Ease ID. 
**-p**      | The name of the encrypted property containing the connection information. 
**-lapi**   | The address of the local OpCon Rest-API server (server:port). 
**-lusr**   | A local user name that will be used to acess through the Rest-API. 
**-lpwd**   | The password of the local user. 

Example on how to create an ACS Ease machine EASE001

```
CreateEaseAgent.exe -opc OPCON -mn EASE001 -id 999 -p  EaseConnection -lapi OPCON:443 -lusr apiusr -lpwd opconxps

```

### ConvertEaseTasks.exe Utility

The utility is used to convert existing Windows embedded script Ease tasks within a schedule to new ACS Ease tasks. 
The process scans through the schedule looking for Windows tasks and then if the Windows task has an embedded script starting with the name "EASE".
If there is a match, a copy of the Windows properties is made, the task type is reset to a Null Job. The task type is changed to ACS / Ease and then the Script arguments 
line is converted to the new ACS Ease task and the ACS properties are then set into the task. As the new Ease environment supports the Multi ease capability, if the
script does not have an -Identifier argument a value of **0001** will be inserted.
  
The advantage of following this process is that existing definitions such as frequencies, dependencies, etc are not touched and remain as they were. Only the task data type is changed.

Please note the definitions are case-sensitive.

Arguments   |  Description
----------- | ----------------
**-jf**     | The job filter used to determine which tasks in the schedule should be converted (supports wildcards and a value of ALL indicates all Ease embedded script tasks in the schedule must be converted).
**-mn**     | The name of the ACS agent that the task will be associated with.
**-opc**    | The name of target OpCon system (matches a header value in the Conversion.config file). 
**-sf**     | The schedule filter used to determine which schedules should be converted (supports wildcards and a value of ALL indicates all schedules must be converted).

Example on how to convert legacy Ease embedded script tasks 

```
ConvertEaseTasks.exe -opc OPCON  -mn EASE001 -sf EASETEST??V -jf ALL

```

## General Conversion Process
The first action is to create the ACS Ease Agent.

### Create the Ease Agent for the connection

- create the new Ease Agent using the supplied name, the name of the existing property containing the Ease connection information.
	- provide the required arguments

### Convert the PowerShell Ease tasks
This process scans through the schedules and tasks converting the found according to the schedule and job filters. 
It is suggested that before starting the process, a copy of the OpCon database is made as well as a copy of the schedules to be converted.

The process resets the job data from Windows to ACS. No other OpCon objects such as frequencies, dependencies, events are touched. 

- run the createEaseAgent.exe utility using the appropriate arguments.
	- process each selected schedule
		- get the list of master jobs associated with the schedule
		- check if the job is a Windows job.
		- check if the job-type is an EmBedded script job
		- if the Embedded Script name start with **EASE**
			- get the Windows properties associated with the job
			- reset the job to a null job.
			- set the machine id and the machine type (27).
			- create the ACS properties object. 
			- convert the script arguments to an ACS Ease job object.
			- commit the job changes.