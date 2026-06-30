# Phase 2 — Elastic SIEM + Suricata IDS + Fleet Server

**Date:** June 27, 2026  
**Time spent:** ~3 hours  
**Status:** Complete

---

## What This Phase Is About

Phase 2 turns the Ubuntu Server into a working SIEM. By the end of this phase, real network traffic from the lab is being captured, analyzed, and stored and you can search and visualize it in a browser.

Three tools get installed here:

- **Elasticsearch** — the database that stores all logs and events
- **Kibana** — the web interface you use to search and visualize everything
- **Suricata** — a network IDS that watches all traffic and fires alerts on suspicious activity
- **Elastic Fleet** — the agent manager that lets you enroll machines and send their logs to Elastic

Together these form the core of a real SIEM stack.

---

## What I Did

### Elasticsearch

Added the official Elastic 8.x repository and installed Elasticsearch. During install, it auto-generated a superuser password which this is only shown once and is needed to log into Kibana.

Key commands:
```bash
wget -q https://artifacts.elastic.co/GPG-KEY-elasticsearch -O /tmp/elastic.key
sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg /tmp/elastic.key
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update && sudo apt install elasticsearch -y
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```

Verified it was running and responding:
```bash
curl -k https://localhost:9200 -u elastic
# Returns cluster name, UUID, and version — confirms Elasticsearch is live
```

### Kibana

Installed Kibana and configured it to listen on all interfaces so it's accessible from the host machine browser (not just localhost).

```bash
sudo apt install kibana -y
# In /etc/kibana/kibana.yml:
# server.port: 5601
# server.host: "0.0.0.0"
sudo systemctl enable kibana
sudo systemctl start kibana
```

Generated an enrollment token from Elasticsearch and used it to connect Kibana on first login:
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

Kibana is now accessible at `http://192.168.50.11:5601` from any machine on the lab network.

### Suricata IDS

Installed Suricata from the official PPA, configured it to monitor the `eth0` interface, and set `HOME_NET` to the lab subnet.

```bash
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update && sudo apt install suricata -y
# In /etc/suricata/suricata.yaml:
# HOME_NET: "[192.168.50.0/24]"
# af-packet interface: eth0
sudo suricata-update        # downloads latest detection rules
sudo systemctl enable suricata
sudo systemctl start suricata
```

Config test to verify everything loaded correctly:
```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v
# Output: "Configuration provided was successfully loaded."
```

### Elastic Fleet Server

Downloaded Elastic Agent 8.17.0 and installed it as a Fleet Server on Ubuntu. Generated the enrollment command from Kibana's Fleet setup page.

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.17.0-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.17.0-linux-x86_64.tar.gz
cd elastic-agent-8.17.0-linux-x86_64

sudo ./elastic-agent install \
  --fleet-server-es=https://192.168.50.11:9200 \
  --fleet-server-service-token=<token from Kibana> \
  --fleet-server-policy=fleet-server-policy \
  --fleet-server-es-ca-trusted-fingerprint=<fingerprint from Kibana> \
  --fleet-server-port=8220
```

### Suricata Integration in Kibana

Added the Suricata integration through Kibana's Integrations menu, pointing it at the eve.json log file:

- Log path: `/var/log/suricata/eve.json`
- Policy: `fleet-server-policy`

---

## Problems I Hit

**Problem:** GPG key error when adding the Elastic repository — `NO_PUBKEY D27D666CD88E42B4`  
**Cause:** The `curl -fsSL | sudo gpg` pipe failed silently, leaving an empty or invalid key file  
**Fix:** Downloaded the key separately with `wget`, then converted it with `gpg --dearmor` as a separate step. Always verify the key file exists and has size > 0 before adding the repo.

**Problem:** Kibana version in the Fleet install command (8.19.17) didn't match the Elastic Agent already downloaded (8.17.0)  
**Cause:** Kibana generates the install command with the latest available version, not the one already on disk  
**Fix:** Skipped the download lines from Kibana's command and ran only the `./elastic-agent install` portion using the 8.17.0 agent already extracted

**Problem:** Suricata `fast.log` showed no alerts after running a test  
**Cause:** The external test site (`testmynids.org`) was unreachable; also, Suricata is quiet when there's no suspicious traffic — that's normal behavior  
**Fix:** Confirmed Suricata was working by checking `eve.json` for stats events and running `sudo suricata -T` to validate the config loaded correctly. `kernel_packets: 469444` in the stats log confirmed it was capturing traffic.

---
<!--
## Proof It Works

**Kibana Discover — 563 Suricata events flowing in real time:**

> *(add screenshot: kibana-suricata-563-events.png)*

**Fleet → Agents tab showing Ubuntu as Healthy:**

> *(add screenshot: fleet-agent-healthy.png)*

**Elasticsearch responding on port 9200:**

> *(add screenshot: elasticsearch-curl-response.png)*
-->
---

## Final State

```
sudo systemctl status elasticsearch   # active (running)
sudo systemctl status kibana          # active (running)
sudo systemctl status suricata        # active (running)
sudo systemctl status elastic-agent   # active (running)
```

All four services enabled and running. Snapshot taken: `Ubuntu-Phase2-Complete`

---

## What I Learned

- Elasticsearch is just a database — it has no interface. Kibana is what you actually use day to day as an analyst
- Suricata doesn't generate alerts just because it's running, it needs actual suspicious traffic matching its rules. Quiet logs don't mean it's broken
- The Elastic Fleet install command from Kibana always references the latest version. If you downloaded an older agent, skip the download lines and just run the install command with your existing agent
- GPG key errors with piped commands are common; separating the download and conversion into two steps is more reliable
- `eve.json` is the main Suricata output file. The `fast.log` is a simplified view, `eve.json` is what gets ingested into Elastic and contains far more detail
