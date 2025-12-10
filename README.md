# Azure Honeypot SIEM Project - Attack Map Visualisation 

## Introduction
This project demonstrates how to deploy and monitor a honeypot environment in Microsoft Azure using Microsoft Sentinel. I configured an intentionally exposed Windows virtual machine to attract unauthorised login attempts, forwarded the security logs into a Log Analytics Workspace, enhanced the attacker IP addresses with geolocation data using a Sentinel watchlist, and visualised global attack patterns in a Sentinel Workbook. The goal of this project is to practice cloud security monitoring, KQL analysis, and SIEM visualisation in a realistic environment.

---

## Technologies and Azure Components Used
- Microsoft Azure  
- Microsoft Sentinel (SIEM)  
- Azure Monitor Agent   
- Azure Virtual Machine (Windows 10)   
- Virtual Network (VNet)  
- Log Analytics Workspace  
- Kusto Query Language (KQL)   
- Windows Event Viewer   
- Network Security Groups (NSG)  
- Microsoft Sentinel  
- Watchlists (Sentinel feature)  
- Workbooks (Sentinel dashboarding feature) 

---

## Security Regulations/Standards 
- NIST SP 800-92 - Guide to Computer Security Log Management   
- NIST SP 800-53 - Security and Privacy Controls for Information Systems and Organisations

---

## Methodology

### Environment Setup
I began by creating a Windows 10 virtual machine configured as a honeypot. A Network Security Group (NSG) was modified to intentionally allow all inbound traffic, exposing the VM to internet-wide scanning and login attempts. The Windows Firewall on the host was disabled to ensure all incoming activity could be logged and observed. 

### Log Generation and Collection
After deploying the VM, I generated baseline security events by intentionally failing multiple login attempts. These events were validated inside Windows Event Viewer to confirm proper log creation (Event ID 4625 representing a failed login attempt). A Log Analytics Workspace (LAW) was then created, and Microsoft Sentinel was connected to serve as the SIEM. The Windows Security Events connector (via Azure Monitor Agent) was configured through a Data Collection Rule to forward all security logs to the LAW. 

### Log Analysis with Kusto Query Language (KQL)
Once the logs started to come in, I began analysing them in the LAW using KQL. Initial queries focused on identifying failed login events and extracting attacker IP addresses. This step confirmed the honeypot was receiving real external login attempts. 

### Adding Geolocation to IP Addresses
Since raw logs do not include geographic information, I imported a GeoIP dataset into Sentinel as a Watchlist. Using the ipv4_lookup() function, attacker IPs were correlated with their geolocation. This enabled geographic analysis of the attacks. 

### Threat Visualisation
To visualise global attack patterns, I created a custom Sentinel Workbook. A KQL query was added to a map visualisation component, producing an interactive attack map that displays the geolocation and amount of login attempts in real time.

---

## Architecture Diagram  
Figure 1: High-level architecture of the Azure honeypot environment, showing how attacker traffic flows into the exposed VM and how logs move through AMA, DCR, Log Analytics, Sentinel, and the Attack Map Workbook.

![Architecture Diagram](images/Screenshot%202025-12-10%20111743.png)

---

## Attack Map Visualisation  
After allowing the honeypot VM to remain exposed to the internet for 24 hours, I created an interactive attack map within Microsoft Sentinel to visualise all inbound authentication attempts. The map collects failed login events (Event ID 4625) and enhances each IP address with geolocation data using the GeoIP watchlist. 

The attack map provides: 
- Real-time geographic distribution of attacking IP addresses  
- Location details  
- Event counts per source

Within 24 hours, attacks originated from multiple regions worldwide, demonstrating how quickly internet-facing systems are scanned and targeted. 

A screenshot of the attack map is shown below:

![Windows VM Attack Map](images/Screenshot%202025-11-19%20190909.png)

---

## Conclusion  
This honeypot project provided a practical view of real-world attack behavior against exposed systems. By collecting and enriching security logs, and visualizing them through a Sentinel attack map, several actionable insights became clear:

1. **Immediate exposure risk:** Even a single internet-facing VM is targeted quickly by automated attacks, underscoring the importance of network hardening and firewall rules.  
2. **Common attack patterns:** Most login attempts were brute-force attacks targeting common usernames, highlighting the need for strong authentication policies, such as multi-factor authentication and unique credentials.  
3. **Geographic awareness:** Enriching logs with GeoIP data allowed identification of attack origins, which can guide threat intelligence and risk prioritization.  
4. **Value of SIEM tools:** Aggregating, enriching, and visualizing logs in Sentinel turned raw events into meaningful, actionable security intelligence.

Overall, this project demonstrates that monitoring, log enrichment, and visualization are crucial for proactive security. It reinforces best practices in network defense, access control, and SIEM-based threat detection, providing clear lessons that can be applied to real-world environments to improve organizational security posture.
