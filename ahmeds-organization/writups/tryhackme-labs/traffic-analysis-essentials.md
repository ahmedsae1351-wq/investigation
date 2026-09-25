---
cover: https://miro.medium.com/v2/resize:fit:720/format:webp/0*GtBefjSEgS8FlWRQ.png
coverY: 0
---

# Traffic Analysis Essentials

## Task1: Introduction <a href="#ff65" id="ff65"></a>

Network Security is a set of operations for protecting data, applications, devices and systems connected to the network. It is accepted as one of the significant subdomains of cyber security. It focuses on the system design, operation and management of the architecture/infrastructure to provide network accessibility, integrity, continuity and reliability. Traffic analysis (often called Network Traffic Analysis) is a subdomain of the Network Security domain, and its primary focus is investigating the network data to identify problems and anomalies.

This room will cover the foundations of Network Security and Traffic analysis and introduce the essential concepts of these disciplines to help you step into Traffic/Packet Analysis.



## Task2: Network Security and Network Data <a href="#id-04f8" id="id-04f8"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*kO_df3lMiXpVrE-oAHmsiw.png" alt="" height="492" width="700"><figcaption></figcaption></figure>

## Network Security <a href="#id-1288" id="id-1288"></a>

The essential concern of Network Security focuses on two core concepts: authentication and authorisation. There are a variety of tools, technologies, and approaches to ensure and measure implementations of these two key concepts and go beyond to provide continuity and reliability. Network security operations contain three base control levels to ensure the maximum available security management.

## **Base Network Security Control Levels:** <a href="#id-6727" id="id-6727"></a>

### Physical Physical: security controls prevent unauthorised physical access to networking devices, cable boards, locks, and all linked components. <a href="#id-5d82" id="id-5d82"></a>

### **Technical :** Data security controls prevent unauthorised access to network data, like installing tunnels and implementing security layers. <a href="#id-84b1" id="id-84b1"></a>

### Administrative Administrative security controls provide consistency in security operations like creating policies, access levels and authentication processes. <a href="#e2d4" id="e2d4"></a>

There are two main approaches and multiple elements under these control levels. The most common elements used in network security operations are explained below.

**The main approaches:**

Access Control : The starting point of Network Security. It is a set of controls to ensure authentication and authorisation.

Threat Control : Detecting and preventing anomalous/malicious activities on the network. It contains both internal (trusted) and external traffic data probes.

**The key elements of Access Control:**

**Firewall Protection :** Controls incoming and outgoing network traffic with predetermined security rules. Designed to block suspicious/malicious traffic and application-layer threats while allowing legitimate and expected traffic.

**Network Access Control (NAC) :** Controls the devices’ suitability before access to the network. Designed to verify device specifications and conditions are compliant with the predetermined profile before connecting to the network.

**Identity and Access Management (IAM)** : Controls and manages the asset identities and user access to data systems and resources over the network.

**Load Balancing :** Controls the resource usage to distribute (based on metrics) tasks over a set of resources and improve overall data processing flow.

**Network Segmentation:** Creates and controls network ranges and segmentation to isolate the users’ access levels, group assets with common functionalities, and improve the protection of sensitive/internal devices/data in a safer network.

**Virtual Private Networks (VPN):** Creates and controls encrypted communication between devices (typically for secure remote access) over the network (including communications over the internet).

**Zero Trust Model:** Suggests configuring and implementing the access and permissions at a minimum level (providing access required to fulfil the assigned role). The mindset is focused on: “Never trust, always verify”.

The key elements of Threat Control:

**Intrusion Detection and Prevention (IDS/IPS):** Inspects the traffic and creates alerts (IDS) or resets the connection (IPS) when detecting an anomaly/threat.

**Data Loss Prevention (DLP):** Inspects the traffic (performs content inspection and contextual analysis of the data on the wire) and blocks the extraction of sensitive data.

**Endpoint Protection:** Protecting all kinds of endpoints and appliances that connect to the network by using a multi-layered approach like encryption, antivirus, antimalware, DLP, and IDS/IPS.

**Cloud Security:** Protecting cloud/online-based systems resources from threats and data leakage by applying suitable countermeasures like VPN and data encryption.

**Security Information and Event Management (SIEM):** Technology that helps threat detection, compliance, and security incident management, through available data (logs and traffic statistics) by using event and context analysis to identify anomalies, threats, and vulnerabilities.

**Security Orchestration Automation and Response (SOAR):** Technology that helps coordinate and automates tasks between various people, tools, and data within a single platform to identify anomalies, threats, and vulnerabilities. It also supports vulnerability management, incident response, and security operations.

**Network Traffic Analysis & Network Detection and Response:** Inspecting network traffic or traffic capture to identify anomalies and threats.

Typical Network Security Management Operation is explained in the given table:

Deployment:

* Device and software installation
* Initial configuration
* Automation

Configuration:

* Feature configuration
* Initial network access configuration

Management:

* Security policy implementation
* NAT and VPN implementation
* Threat mitigation

Monitoring:

* System monitoring
* User activity monitoring
* Threat monitoring
* Log and traffic sample capturing

Maintenance:

* Upgrades
* Security updates
* Rule adjustments
* Licence management
* Configuration updates

## Managed Security Services <a href="#d9f7" id="d9f7"></a>

Not every organisation has enough resources to create dedicated groups for specific security domains. There are plenty of reasons for this: budget, employee skillset, and organisation size could determine how security operations are handled. At this point, Managed Security Services (MSS) come up to fulfil the required effort to ensure/enhance security needs. MSS are services that have been outsourced to service providers. These service providers are called Managed Security Service Providers (MSSPs). Today, most MSS are time and cost effective, can be conducted in-house or outsourced, are easy to engage, and ease the management process. There are various elements of MSS, and the most common ones are explained below.

**Network Penetration Testing:** Assessing network security by simulating external/internal attacker techniques to breach the network.

**Vulnerability Assessment:** Assessing network security by discovering and analysing vulnerabilities in the environment.

**Incident Response:** An organised approach to addressing and managing a security breach. It contains a set of actions to identify, contain, and eliminate incidents.

**Behavioural Analysis:** An organised approach to addressing system and user behaviours, creating baselines and traffic profiles for specific patterns to detect anomalies, threats, vulnerabilities, and attacks.

Q\&A:

Q1: Which Security Control Level covers contain creating security policies?

A: Administrative

Q2: Which Access Control element works with data metrics to manage data flow?

A: Load Balancing

Q3: Which technology helps correlate different tool outputs and data sources?

A: SOAR

## Task3: Traffic Analysis <a href="#d6d2" id="d6d2"></a>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*seCAe36RFgKyr-Fw.png" alt="" height="492" width="700"><figcaption></figcaption></figure>

At the top of the task, click the green View Site button.

<figure><img src="https://miro.medium.com/v2/resize:fit:264/1*fr6EHRjNRbtseWT5zLE13A.png" alt="" height="210" width="264"><figcaption></figcaption></figure>

Now you ready to start

<figure><img src="https://miro.medium.com/v2/resize:fit:680/1*9DTLapxzsFD-LvtfMsozzQ.png" alt="" height="596" width="680"><figcaption></figcaption></figure>

**Level-1 is simulating the identification and filtering of malicious IP addresses.**

Now Click the black Start Network Traffic button.

<figure><img src="https://miro.medium.com/v2/resize:fit:680/1*piTpF2qocTN-VDGf0YEj3g.png" alt="" height="596" width="680"><figcaption></figcaption></figure>

When you press the Start Network Traffic Button we will see the traffic running through the network but we have malicious traffic so there is a black button called Restore Network and Log Traffic for further verification click on this button.

<figure><img src="https://miro.medium.com/v2/resize:fit:662/1*0VAxy9K2ScnXwRnrzp5kUg.png" alt="" height="583" width="662"><figcaption></figcaption></figure>

Now we will have traffic running through the network again. This time we will get logs of what is running through the network. Once we have enough logs, you will be instructed to analyze the data to find two IP addresses to filter through the firewall. By looking at the IDS/IPS system, we can see some suspicious IP addresses.

<figure><img src="https://miro.medium.com/v2/resize:fit:586/1*iT_i2oc5hvS3PDZJMoaJyw.png" alt="" height="606" width="586"><figcaption></figcaption></figure>

Now we will see that we have two suspicious IP addresses, one for Metasploit traffic and the other for bad traffic. We know that Metasploit is an attack framework so this can’t be good, the same IP address is responsible for the multiple login attempts, so let’s put Metasploit IP address and bad traffic in IDS/IPS System Filter. Once done, click on the “Replay Network Traffic” button:

<figure><img src="https://miro.medium.com/v2/resize:fit:666/1*AG5PDlusCO0FWeUnHjxu6Q.png" alt="" height="474" width="666"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:647/1*_7YH8fHSFHUbAHWqRk_vbA.png" alt="" height="580" width="647"><figcaption></figcaption></figure>

After restarting the Network Traffic, you will have successfully block the malicious traffic. You will get a pop-up window, this window will contain the first flag. Type the answer into the TryHackMe answer field, then click submit.

<figure><img src="https://miro.medium.com/v2/resize:fit:322/1*AZttyYdgyCguWUP-KUOl0g.png" alt="" height="247" width="322"><figcaption></figcaption></figure>

**What is the flag?**

**Answer: THM{PACKET\_MASTER}**

## Level-2 is simulating the identification and filtering of malicious IP and Port addresses. <a href="#e50a" id="e50a"></a>

Now we are tasked with blocking destination ports, we need to get these from the Traffic Analyzer table. If we look at the sus IP addresses from the previous question, along with number five, since it is labeled as Suspicious ARP Behavior. We can see the destination ports they correlate to in the Traffic Analyzer table on the right.

<figure><img src="https://miro.medium.com/v2/resize:fit:416/1*Z6R7P0NaD8P4Nx7IJ6xogw.png" alt="" height="491" width="416"><figcaption></figcaption></figure>

Since we only need the port numbers the port number into the Filter box, then click the blue Add to Filter.

<figure><img src="https://miro.medium.com/v2/resize:fit:567/1*Hr5sGoBolMNpFKZYwK5ciQ.png" alt="" height="868" width="567"><figcaption></figcaption></figure>

Once you have added all the ports, a black Restart Network Traffic button will apper. Click it.

<figure><img src="https://miro.medium.com/v2/resize:fit:554/1*bB-ldgD8vCZd01Htghug_w.png" alt="" height="482" width="554"><figcaption></figcaption></figure>

After restarting the Network Traffic, you will have successfully block the malware. You will get a pop-up window, this window will contain the first flag. Type the answer into the TryHackMe answer field, then click submit.

<figure><img src="https://miro.medium.com/v2/resize:fit:287/1*urBgjbe3tZkGDYBL3eQSOA.png" alt="" height="225" width="287"><figcaption></figcaption></figure>

## What is the flag? <a href="#id-63b9" id="id-63b9"></a>

**Answer: THM{DETECTION\_MASTER}**

Congratulations! You just finished the “Traffic Analysis Essentials” room.

In this room, we covered the foundations of the network security and traffic analysis concepts:

* Network Security Operations
* Network Traffic Analysis

Now, you are ready to complete the **“Network Security and Traffic Analysis”** module
