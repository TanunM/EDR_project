# Wazuh_EDR_project

## Introduction

This is a documents of Wazuh Endpoint Detection environment in a virtualized, on-premise setting. The project uses the Wazuh Manager and Dashboard on a server and Wazuh Agent on Windows endpoint. The primary security function demonstrated is File Integrity Monitoring, allowing the detection of unauthorized changes to a designated folder in real-time.

## Objective
The core objective is to successfully establish the foundational components of a Wazuh and validate its core security capability by:
1.  Installing the Wazuh Manager on a server.
2.  Enrolling a Windows 10 host as a monitored agent.
3.  Configuring the agent to use the Syscheck module for real-time File Integrity Monitoring on a folder.
4.  Verifying that security alerts are generated and visible in the Wazuh Dashboard upon file changes.

## Requirements and Tools
| Category | Tools and Requirements |
| --- | --- |
| Virtualization | VirtualBox |
| Operating Systems | Wazuh OVA,  Microsoft Windows 10 |
| Networking | Bridged networking |

## Step 1: Install Wazuh OVA
1. Download the Wazuh OVa from the [Wazuh site](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html)
2. login using the given credentials
3. Using the commandline get the IP of the device and from the host machine browser access the GUI.
4. The link address is https://<wazuh OVA IP>/app/login
5. Login to the dashboard using the default credentials
   
## Step 2: Install Agent in Windows Host
Download and Install the Wazuh Agent from the [Wazuh website](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html) You can use one of the 2 methods to link windows agent to the dashboard.

### Manual Register
1. On the WWazuh OVA terminal, run the agent management utility:
    ```bash
    sudo /var/ossec/bin/manage_agents
    ```
2. Select **A** (Add agent). Enter an Agent name.
3. Select **E** (Extract key for a specified agent) for the newly created agent.
4. Copy the entire output key.
5. Apply Key and Manager IP in Windows Agent GUI.

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/3.%20wazuh_agent.png'/>

### Automated Register
1. In the Wazuh Dashboard, go to **Agents --> Deploy new agent**.
2. Select **Windows** as the Operating System.
3. Follow the on-screen instructions to **download the installer** for the Wazuh Agent and to add it to the dashboard.
4. Run the command in powershell or cmd.
5. start the Wazuh agent.

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/1.%20add_agent.png'/>

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/2.%20agent_download.png'/>

Finally, verify the onboarding by refreshing the dashboard and navigating to the Agents section. A new active device will be shown with the appointed name.

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/4.%20agent_active.png'/>

## Step 3: Simulate suspicious login attempt.
1. Logout from the host machine and attempt to login using incorrect password
2. After few attempt login and go to the wazuh dashboard.
3. You will see new alerts generated. Search for rule id 60204, which is triggered for failed attempt.
4. This way you can monitor event log.

## Step 4: Configure File Integrity Monitoring
We will configure the Windows Agent's **Syscheck** module to monitor a custom folder in real-time.
1. On the Windows Host, create a simple folder in your desired folder like Desktop.
2. Copy the path of the folder.
3. Open notepad as administrator.
4. Navigate to
```
C:\Program Files (x86)\ossec-agent\ossec.conf
```
5. In the file add the following line.
```
<directories realtime="yes">**<file path>**</directories>
```
6. Save the file and restart the agent.
7. Now refresh the Wazuh dashboard.

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/5.%20ossec_file.png'/>

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/6.%20restart.png'/>

## Step 4: Generate Test Events
1. On the host machine first add a file to the folder.
2. you will see even generated on the wazuh dashboard
3. Then delete the file to see another event being generated.
4. If you inspect the logs you will see that what changes were made has been logged in the event description.

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/8.%20File_info.png'/>

<img src='https://github.com/TanunM/wazuh_EDR_project/blob/main/gallery/7.%20added.png'/>

## Troubleshooting
* Windows Agent Status is "Never Connected": Firewall blocking port **1514/TCP**.
* Incorrect Manager IP or Agent Key: Verify Key & IP
* Agent is Active, but No FIM Alerts: Incorrect "ossec.conf" syntax or agent restart failed or incorrect monitored folder path.

## Key Learning
This lab provides hands-on experience with fundamental SIEM operations:
* Establishing a Wazuh Manager to collect and analyze security data from a remote endpoint.
* Monitor log events for unauthorized login attempt.
* Understanding the role of the Wazuh Agent as the lightweight component for gathering and forwarding local security events.
* Practical implementation of FIM to detect and alert on changes to a critical security control.
* Reinforcing the importance of proper network configuration and firewall rules in a security architecture.

## Reference
* [Wazuh EDR](https://www.youtube.com/watch?v=QT81wcuoRFY)
