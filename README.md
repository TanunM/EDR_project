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
2. login using the given credi
1.  **Add the Wazuh GPG Key:**
    Open a terminal on your Ubuntu VM and execute the command to import the GPG key, which verifies the authenticity of the Wazuh packages.
    ```bash
    curl -s [https://packages.wazuh.com/key/GPG-KEY-WAZUH](https://packages.wazuh.com/key/GPG-KEY-WAZUH) | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
    ```
2.  **Download and Run the Installation Script:**
    Download the official install script (check documentation for the current version) and run it with the **`-a`** (all components) and **`-i`** (interactive mode) flags.
    ```bash
    curl -sO [https://packages.wazuh.com/4.x/wazuh-install.sh](https://packages.wazuh.com/4.x/wazuh-install.sh) && sudo bash ./wazuh-install.sh -a -i
    ```
3.  **Record Credentials:**
    The script will display the **default credentials** (username and password) for the Wazuh Dashboard upon completion. **Copy these credentials** and your Ubuntu VM's IP address.

---

## Step 2: Access the Wazuh Dashboard and Install Agent (Windows Host)

1.  **Get the Manager's IP Address:**
    On the Ubuntu VM, check the IP address configured for its network adapter (e.g., using `ip a` or `ifconfig`).
2.  **Access the Dashboard:**
    On your Windows Host machine, open a browser and navigate to the Ubuntu VM's IP address using HTTPS:
    ```
    https://<ubuntu-vm-ip>
    ```
    Accept the browser security warning and log in.
3.  **Download and Install the Wazuh Agent:**
    * In the Wazuh Dashboard, go to **Agents** $\rightarrow$ **Deploy new agent**.
    * Select **Windows** as the Operating System.
    * Follow the on-screen instructions to **download the MSI installer** for the Wazuh Agent.
    * Run the MSI installer on the Windows Host using the **default settings**.

---

## Step 3: Register the Agent and Verify Connection

This process uses the manual key exchange method to establish a secure, authenticated connection between the agent and manager.

1.  **Generate Agent Key on the Ubuntu Manager:**
    On the Ubuntu terminal, run the agent management utility:
    ```bash
    sudo /var/ossec/bin/manage_agents
    ```
    * Select **A** (Add agent). Enter an Agent name (e.g., **WindowsHost**).
    * Select **E** (Extract key for a specified agent) for the newly created agent.
    * **Copy the entire output key** (the long string beginning with `AQ...`).
2.  **Apply Key and Manager IP in Windows Agent:**
    * On the Windows Host, open the **Wazuh Agent Manager** GUI.
    * Go to **Manage** $\rightarrow$ **Configuration**.
    * In the **Manager** field, enter the Ubuntu VM's IP address.
    * In the **Agent Key** field, **paste the key** copied in the previous step.
    * Click **Save** and then **Restart** the agent service.
3.  **Verify Onboarding:**
    Return to the Wazuh Dashboard. Navigate to **Agents** and confirm the **WindowsHost** agent is listed with a **Status of Active (Green)**.

---
