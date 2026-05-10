# wazuh-siem-lab
# Wazuh SIEM Threat Detection Lab

A fully isolated attack/defense lab built to simulate real-world cyberattacks and detect them using a self-configured SIEM. This project covers network architecture, agent deployment, live attack execution, and custom detection rule authoring.

---

## Lab Architecture

| Machine | Role | OS |
|---|---|---|
| Wazuh Server | SIEM / Log Aggregator | Amazon Linux 2023 (OVA) |
| Kali Linux | Attacker | Kali Linux 2026.1 |
| Metasploitable2 | Vulnerable Target | Ubuntu 8.04 |

**Network:** All three VMs run on an isolated Host-Only network (`192.168.56.0/24`) with no internet exposure. Kali and Wazuh have a secondary NAT adapter for setup only.

---

## Tools Used

- **Wazuh 4.14.5** — SIEM, log analysis, alert generation, custom rules
- **Nmap** — Network reconnaissance and port scanning
- **Hydra** — FTP brute-force credential attacks
- **Oracle VirtualBox** — Virtualization and isolated network architecture
- **Kali Linux** — Attack platform
- **Metasploitable2** — Intentionally vulnerable target

---

## What I Built

### 1. Isolated Lab Network
Configured a three-VM environment using VirtualBox Host-Only networking. Metasploitable2 is intentionally restricted to the internal network only — no NAT, no internet access — to simulate a real isolated target environment.

### 2. Wazuh SIEM Deployment
Deployed the Wazuh 4.14.5 OVA and accessed the web dashboard via the Host-Only network. Configured the Wazuh agent on Kali Linux, pointing it to the SIEM server for real-time log shipping.

### 3. Attack Execution

**Network Reconnaissance (Nmap)**
```bash
sudo nmap -sV -O 192.168.56.101
```
Enumerated all open ports and services on Metasploitable2, mapping the attack surface prior to exploitation. Identified 20+ open services including FTP, MySQL, VNC, IRC, and HTTP.

**FTP Brute-Force (Hydra)**
```bash
hydra -l msfadmin -p msfadmin ftp://192.168.56.101
```
Executed a credential attack against the FTP service on Metasploitable2. Successfully authenticated and demonstrated weak credential vulnerability.

### 4. SIEM Detection & Alerting
The Wazuh agent on Kali generated **300+ alerts** from attack activity including:
- Authentication failures and successes
- Host-based anomaly detection (rootcheck)
- Suspicious port activity
- MITRE ATT&CK tagged events: **T1046** (Network Service Scanning), **T1110** (Brute Force)

### 5. Custom Detection Rules
Authored custom XML detection rules in `/var/ossec/etc/rules/local_rules.xml`:

```xml
<group name="local,syslog,sshd,">
  <rule id="100002" level="12">
    <if_sid>533</if_sid>
    <description>Suspicious port activity detected - possible attack tool</description>
    <mitre>
      <id>T1046</id>
    </mitre>
  </rule>
</group>
```

Rule 100002 successfully fired at **level 12 (high severity)** and appeared in the Wazuh Threat Hunting dashboard confirming end-to-end detection.

---

## Key Takeaways

- Gained a defender's perspective on attacks I executed as an attacker — watching Hydra and Nmap generate live SIEM alerts makes the threat landscape concrete
- Understood how SIEM agents, log shippers, and alerting engines work together in a real environment
- Learned how to write and validate custom detection rules mapped to MITRE ATT&CK
- Troubleshot real infrastructure issues: network binding, service initialization, agent configuration, and XML rule syntax

---

## Certifications
CompTIA Security+ · Network+ · A+ · Tech+ · CSIS · CIOS · Cisco Networking CompCert · TestOUT Security PRO · Linux PRO · PC PRO

*CySA+ in progress*

---

## Connect
- LinkedIn: [linkedin.com/in/kevin-escandon](https://linkedin.com/in/kevin-escandon)
- Email: kevinchristian.escandon@gmail.com
