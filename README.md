# Splunk Basics — Did you SIEM?  

In this challenge from TryHackMe's *Advent of Cyber*, I used Splunk to investigate a simulated attack against a web server. The goal was to practice log analysis, threat hunting, and SIEM skills by uncovering how the attacker compromised the system and what data was exfiltrated.  

---

## Questions to Answer
- What is the attacker IP found attacking and compromising the web server?  
- Which day was the peak traffic in the logs? (Format: YYYY-MM-DD)  
- What is the count of Havij user_agent events found in the logs?  
- How many path traversal attempts to access sensitive files on the server were observed?  
- Examine the firewall logs. How many bytes were transferred to the C2 server IP from the compromised web server?  

---

## Step 1: Exploring the Logs  
**Question:** What is the attacker IP?  
I started with a simple query:  
```spl
index=main sourcetype=web_traffic
```  
This allowed me to explore the ingested logs and identify the suspicious client IP.  

---

## Step 2: Detecting Traffic in the Logs  
**Question:** Which day was the peak traffic?  
Here I focused on the `client_ip` field and used visualizations to analyze traffic patterns.  
By plotting the data, I could clearly see which day had the highest spike in activity.  

---

## Step 3: SQL Injection  
**Question:** What is the count of Havij user_agent events?  
By filtering for the Havij user agent, I checked the top values and quickly identified the number of events associated with automated SQL injection attempts.  

---

## Step 4: Path Traversal Attempts  
**Question:** How many path traversal attempts were observed?  
Using the attacker’s IP and filtering suspicious paths, I was able to count the events showing attempts to access sensitive files via path traversal.  

---

## Step 5: Firewall Logs and Exfiltration  
**Question:** How many bytes were transferred to the C2 server?  
Although I didn’t capture a screenshot here, the query was:  
```spl
sourcetype=firewall_logs src_ip=<compromised_server> dest_ip=<attacker_ip>
```  
This confirmed that connections were allowed to the C2 server. Finally, I used:  
```spl
| stats sum(size_bytes) AS total_exfiltrated
```  
to calculate the total data exfiltrated.  

---

## What I Learned  
- **Splunk basics**: how to use `index`, `sourcetype`, and field filters to explore logs effectively.  
- **Visualization skills**: using `timechart` and other commands to spot anomalies and peak traffic days.  
- **Threat hunting mindset**: filtering out benign traffic (common browsers) to focus on suspicious user agents like sqlmap, Havij, curl, and wget.  
- **Attack chain analysis**: tracing the attacker’s steps from reconnaissance (probing config files), exploitation (SQL injection, path traversal), to exfiltration and C2 communication.  
- **SIEM value**: seeing how Splunk can correlate web traffic with firewall logs to confirm data exfiltration and attacker infrastructure.  

Overall, this exercise reinforced how SIEM tools are powerful for detecting, investigating, and responding to security incidents. It was a hands‑on way to practice log analysis, anomaly detection, and incident response workflows.  

---
MORE: https://medium.com/@andreymsdev/splunk-basics-did-you-siem-cb53cf00cd96
