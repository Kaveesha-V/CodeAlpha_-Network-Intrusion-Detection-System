# 1. Create the README.md file
cat << 'EOF' > README.md
# Network Intrusion Detection System (NIDS) using Suricata

An end-to-end implementation of a Network Intrusion Detection and Prevention environment configured on Ubuntu Linux. This project demonstrates real-time packet inspection, custom rule detection, structured alert logging, and automated threat mitigation using iptables.

## 📌 Features
- **Traffic Inspection:** Live packet capture and protocol parsing with Suricata.
- **Custom Alert Signatures:** Tailored detection rules for ping scans, port scans, and unauthorized HTTP requests.
- **Telemetry Parsing:** Real-time log monitoring using `fast.log` and structured queries on `eve.json` via `jq`.
- **Active Response / IPS:** Firewall enforcement using `iptables` drop rules to mitigate detected threats.
- **Visualization:** Containerized dashboard deployment via Docker for event tracking.

---

## 🛠️ Prerequisites & Tools
- **OS:** Ubuntu 24.04 LTS
- **NIDS Engine:** Suricata
- **Firewall:** iptables
- **Log Parsing:** jq
- **Containers:** Docker

---

## 🚀 Setup & Execution

### 1. Start Suricata
```bash
sudo suricata -c /etc/suricata/suricata.yaml -i <interface_name>
