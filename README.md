# SOC Home Lab

> Building a fully functional Security Operations Center lab from scratch to develop real, hands-on defensive security skills.  
> Inspired by the [SOC Lab Roadmap](https://mmajeed7864.github.io/soclab), but documented in my own words, at my own pace, with my own problems and fixes.

---

## Why I Built This

Certifications teach you concepts. This lab teaches me what actually breaks, why it breaks, and how to fix it under pressure.

My goal is to walk into a SOC analyst interview and be able to say — with proof — that I have deployed a SIEM, triaged real alerts, investigated endpoints, and written detection rules. Not theory. Actual lab evidence.

---

## Lab Environment

| Machine | OS | Role | IP |
|---|---|---|---|
| Windows Server 2022 | Server 2022 | Domain Controller, AD, DNS, DHCP | 192.168.50.10 |
| Ubuntu Server | 22.04 LTS | SIEM, IDS, Fleet Server | 192.168.50.11 |
| Windows 11 | 22H2 | Domain-joined endpoint (victim machine) | 192.168.50.12 |

All VMs run in VirtualBox on a host-only network (`192.168.50.0/24`) with DHCP reservations so IPs stay fixed across reboots.

---

## Tools Deployed

| Tool | Purpose |
|---|---|
| Elasticsearch 8.x | Log storage and search |
| Kibana | SIEM dashboard and visualization |
| Suricata IDS | Network intrusion detection |
| Elastic Fleet | Centralized agent management |
| Wazuh | Endpoint detection and response (EDR) |
| Sysmon | Deep Windows telemetry |
| Splunk | Second SIEM for SPL investigation practice |
| Active Directory | Identity, domain, and user management |
| Kali Linux | Attack simulation |
| Wireshark / tcpdump | Packet capture and analysis |

---

## Progress Tracker

| Phase | Track | Topic | Status | Log |
|---|---|---|---|---|
| 1 | Lab Setup | VM setup, networking, AD, DHCP | ✅ Complete | [log](Phase-1-Lab-Setup/log.md) |
| 2 | Lab Setup | Elastic SIEM + Suricata + Fleet | ✅ Complete | [log](Phase-2-Lab-Setup/log.md) |
| 3 | Lab Setup | Wazuh EDR | 🔧 In Progress | — |
<!-- 
| 4 | SOC L1 Workflow | Windows, Linux, network log fundamentals | ⬜ Upcoming | — |
| 5 | SOC L1 Workflow | Scripted attack loop + high-volume triage | ⬜ Upcoming | — |
| 6 | SOC L1 Workflow | SOC ticket writing, escalation, shift handoff | ⬜ Upcoming | — |
| 7 | SOC L1 Workflow | Phishing and email security investigation | ⬜ Upcoming | — |
| 8 | SOC L1 Workflow | Splunk fundamentals | ⬜ Upcoming | — |
| 9 | SOC L1 Workflow | Detection engineering and rule tuning | ⬜ Upcoming | — |
| 10 | Endpoint & Identity | Active Directory environment | ⬜ Upcoming | — |
| 11 | Endpoint & Identity | AD attack simulation + alert triage | ⬜ Upcoming | — |
| 12 | Endpoint & Identity | Endpoint/EDR investigation | ⬜ Upcoming | — |
| 13 | Endpoint & Identity | IAM, hardening, least privilege | ⬜ Upcoming | — |
| 14 | Cybersecurity Analyst | Vulnerability management | ⬜ Upcoming | — |
| 15 | Cybersecurity Analyst | Threat intelligence + IOC enrichment | ⬜ Upcoming | — |
| 16 | Cybersecurity Analyst | Incident response playbooks | ⬜ Upcoming | — |
| 17 | Cybersecurity Analyst | GRC, compliance, audit evidence | ⬜ Upcoming | — |
| 18 | Deep Investigation | Network traffic analysis | ⬜ Upcoming | — |
| 19 | Deep Investigation | Malware triage + sandbox analysis | ⬜ Upcoming | — |
| 20 | Deep Investigation | Digital forensics + memory basics | ⬜ Upcoming | — |
| 21 | Deep Investigation | Threat hunting | ⬜ Upcoming | — |
| 22 | Deep Investigation | External SOC practice | ⬜ Upcoming | — |
| 23 | Cloud Security | AWS CloudTrail + GuardDuty | ⬜ Upcoming | — |
| 24 | Cloud Security | Microsoft Sentinel + Defender | ⬜ Upcoming | — |
| 25 | Automation | Python automation + SOAR | ⬜ Upcoming | — |
| 26 | Job Prep | Portfolio, resume, interview prep | ⬜ Upcoming | — |
-->

---

## What Makes This Different From Just Following a Tutorial

- Every phase is documented with the **actual problems I come across**, not just the happy path
- I explain **what each tool does and why it matters** in my own words
- Screenshots are included as **proof of working output**, not stock images
- This is a **living document** — updated as I go, mistakes included

---

## Skills Being Built

`SIEM` `Elastic Stack` `Suricata IDS` `Wazuh XDR` `Splunk` `Active Directory`  
`Log Analysis` `Alert Triage` `Threat Detection` `Incident Response`  
`Linux Administration` `Network Security` `Endpoint Investigation`  
`Detection Engineering` `KQL` `SPL` `MITRE ATT&CK` `Python Automation`

---

## Reference

Roadmap source: [mmajeed7864.github.io/soclab](https://mmajeed7864.github.io/soclab)  
All lab work is original — I use the roadmap as a guide, not a script.
