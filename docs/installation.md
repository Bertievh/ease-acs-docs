---
title: Install EASE ACS
description: How to install the EASE ACS connector on an on-prem OpCon system or SMA Relay installation.
tags: [type/procedural, role/system-administrator, feature/ease-acs]
sidebar_label: 'Installation'
---

# Install EASE ACS

**Theme:** Build  
**Who Is It For?** System Administrator

## What is it?

The EASE ACS connector is a `.dll`-based integration that enables ACS Ease job type definitions in Solution Manager. After installation, the connector is available on on-prem OpCon systems and SMA Relay installations.

- Complete this installation to add the ACS Ease job type to an on-prem OpCon system or SMA Relay installation.
- Follow the version 25.0.1 steps to enable bundle task types (BUNDLE-RSJEDIT, BUNDLE-SEQ-FTP, BUNDLE-SEQ-PROMPT), which require the EASE-LOCAL sub-schedule structure.

## Requirements

- The server that runs SMANetCom or the SMA Relay must have HTTPS access to the Ease DataCenter OpCon Rest-API and to the local OpCon Rest-API.
- The local OpCon user defined in the agent must be able to add jobs to the local bundle schedule (EASE-LOCAL) and to create global properties, which the SEQ task uses.

## Install

To install the EASE ACS connector, complete the following steps:

1. Download the ACS Ease software from the SMA FTP site. The installer is located at `/OpCon Releases/Integrations/Ease/`. Select the required version.
2. Unzip the `ACSEase.zip` file.
3. Copy the `ACSEase` directory to the appropriate location:
   - On-prem installations: Copy to `\SAM\plugins`.
   - Cloud (SMA Relay) installations: Copy to `\Relay\plugins`.
4. Restart the services: **SMA OpCon Service Manager** and **SMA OpCon RestAPI** for on-prem installations, or the **SMA OpCon Relay** service for SMA Relay installations.

**Version 25.0.1 and later**

5. In Deploy, import the `\workflows\EASE-LOCAL.json` file.
6. Edit the `EASE-LOCAL.json` file:
   a. Change the primary machine name to match the target OpCon system.
   b. Change the user ID used to run the tasks.
7. Import the updated `EASE-LOCAL.json` file into Deploy using the **File Import** function.
8. Deploy the `EASE-LOCAL` schedule to the target OpCon system. The deployment includes the required sub-schedules.

The EASE ACS connector is installed and ready for configuration.

## Exception handling

**The EASE ACS connector does not appear in Solution Manager after installation.**
Verify that the `ACSEase` directory was copied to the correct `plugins` folder for your installation type: `\SAM\plugins` for on-prem OpCon or `\Relay\plugins` for SMA Relay. Restart the SMA OpCon Service Manager and SMA OpCon RestAPI services.

**The EASE-LOCAL schedule fails to deploy or tasks reference an incorrect machine.**
Verify that the machine name in `EASE-LOCAL.json` was updated to match the target OpCon system before importing the file into Deploy. If the machine name was not updated, edit the `EASE-LOCAL.json` file, re-import it into Deploy using the **File Import** function, and redeploy.

**Related topics:**

- [EASE ACS overview](overview)
- [ACS Ease operation](ACSEaseOperation)
