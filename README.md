<h1>Security Operations Center Home Lab</h1>


<h2>Description</h2>
This project consists of setting up a fully functioning cloud-based SOC environment, using ELK as the SIEM and implementing osTicket for ticketing. I monitored network traffic, simulated attacks to test incident response processes, and responded to alerts generated.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Elasticsearch</b>
- <b>Kibana</b>
- <b>osTicket</b>
- <b>Mythic</b> (C2 Framework)

<h2>Environments Used </h2>

- <b>Vultr Cloud</b>
- <b>Kali Linux</b>
- <b>Ubuntu</b>
- <b>Windows</b>

<h2>Project walk-through:</h2>

<p align="center">
VPN Network Diagram: <br/>
<img src="https://i.imgur.com/9bgnKz0.jpeg" height="80%" width="80%" alt="Virtual Private Network Diagram"/>
          <p>   Created a network diagram to show the virtual private network and the connections within the network</p>  
<br />
<br />
External Connections to VPN:  <br/>
<img src="https://i.imgur.com/fK9H4qp.jpeg" height="80%" width="80%" alt="External Connections to VPN"/>
<p> Created a diagram to show external devices and servers connecting to the vpn</p>
<br />
<br />
<br />
Ubuntu VM deployed with Elasticsearch: <br/>
<img src="https://i.imgur.com/pvVYyv2.jpeg" height="80%" width="80%" alt="VPC w/ Ubuntu VM "/>
<p>In this stage, I created a Virtual Private Cloud (VPC) in Vultr, deployed an Ubuntu virtual machine, and installed Elasticsearch to serve as part of the ELK Stack. After configuring the Elasticsearch settings for external access, I implemented firewall rules to restrict access to the machine, ensuring only authorized connections. Finally, I started the Elasticsearch service and verified that it was running successfully.</p>
<br />
<br />
<br />
Kibana installation & setup:  <br/>
<img src="https://i.imgur.com/pioyqbu.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/yh4XEe9.jpeg" height="80%" width="80%" />
<p>Downloaded and installed Kibana on the existing Ubuntu virtual machine, then configured Kibana by updating the server port and host in its configuration file. Enabled and started Kibana as a service. Generated an Elasticsearch enrollment token and used it to connect Kibana to Elasticsearch. Adjusted firewall rules to allow access to port 5601, then logged into Kibana successfully. Finally, configured encryption keys for Kibana to ensure persistent settings for saved objects and security.</p>
<br />
<br />
<br />
Windows Server seperated from VPC:  <br/>
<img src="https://i.imgur.com/o2grXTy.jpeg" height="80%" width="80%" />
<p>Deployed a Windows Server 2022 on Vultr using the shared CPU option. The server was set up without being part of the Virtual Private Cloud (VPC) to isolate it from other critical infrastructure. Configured the server and verified Remote Desktop Protocol (RDP) access to ensure it's exposed to the internet. The server will start generating logs for future analysis of unsuccessful login attempts.</p>
<br />
<br />
<br />
Fleet Server with Elastic Agent and Windows Server enrolled:  <br/>
<img src="https://i.imgur.com/uXz0c3B.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/ypcURGK.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/T5iRfce.jpeg" height="80%" width="80%" />
<p>Deployed an Ubuntu server to act as the Fleet Server in the cloud and configured it to manage agents centrally. Installed and configured the Elastic Agent on the Fleet Server and addressed connection issues by adjusting firewall rules for both the Fleet Server and Elasticsearch (port 9200 and 8220). Successfully enrolled the Windows Server into the Fleet, allowing for system logs and metrics collection. Finally, captured initial authentication logs from the Windows server, verifying the setup.</p>




<br />
<br />
Sysmon installation:  <br/>
<img src="https://i.imgur.com/MlnUpcj.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/KcM0yLC.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/NLHMqsW.jpeg" height="80%" width="80%" />
<p>Installed and configured Sysmon on the Windows Server. Downloaded Sysmon from Microsoft Learn and retrieved a popular configuration file from GitHub. Using PowerShell, installed Sysmon as a service and confirmed its successful setup by checking the Windows Event Viewer for Sysmon logs. Verified that Sysmon was capturing network connections and generating logs for further monitoring.</p>
<br />
<br />
<br />
Intergrated Sysmon & Microsoft Defender:  <br/>
<img src="https://i.imgur.com/9FOaHUI.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/rOaZ4Js.jpeg" height="80%" width="80%" />
<p>Added integrations for Sysmon and Microsoft Defender into the Elasticsearch instance, allowing the collection of logs from custom Windows Event channels. Configured specific event IDs for ingestion, such as 1116, 1117, and 50001, for more targeted logging. Troubleshooting included ensuring connectivity between agents and Elasticsearch by allowing incoming connections on port 9200. Verified Sysmon and Defender logs were successfully ingested into the instance.</p>
<br />
<br />
<br />
Ubuntu SSH server Authentication Logs:  <br/>
<img src="https://i.imgur.com/ZgyesUv.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/SzLhOqG.jpeg" height="80%" width="80%" />
<p>Deployed an Ubuntu server on Vultr for monitoring SSH authentication logs. Configured the server, updated repositories, and used PowerShell to SSH into the instance. Accessed authentication logs in /var/log/auth.log to identify failed login attempts, specifically filtering for failed root login attempts. Used the grep and cut commands to extract and display the failed login IP addresses. Prepared the server for monitoring failed brute-force attacks and planned to install the Elastic Agent for log forwarding to Elasticsearch.</p>
<br />
<br />
<br />
Alert & Dashboard creation for SSH activity:  <br/>
<img src="https://i.imgur.com/Pe2DPyq.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/9TMIaOU.jpeg" height="80%" width="80%" />
<p>Ingested logs from the SSH server into Elasticsearch and queried for brute force activity by filtering logs based on agent name, user, and source IP to identify failed authentication attempts. Saved the search as "SSH Failed Activity" and created an alert for brute force attempts, setting thresholds to trigger the alert when more than five failed login attempts occur within five minutes. Additionally, created a dashboard in Elasticsearch Maps to visualize the geographic source of these attempts using the source IP’s geolocation. Duplicated the dashboard to track successful SSH authentication attempts. This setup allows for both alerting on brute force activity and monitoring the source of the attacks in real time.After successfully ingesting logs from our SSH server into the Elasticsearch instance, the next step was to do the same for the RDP server. By filtering for event ID 4625, which represents failed login attempts, we identified multiple failed authentications. Key fields such as source IP and username were added to the table for detailed monitoring. A saved search, "RDP Failed Activity," was created to track these events.

To further strengthen monitoring, an alert for RDP brute force attempts was created, similar to the SSH brute force alert. This alert was configured to trigger after five failed login attempts within five minutes. Finally, a dashboard was developed for visualization, allowing for easy tracking of where these attacks are originating.
This process emphasizes the importance of securing exposed services such as SSH and RDP by ensuring strong passwords, multi-factor authentication (MFA), and limited access, as they are prime targets for brute force attacks.</p>

<br />
<br />
<br />
Attack Diagram: <br/>
<img src="https://i.imgur.com/5z9BUMl.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/cHHsWah.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/BpgyWXU.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/U0typtC.jpeg" height="80%" width="80%" />
<p>Created an attack diagram using draw.io to map out a cyber attack involving a Mythic C2 server, Windows server, and an attacker laptop running Kali Linux. The process included six phases: RDP brute force for initial access, running discovery commands, disabling Windows Defender for defense evasion, downloading and executing a Mythic agent via PowerShell for execution, establishing a C2 session, and exfiltrating a fake password file (passwords.txt). This diagram served as a visual guide for planning and executing the attack path in a controlled environment. Now it's time to attack!!!</p>
<br />
<br />
<br />


Mythic Server Intialization: <br/>
<img src="https://i.imgur.com/F70KHGl.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/6tEEO0V.jpeg" height="80%" width="80%" />
<br />

Brute Force process: <br/>
<img src="https://i.imgur.com/pDtFS5s.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/fBvF9hv.jpeg" height="80%" width="80%" />
<br />
C2 connection Established: <br/>
<img src="https://i.imgur.com/OBrvihs.jpeg" height="80%" width="80%" />
<br />
File Downloaded from host: <br/>
<img src="https://i.imgur.com/Ry8Qbqk.jpeg" height="80%" width="80%" />
<p>I deployed a Mythic C2 server using Vultr and configured it with Docker and Kali Linux. The attack involved brute forcing an RDP login on a Windows server, performing discovery commands, disabling Windows Defender, and executing a Mythic agent generated with a C2 profile. The agent was downloaded via PowerShell, establishing a C2 connection. Using the active session, I exfiltrated a fake password file (passwords.txt) from the Windows server. This process demonstrated the complete attack path, from initial access to exfiltration, using Mythic C2 and Kali Linux.</p>
<br />
<br />
The next steps in this project is the investigation: <br/>
<br />
<br />
<br />
Query for Process Creation(event code:1) & Original file name(Apollo.exe): <br/>
<img src="https://i.imgur.com/L80hlDC.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/77UWFgc.jpeg" height="80%" width="80%" />
<br />
Alert created for Mythic/Apollo agent: <br/>
<img src="https://i.imgur.com/x9HsLVg.jpeg" height="80%" width="80%" />
<br />
Suspicious activity dashboard: <br/>
<img src="https://i.imgur.com/VLRO2zS.jpeg" height="80%" width="80%" />
<p>I created an alert in Elastic for Mythic C2 activity by querying logs for our service host executable and correlating Sysmon event codes. First, I searched for process creation events (event code 1) and extracted the SHA-256 hash and original file name (Apollo.exe). Then, I used these fields to build a query and alert that triggers on process creation involving the Apollo agent. Finally, I created a custom dashboard that monitors suspicious activity, including process creation events for PowerShell and CMD, network connections, and Defender being disabled, offering at-a-glance insights into potentially malicious activity.</p>
<br />
<br />
<br />
Ticketing system: <br/>
<img src="https://i.imgur.com/YAkU8Yr.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/R7YriON.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/e2xwm2E.jpeg" height="80%" width="80%" />
<br />
Elastic Defender: <br/>
<img src="https://i.imgur.com/jYip6R0.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/IhA8ukM.jpeg" height="80%" width="80%" />
<img src="https://i.imgur.com/htj8pnY.jpeg" height="80%" width="80%" />
<p>The setup process started with osTicket, where a Windows server was deployed, and XAMPP was installed to host the osTicket instance. Configuration included editing Apache and PHP settings, creating databases, and setting firewall rules for web server security. osTicket was then integrated with Elasticsearch using an API key, enabling automatic ticket generation for alerts.

Next, the investigation of SSH brute force attacks was demonstrated by querying Elasticsearch for specific IPs and users involved in the attacks. Tools like AbuseIPDB and GreyNoise were used to assess the threat level of the IPs. Similar methodologies were applied to RDP brute force investigations, where IP reputation was checked, and failed/successful login attempts were analyzed.

The investigation of Mythic C2 focused on process creation and network connections. Sysmon telemetry and process GUIDs were used to track C2 agent behavior, file creations, and outbound connections, particularly for the agent named servicehost.exe. Powershell commands and network connections were analyzed to understand the C2 activity timeline.

Lastly, Elastic Defend (Elastic's EDR solution) was installed to monitor and protect endpoints. The tool blocked malicious files like mydfir-30.exe, provided detailed telemetry (file hashes, paths, etc.), and isolated compromised hosts. Elastic Defend's features, such as real-time detection and prevention, were highlighted, along with host isolation.</p>
<br/>
<br/>

<h2>Mitigation & Prevention </h2>

<b>1. Strengthen Access Controls:</b> (RDP and SSH Access Restrictions)
- Limit RDP and SSH access to only trusted IP ranges by configuring strict firewall rules.
- Disable RDP and SSH on public-facing servers whenever possible or restrict to VPN-only access.
- Use secure SSH key-based authentication instead of password-based authentication for SSH.
- Enable MFA for all RDP and SSH connections. This adds an additional layer of security to prevent unauthorized access even if credentials are compromised.
<br/>

<b>2. Password Policies:</b> (Enforce strong password policies to prevent brute force attacks)

- Require complex passwords with a combination of uppercase, lowercase, numbers, and symbols.
- Implement account lockout policies after a set number of failed login attempts, especially for RDP and SSH.
- Regularly rotate passwords and educate users on best password management practices.
<br/>

<b>3. System and Network Hardening:</b> (Patch Management & Disabling Unnecessary Services)

- Regularly update and patch all systems and software, particularly public-facing services like RDP, to address vulnerabilities.
- Use vulnerability scanners (such as Wazuh or Nessus) to detect and patch known vulnerabilities in the environment.
- Disable unnecessary services, such as RDP or certain network protocols, on critical systems that don’t require them.
- Regularly audit exposed ports and services to reduce the attack surface area.
<br/>

<b>4. Implement Network Segmentation:</b> 

- Isolate sensitive resources from public networks using Virtual Private Clouds (VPC) and VLANs.
- Segregate systems with different security requirements into separate network zones to contain potential attacks.
<br/>

<b>5. Monitor and Analyze Logs:</b> (Real-time Monitoring & Log Forwarding to SIEM) 

- Continuously monitor RDP and SSH login attempts by collecting logs and creating alerts for suspicious activity (such as repeated failed logins) using Sysmon and ELK.
- Forward logs from both Windows and Linux servers to Elasticsearch (or another SIEM) for centralized monitoring.
- Use tools like Fail2Ban (for SSH) to automatically ban IPs after repeated login failures, updating firewall rules dynamically.
<br/>

<b>6. Implement Application Whitelisting & Integrity Checking:</b> 

- Limit executable files to only authorized, trusted software using application whitelisting.
- Use integrity checking solutions (e.g., Tripwire) to monitor system files for unauthorized changes, such as in the case of file exfiltration or C2 agent installation.
<br/>

<b>7. Security Awareness Training:</b> 

- Train employees and system administrators on detecting phishing attempts and the importance of avoiding weak RDP and SSH credentials.
- Provide ongoing training about social engineering, phishing threats, and malware to minimize human errors.
<br/>

<b>8. Deploy Endpoint Detection and Response (EDR):</b> 

- Use Elastic Defend or another EDR tool to detect suspicious activity, such as execution of unauthorized scripts or suspicious network connections (like the Mythic C2 server).
- Configure alerts for any abnormal PowerShell or CMD executions, especially in conjunction with disabled security tools like Defender.
<br/>

<b>9.  Incident Response Playbooks:</b> 

- Develop and maintain incident response playbooks for handling brute force attacks, malware infections, and unauthorized remote access attempts.
- Conduct regular tabletop exercises and security drills to ensure that the team is prepared to respond to similar incidents.
<br/>
<p>These steps will help make the environment more resilient to brute force attacks, malware deployment, and unauthorized remote access attempts. They are directly relevant to the attacks I simulated in this project and real-world scenarios, and will significantly strengthen the overall security posture.</p>
<br/>
<h2>Conclusion </h2>

<p>Throughout this project, I demonstrated a wide range of essential SOC analyst skills, including setting up and managing tools for logging, monitoring, and incident response. By integrating ELK with osTicket, I streamlined incident management through an efficient alert-to-ticketing system. I combined host-based and network telemetry monitoring to detect, analyze, and respond to potential threats. Additionally, I implemented a mitigation and prevention strategy to bolster defenses against the types of attacks simulated in this project. Overall, this project highlights my technical expertise in creating a comprehensive SOC environment, showcasing strong investigative techniques, threat detection, response capabilities, and proactive security measures critical for maintaining network security.</p>

























<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
