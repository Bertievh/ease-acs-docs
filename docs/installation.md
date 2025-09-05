---
sidebar_label: 'Installation'
---

# Ease ACS Installation

Download the ACS Ease software from the SMA FTP Site.
Location will be in
**/OpCon Releases/Integrations/Ease/** 
Select the required version.

- Unzip the ACSEase.zip file.
- On-prem customers 
  Copy the ACSEase directory to the \\SAM\\plugins directory
- Cloud customers
  Copy the ACSEase directory to the \\Relay\\plugins directory

Version 25.0.1
- Use Deploy to insert the workflow \\workflows\\EASE-LOCAL.json into the local OpCon system.
  
  - Edit the EASE-LOCAL.json file 
    - changing the primary machine name to match the target OpCon system.
    - changing the userid used to execute the tasks.
  - Import the changed EASE-LOCAl.json file into Deploy using the **File Import** function.
  - Deploy the EASE-LOCAL schedule to the target OpCon system (the schedule includes the required sub-schedules).  

