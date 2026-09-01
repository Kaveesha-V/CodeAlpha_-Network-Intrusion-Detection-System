# Network Intrusion Detection System (NIDS) & Prevention Setup

An end-to-end implementation of a Network Intrusion Detection and Prevention System (NIDS/IPS) built using **Suricata** on **Ubuntu 24.04 LTS**. This project demonstrates real-time network packet inspection, signature-based rule crafting, structured log telemetry processing, automated firewall mitigation with `iptables`, and containerized dashboard deployment using Docker.

---

## 📌 Project Overview & Tasks

* **Task 1: NIDS Setup & Engine Deployment:** Installed, configured, and initiated Suricata on active network interfaces to monitor inbound and outbound network traffic in real time.
* **Task 2: Rule Configuration & Threat Detection:** Authored and integrated custom detection rules to capture suspicious network behaviors such as ICMP ping scans, suspicious HTTP probes, and unusual outbound network activity.
* **Task 3: Continuous Traffic & Telemetry Monitoring:** Streamed and parsed live intrusion alerts using standard text logs (`fast.log`) and structured JSON feeds (`eve.json`) filtered via `jq`.
* **Task 4: Intrusion Response Mechanisms (IPS):** Enforced active defense mechanisms at the Linux kernel level by applying `iptables` drop rules to isolate detected malicious IP addresses.
* **Task 5: Threat Visualization (Optional):** Deployed a containerized **EveBox** dashboard via Docker to aggregate, search, and visualize detected attack vectors.

---

## 🛠️ Environment & Prerequisites

* **Operating System:** Ubuntu 24.04 LTS (Oracle VirtualBox)
* **NIDS Engine:** Suricata
* **Packet Filtering / Firewall:** Netfilter (`iptables`)
* **Log Parsing Utilities:** `jq`, `tail`, `grep`
* **Container Engine:** Docker

---

## 📂 Project Structure

```text
.
├── rules/
│   └── local.rules          # Custom Suricata intrusion detection signatures
├── config/
│   └── suricata.yaml        # Suricata engine and network interface configuration
├── scripts/
│   └── firewall_block.sh    # Dynamic iptables mitigation script
├── logs/
│   ├── sample_fast.log      # Raw text alert entries
│   └── sample_eve.json      # Structured JSON telemetry
└── README.md                # Project documentation and syntax reference
💻 Linux Command Syntax Reference
1. Interface & Engine Commands
Bash
# Display active network interfaces and assigned IP addresses
ip -br addr show

# Validate Suricata configuration file syntax
sudo suricata -T -c /etc/suricata/suricata.yaml -v

# Launch Suricata in live capture mode on interface enp0s3
sudo suricata -c /etc/suricata/suricata.yaml -i enp0s3
2. Custom Rule Syntax (/etc/suricata/rules/local.rules)
Plaintext
# Rule 1: Detect ICMP Echo Requests (Ping Probes)
alert icmp any any -> $HOME_NET any (msg:"[NIDS Alert] ICMP Ping Sweep Detected"; itype:8; sid:1000001; rev:1;)

# Rule 2: Detect Inbound TCP Port 80 Probing
alert tcp any any -> $HOME_NET 80 (msg:"[NIDS Alert] Suspicious Inbound HTTP Request"; flow:to_server,established; sid:1000002; rev:1;)

# Rule 3: Detect Outbound Package Management & System Requests
alert ip any any -> any any (msg:"[NIDS Info] Outbound Traffic Linked to Package Management"; sid:1000003; rev:1;)
Bash
# Update and reload Suricata rule definitions
sudo suricata-update
3. Log Inspection & Stream Processing Syntax
Bash
# Follow real-time raw alert feed
sudo tail -f /var/log/suricata/fast.log

# Stream and filter JSON alert objects with jq
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'

# Extract specific telemetry fields (timestamp, alert message, source/dest IP)
sudo tail -f /var/log/suricata/eve.json | jq -r '[.timestamp, .alert.signature, .src_ip, .dest_ip] | @tsv'
4. Active Mitigation Syntax (iptables)
Bash
# Drop all incoming packets from a malicious host (e.g., 10.0.2.2)
sudo iptables -A INPUT -s 10.0.2.2 -j DROP

# View active firewall rules with packet and byte counters
sudo iptables -L INPUT -v -n --line-numbers

# Delete the drop rule (unblock host)
sudo iptables -D INPUT -s 10.0.2.2 -j DROP
5. Visualization Setup Syntax (Docker & EveBox)
Bash
# Install Docker
sudo snap install docker

# Deploy EveBox web UI connected to Suricata JSON logs
sudo docker run -d \
  --name evebox \
  -p 5636:5636 \
  -v /var/log/suricata/eve.json:/var/log/suricata/eve.json:ro \
  jasonish/evebox:latest -e /var/log/suricata/eve.json

# Check container status
sudo docker ps
Access the dashboard at: http://localhost:5636
