#🔵 Blue Eye — SOC Monitor

**Blue Eye** is a real-world, production-ready Security Operations Center (SOC) tool built in Python. It captures live network traffic, detects threats using rule-based and machine learning techniques, sends Telegram alerts, generates professional PDF reports, and exposes a dark-themed web dashboard — all in a single file.

---

## 🚀 Features

| Feature | Details |
|---|---|
| **Live Packet Capture** | Captures traffic using Scapy; supports BPF filters, interface selection, PCAP replay |
| **20+ Threat Detectors** | Port scan, DDoS, ARP spoofing, DNS tunneling, brute force, data exfiltration, ransomware, crypto mining, lateral movement, Tor/VPN, rogue DHCP, WiFi deauth, HTTP injection, SSH tunneling |
| **ML Anomaly Detection** | IsolationForest + OneClassSVM ensemble; auto-trains on live traffic |
| **UEBA** | User & Entity Behavior Analytics — per-IP behavioral baseline, sigma-based anomaly scoring |
| **Kill Chain Correlation** | Correlates multi-stage attacks into incidents; detects APT campaigns |
| **Threat Intelligence** | AbuseIPDB + VirusTotal API integration; Tor exit node list auto-sync |
| **MITRE ATT&CK Mapping** | Every alert tagged with Tactic ID, Technique ID, name |
| **Telegram Alerts** | Real-time alerts for CRITICAL/HIGH threats; WHO/WHAT/WHEN/WHERE/HOW/EVIDENCE format |
| **PDF Report** | Professional 10-section report: executive summary, charts, timeline, MITRE map, IR playbooks, attacker profiles, kill chain incidents |
| **Web Dashboard** | Flask dark-UI; login protected; live stats, threat table, IP block/unblock, hunt triggers |
| **Auto-Blocking** | Optional iptables-based IP blocking with configurable duration |
| **Geo-Blocking** | Block traffic from specific countries |
| **False Positive Guard** | Whitelist/CDN suppression; per-IP per-threat cooldown |
| **SQLite Database** | Persistent storage for packets, threats, incidents, blocked IPs |

---

## 📋 Requirements

- **Python 3.9+**
- **Linux recommended** (for live capture and auto-blocking)
- **Root / Administrator** required for live packet capture

### Install Dependencies

```bash
pip install -r requirements.txt
```

Or manually:

```bash
pip install flask scapy rich scikit-learn matplotlib pandas numpy requests fpdf2
```

---

## ⚙️ Configuration (Environment Variables)

Blue Eye reads sensitive config from environment variables — never hardcode secrets.

### Set via shell:

```bash
export TELEGRAM_ENABLED=true
export TELEGRAM_BOT_TOKEN=your_token_here
export TELEGRAM_CHAT_ID=your_chat_id
export ABUSEIPDB_API_KEY=your_key
export BLUEEYE_USERNAME=admin
export BLUEEYE_PASSWORD=your_secure_password
```

Or create a `.env` file and load it:

```bash
set -a && source .env && set +a
python blueeye.py
```

---

## 🖥️ Usage

### Start Live Capture + Dashboard

```bash
sudo python blueeye.py
```

### Capture on Specific Interface for 10 Minutes

```bash
sudo python blueeye.py --iface eth0 --time 600
```

### Replay a PCAP File

```bash
python blueeye.py --pcap capture.pcap
```

### Generate PDF Report from Existing Database

```bash
python blueeye.py --report-only
```

### Dashboard Only (No Capture)

```bash
python blueeye.py --dashboard-only --port 8080
```

### Enable Auto-Blocking + Geo-Blocking

```bash
sudo python blueeye.py --block-on --geoblock --allowed-countries India
```

### All Options

```
--iface IFACE          Network interface to capture on
--time SECS            Capture duration in seconds (0 = forever)
--filter FILTER        BPF capture filter expression
--filter-ip IP         Only process packets from/to this IP
--filter-proto PROTO   Only process this protocol
--pcap FILE            Replay a PCAP file instead of live capture
--report-only          Generate PDF from existing DB and exit
--dashboard-only       Start web dashboard only (no capture)
--no-web               Disable web dashboard
--port PORT            Dashboard port (default: 5000)
--host HOST            Dashboard bind address (default: 0.0.0.0)
--block-on             Enable auto-blocking of threats
--block-dur SECS       Auto-block duration (default: 3600)
--geoblock             Enable geo-blocking
--allowed-countries    Allowed countries for geo-blocking
--ddos-thresh N        DDoS packets/sec threshold
--scan-thresh N        Port scan unique ports threshold
--brute-thresh N       Brute force attempts threshold
--sensitivity FLOAT    Adaptive sensitivity sigma (default: 2.5)
--terminal-dash        Show rich terminal dashboard
--debug                Enable debug logging
```

---

## 🌐 Web Dashboard

Access at: `http://localhost:5000`

Default login: xxxxxx / xxxxxxx (change via env vars)

**Dashboard pages:**
- Live threat feed with severity coloring
- Top attacker IPs
- Block / unblock IPs manually
- Trigger threat hunts (beaconing, DGA, password spray)
- Session and topology views
- UEBA anomaly profiles

---

## 📁 Output Files

All output goes to the `output/` directory:

| File | Description |
|---|---|
| `output/blueeye.db` | SQLite database (packets, threats, incidents, blocked IPs) |
| `output/BlueEye_SOC_Report.pdf` | Auto-generated PDF report |
| `output/blueeye_capture.pcap` | Raw packet capture |
| `output/blueeye.log` | Application log |

---

## 📊 PDF Report Sections

1. Executive Summary
2. Visual Analysis (charts: protocol distribution, threat timeline, top IPs)
3. Threat Timeline — Chronological Log
4. Detailed Threat Analysis (per-event cards)
5. Top Attacker IP Profiles
6. MITRE ATT&CK Coverage Map
7. Incident Response Playbooks
8. Blocked IP Addresses
9. Correlated Incidents (Kill Chain Analysis)
10. Recommendations & Next Steps

---

## 🔒 Security Notes

- **Never commit** your `.env` file or API keys to Git
- Add `.env` and `output/` to `.gitignore`
- Change default dashboard credentials before deploying
- Run behind a firewall; do not expose port 5000 to the internet without authentication

---

## 📦 Project Structure

```
blue-eye/
├── blueeye.py          # Main application (single-file)
├── requirements.txt    # Python dependencies
├── README.md           # This file
├── INTERVIEW_GUIDE.md  # Technical guide for interviews
├── .env.example        # Example environment variables
├── .gitignore          # Git ignore rules
└── output/             # Auto-created at runtime
    ├── blueeye.db
    ├── BlueEye_SOC_Report.pdf
    ├── blueeye_capture.pcap
    └── blueeye.log
```

---

## 🛡️ Detected Threat Types & MITRE Mapping

| Threat | MITRE Tactic | Technique |
|---|---|---|
| Port Scan / SYN Stealth Scan | Discovery | T1046 |
| DDoS / Flood Attack | Impact | T1499 |
| ICMP Flood | Impact | T1499 |
| ARP Spoofing / Poisoning | Credential Access | T1557 |
| DNS Tunneling | C2 / Exfiltration | T1071.004 / T1048 |
| Brute Force Attack | Credential Access | T1110 |
| Lateral Movement | Lateral Movement | T1021 |
| Data Exfiltration | Exfiltration | T1041 |
| Crypto Mining | Impact | T1496 |
| Tor/VPN/Proxy | C2 | T1090 |
| SSH Tunneling | C2 | T1572 |
| Ransomware Behavior | Impact | T1486 |
| HTTP Injection | Initial Access | T1190 |
| Rogue DHCP Server | Credential Access | T1557 |
| WiFi Deauth Attack | Impact | T1499 |
| DGA Domain Detected | C2 | T1568.002 |
| C2 Beaconing | C2 | T1071 |
| Password Spray | Credential Access | T1110.003 |
| APT Campaign | Reconnaissance | T1595 |
| ML / UEBA Anomaly | Reconnaissance | T1595 |

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

*Built with Python, Scapy, Flask, scikit-learn, fpdf2, and Rich.*
