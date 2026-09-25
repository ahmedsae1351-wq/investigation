---
cover: ../../.gitbook/assets/PacketDetective.jpg
coverY: 0
---

# PacketDetective Lab

The attacker's activity shows intensive use of the SMB protocol, indicating a potential pattern of large data transfer or file access. Therefore, I identified the total number of bytes for the SMB protocol within each PCAP file through Statistics > Protocol Hierarchy, which amounted to 4406.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-23 224357.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-23 224420.png" alt=""><figcaption></figcaption></figure>

***

Authentication through SMB was a critical step in gaining access to the targeted system. Identifying the username used for this authentication will help determine if a privileged account was compromised.

Before proceeding, we must understand the basic steps of the authentication process:

1. **Session Establishment:** The client initiates a connection to the SMB server by sending a Negotiate Protocol request to agree on the SMB version and security features.
2. **User Authentication:**
   * The client provides credentials (username and password) in an SMB Session Setup Request.
   * The server validates the credentials using available authentication methods (NTLM, Kerberos, etc.).
3. **Authorization:** Once authenticated, the server grants or denies access based on the user's permissions and access control lists (ACLs).
4.  **NTLM Authentication Flow**:

    * **Negotiation**: The client sends a negotiation message to the server, indicating the NTLM version and capabilities it supports.
    * **Challenge**: The server generates a random challenge (a nonce) and sends it to the client.
    * **Response**:&#x20;

    1- The client uses the user's password hash to encrypt the challenge and sends the                     encrypted response back to the server.&#x20;

    &#x20;2- The server verifies the response by comparing it with its own calculation. If they match, the user is authenticated.

<figure><img src="../../.gitbook/assets/Pasted image 20250123234355.png" alt=""><figcaption></figcaption></figure>

To search for the username used for authentication on the SMB service, there are several methods. The first method is by looking at the information column, which provides details about each packet.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-23 235850.png" alt=""><figcaption></figcaption></figure>

The second method is by using a direct filter to locate the packets that contain NTLM authentication:

ntlmssp.auth.username&#x20;

ntlmssp

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 000545.png" alt=""><figcaption></figcaption></figure>

As we have seen, the attacker has compromised the administrator account.

***



During the attack, the adversary accessed certain files. Identifying which files were accessed can reveal the attacker's intent.

To determine which file the attacker opened, go to File menu > Export Objects > SMB

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 001613.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 001627 (1).png" alt=""><figcaption></figcaption></figure>

The file name is Event Log. The attacker might have intended to delete the logs to avoid detection or to conceal their actions within the system.

***

Clearing event logs is a common tactic to hide malicious actions and evade detection. Pinpointing the timestamp of this action is essential for building a timeline of the attacker’s behavior.

By default, Wireshark uses a timestamp format calculated in seconds since the first packet was captured. Therefore, we need to change the timestamp format first to achieve what we're looking for.

View > Time Display Format > UTC Date and Time of Day

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 003051.png" alt=""><figcaption></figcaption></figure>

To identify the packet in which the event log was cleared, we need to understand how protocols like **SMB** or **RPC** work, which are used by Windows systems to manage event logs. Typically, event logs are cleared using specific commands over protocols like **DCE/RPC** or **SMB**.

In Windows systems, event logs are typically cleared using the **ElfClearEventLogFileW** or **ElfClearEventLogFileA** functions over the **DCE/RPC** protocol.

These commands are sent through **SMB** or **RPC** packets, so you will need to filter traffic related to these protocols.

**Key RPC Operations for Analysis**:

* **Opnum 0 - ClearEventLog**: Most significant for investigations, as this action indicates an attempt to erase event logs. By identifying the timestamp of this call, we can mark when the attacker tried to cover their tracks.
* **Opnum 7 - ReadEventLog**: Useful for checking if the attacker accessed logs before clearing them. Attackers may review logs to verify if their actions were recorded.
* **Opnum 1 - BackupEventLog**: Relevant if there’s an attempt to back up logs, possibly indicating that the attacker wanted to review or manipulate logs offline.
* **Opnum 4 and 5 - GetNumberOfEventLogRecords and GetOldestEventLogRecord**: Important for detecting reconnaissance activity, where attackers may be assessing the logs’ size and content before further actions.

o identify the packet, we will use the filter `dcerpc.opnum == 0`.

<figure><img src="../../.gitbook/assets/Pasted image 20250124004327.png" alt=""><figcaption></figcaption></figure>

To display the timestamp of the packet,&#x20;

View > Time Display Format > UTC Date and Time of Day

&#x20;or&#x20;

**Ctrl+Alt+7**

<figure><img src="../../.gitbook/assets/Pasted image 20250124004525.png" alt=""><figcaption></figcaption></figure>

***



The attacker used "named pipes" for communication, suggesting they may have utilized Remote Procedure Calls (RPC) for lateral movement across the network. RPC allows one program to request services from another remotely, which could grant the attacker unauthorized access or control.

When analyzing the packets, the **ISystemActivator** protocol column and the **RemoteCreateInstance** information column caught my attention.&#x20;

What is **ISystemActivator**?

**ISystemActivator** is part of **DCOM** (Distributed Component Object Model) in Windows. Its function is to **activate services or objects** on a remote machine over the network. **ISystemActivator** allows applications to start remote objects or services, such as running a service on another machine as if it were running on the local machine.

In **Wireshark**, **ISystemActivator** appears when a request is made to activate an object or service remotely, and it can interact with **named pipes** such as `\PIPE\svcctl` (for service management). Adding **ISystemActivator** in the analysis means it is being used to activate services or objects remotely over the network.

**Named Pipes in Windows**

In Windows, **Named Pipes** are used as a method for inter-process communication. Pipes act like virtual files where one process can write data, and another can read it. Each pipe is identified by a unique name, such as `\PIPE\svcctl` or `\PIPE\atsvc`.

**Examples of Pipes:**

* `\PIPE\svcctl`: Used for remote service management.
* `\PIPE\samr`: Used for accessing stored passwords.
* `\PIPE\atsvc`: Used for remote task scheduling.

**ISystemActivator and DCOM:** In the analyzed packet, we found a protocol called **ISystemActivator**, which is part of **DCOM** and is used to activate services or objects on a remote machine. Pipes like `\PIPE\atsvc` or `\PIPE\svcctl` might be used in this context.

To search for the pipes in the packets, you can use the following filter:

`frame contains 5c:00:50:00:49:00:50:00:45`

This filter looks for **\PIPE**.

**Pipe Used:** In this analysis, the **\PIPE\atsvc** related to **task scheduling** was found.

<figure><img src="../../.gitbook/assets/Pasted image 20250124012832.png" alt=""><figcaption></figcaption></figure>

***

Measuring the duration of suspicious communication can reveal how long the attacker maintained unauthorized access, providing insights into the scope and persistence of the attack. Therefore, we will determine the connection duration between the specified addresses 172.16.66.1 and 172.16.66.36.

To do this, follow these steps:

1. Open **Statistics Menu**.
2. Select **Conversation**.
3. Go to the **IPv4** tab.

This will display the conversation details, including the duration of the communication between the two IP addresses.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 013403.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 013457 (1).png" alt=""><figcaption></figcaption></figure>

***

The attacker used a non-standard username to set up requests, indicating an attempt to maintain covert access. Identifying this username is essential for understanding how persistence was established.

I searched for the username to identify the name.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 014715.png" alt=""><figcaption></figcaption></figure>

To find the executable file:      File menu > Export Objects > SMB

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-24 015142.png" alt=""><figcaption></figcaption></figure>

