📘 Full project report: [docs/ReconX_Project_Report.docx](docs/ReconX_Project_Report.docx)

# 🛰️ ReconX — Custom Reconnaissance Tool

<p align="left">
  <img src="https://img.shields.io/badge/python-3.6%2B-blue" alt="Python Version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
  <img src="https://img.shields.io/badge/platform-Linux%20%7C%20Kali-lightgrey" alt="Platform">
  <img src="https://img.shields.io/badge/status-active-success" alt="Status">
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen" alt="PRs Welcome">
</p>

**ReconX** is a lightweight, modular reconnaissance toolkit built during the **Reverse 4K Summer Training Camp** cybersecurity internship. It automates the information-gathering phase of penetration testing by combining **passive** and **active** recon modules behind a simple, unified command-line interface.

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Objectives](#-objectives)
- [Scope & Limitations](#-scope--limitations)
- [Features](#-features)
- [Project Structure](#-project-structure)
- [Architecture](#-architecture)
- [Technology Stack](#-technology-stack)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Usage](#-usage)
- [Module Details](#-module-details)
- [Reporting](#-reporting)
- [Sample Output](#-sample-output)
- [Testing & Performance](#-testing--performance)
- [Troubleshooting / FAQ](#-troubleshooting--faq)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [Team & Credits](#-team--credits)
- [Acknowledgments](#-acknowledgments)
- [GitHub Repositories](#-github-repositories)
- [Contact & Support](#-contact--support)
- [Disclaimer](#-disclaimer)
- [License](#-license)

---

## 🧭 Overview

ReconX streamlines the reconnaissance phase of a penetration test by automating common OSINT and network-scanning techniques. It pulls domain, DNS, and subdomain intelligence passively, then performs active checks such as port scanning, banner grabbing, and technology fingerprinting — outputting everything into structured, timestamped reports.

## 🎯 Objectives

- Build a command-line interface (CLI) reconnaissance tool
- Automate key recon techniques: WHOIS, DNS queries, subdomain enumeration, port scanning, and banner grabbing
- Implement a modular structure for scalability and easy debugging
- Provide clean, structured reports for penetration testers
- Maintain ethical boundaries through non-intrusive, passive-first scanning

## 🔒 Scope & Limitations

ReconX focuses purely on **reconnaissance** — it does not include vulnerability exploitation or payload delivery. It operates over the internet and may be affected by rate-limiting on third-party APIs or restricted network access. All modules rely on legal, publicly available data sources and are intended for use **only on authorized targets**.

---

## 🧠 Features

### 🔍 Passive Recon
| Module | Description |
|---|---|
| WHOIS Lookup | Retrieves domain registration, registrar, and expiry data via the `whois` library |
| DNS Enumeration | Resolves A, MX, TXT, and NS records using `dnspython` |
| Subdomain Enumeration | Queries crt.sh certificate transparency logs to passively discover subdomains (no brute-forcing) |

### 🔧 Active Recon
| Module | Description |
|---|---|
| Port Scanning | Raw TCP socket scan across common ports to detect open services |
| Banner Grabbing | Connects to open ports and reads service banners to identify running software/versions |
| Technology Detection | Sends an HTTP GET request and inspects response headers (`Server`, `X-Powered-By`) to fingerprint the web stack |

### 📄 Reporting
- Aggregates results from all modules into a single, timestamped `.txt` report
- Output saved to the `/report/` directory with clearly divided sections
- Designed for easy extension into `.html` format

### ⚙️ Modularity & CLI Control
- Every module runs independently and is callable via CLI flags
- `cli.py` uses Python's `argparse` with subparsers for `active` and `passive` modes
- `main.py` acts as a controller that runs all modules sequentially with graceful error handling

---

## 📁 Project Structure

```
ReconX/
├── main.py                  # Controller — runs all active + passive modules sequentially
├── cli.py                   # CLI interface — run specific modules via flags
├── active/
│   ├── port_scan.py         # TCP port scanner
│   ├── banner_grab.py       # Service banner grabber
│   └── tech_detect.py       # Web technology fingerprinting
├── passive/
│   ├── whois_lookup.py      # WHOIS lookup module
│   ├── dns_enum.py          # DNS record enumeration (A, MX, TXT, NS)
│   └── subdomain_enum.py    # Subdomain discovery via crt.sh
├── report/                  # Timestamped output reports
├── requirements.txt         # Dependency list
├── .gitignore                # Excludes venv, __pycache__, and report output
├── LICENSE                  # MIT License
└── README.md                # Project documentation
```

---

## 🏗️ Architecture

```
                    User
                     │
                     ▼
              cli.py (--mode, --tool)
                     │
      ┌──────────────┼──────────────┐
      ▼                             ▼
  mode = active                mode = passive
      │                             │
  ┌───┼────────┐             ┌──────┼────────┐
  ▼   ▼        ▼             ▼      ▼         ▼
portscan  banner  tech    whois    dns    subdomain
  │        │       │        │       │         │
  └────────┴───────┴────────┴───────┴─────────┘
                     │
                     ▼
            report/ (timestamped .txt)
```

---

## 🛠️ Technology Stack

- **Language:** Python 3
- **Libraries:** `requests`, `socket`, `dnspython`, `argparse`, `whois`
- **External APIs:** [crt.sh](https://crt.sh) (certificate transparency logs) for subdomain enumeration
- **Environment:** Linux (Kali), CLI-based

---

## ✅ Prerequisites

- Python **3.6+**
- `pip` package manager
- Internet access (for WHOIS, DNS, crt.sh, and HTTP-based modules)
- Recommended OS: Linux / Kali (Windows and macOS also supported with Python 3 installed)
- No API keys required — crt.sh is queried anonymously and has no authentication step, but is subject to informal rate-limiting under heavy use

**`requirements.txt`**
```
requests
dnspython
python-whois
```

---

## 🚀 Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/mahadzulfiqar/custom-recon-tool.git
cd custom-recon-tool
pip install -r requirements.txt
```

> Recommended: use a virtual environment to keep dependencies isolated.

```bash
python3 -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

---

## 🚀 Usage

### Run everything at once

```bash
python3 main.py
```
This prompts for a target (domain or IP) and runs all active and passive modules sequentially, printing results for each.

### Run specific modules via the CLI

The CLI is built with `argparse` subparsers — choose a mode (`active` / `passive`) and a tool.

**Active Recon**
```bash
python3 cli.py active --tool portscan
python3 cli.py active --tool banner
python3 cli.py active --tool tech
```

**Passive Recon**
```bash
python3 cli.py passive --tool whois
python3 cli.py passive --tool dns
python3 cli.py passive --tool subdomain
```

### Run individual scripts directly

```bash
python3 passive/whois_lookup.py
python3 passive/dns_enum.py
python3 passive/subdomain_enum.py
python3 active/port_scan.py
python3 active/banner_grab.py
python3 active/tech_detect.py
```
Each script will interactively prompt for the target domain, host, or IP/port.

### Example Commands
```bash
python3 cli.py passive --tool whois
python3 cli.py passive --tool dns
python3 cli.py active --tool portscan
```

---

## 🔍 Module Details

### Subdomain Enumeration
Queries crt.sh (`https://crt.sh/?q=%25.{domain}&output=json`) for certificate transparency records, parses the `name_value` field, and prints/writes a de-duplicated, sorted list of subdomains. Purely passive — no DNS brute-forcing involved.

### WHOIS Lookup
Uses `whois.whois(domain)` to return registrar, creation/expiry dates, name servers, and contact details in a structured format.

### DNS Enumeration
Iterates over `A`, `MX`, `TXT`, and `NS` record types using `dns.resolver.resolve()`, handling `NoAnswer` and `NXDOMAIN` exceptions gracefully.

### Port Scanning
Attempts a TCP connection (`socket.connect_ex()`) against a configurable list of ports (default: `21, 22, 80, 443, 8080`) with a 1-second timeout, reporting which ports respond as open.

### Banner Grabbing
Opens a socket to a given IP/port, reads up to 1024 bytes from the initial response, and decodes/prints the service banner — useful for identifying software and version info.

### Technology Detection
Sends an HTTP GET request to the target and inspects the `Server` and `X-Powered-By` response headers to identify the underlying web technology stack.

---

## 📄 Reporting

Each module's results are written to a timestamped `.txt` file inside `/report/`, with clearly labeled sections (e.g. `WHOIS Lookup`, `DNS Enumeration`, `Port Scan`) for easy readability and downstream analysis.

```python
whois_result = perform_whois(target)
write_report("WHOIS Lookup", whois_result, target)
```

---

## 🖥️ Sample Output

```
=== Custom Recon Tool ===
Enter the target (domain or IP): example.com

--- PASSIVE RECON ---
[WHOIS Lookup for: example.com]
Registrar: RESERVED-Internet Assigned Numbers Authority
Creation Date: 1995-08-14
Expiry Date: 2026-08-13

[DNS Enumeration for: example.com]
A Records:
 - 93.184.216.34
MX Records: No Answer
NS Records:
 - a.iana-servers.net.
 - b.iana-servers.net.

[Subdomain Enumeration using crt.sh for: example.com]
[+] Found 3 unique subdomains:
 - www.example.com
 - dev.example.com
 - mail.example.com

--- ACTIVE RECON ---
Scanning ports on example.com
[+] Port 80 is OPEN
[+] Port 443 is OPEN

[+] Banner from 93.184.216.34:80 - Apache/2.4.49 (Ubuntu)

Report saved to: report/example_com_20250721.txt
```

---

## 🧪 Testing & Performance

### Test Strategy
Each module was tested individually against sample domains (e.g. `example.com`) as well as live domains with known open ports, covering both offline and online conditions.

### Test Cases
- WHOIS returns a valid domain creation date
- DNS enumeration lists A, MX, and NS records correctly
- Port scan detects standard open ports (22, 80, 443)
- Banner grab correctly identifies running services (e.g. Apache, nginx)

### Performance Metrics
| Module | Approx. Time |
|---|---|
| WHOIS Lookup | < 1s |
| DNS Records | ~0.5s |
| Subdomain Scan | 2–3s |
| Port Scan (top ports) | 3–5s |
| Banner Grab | 1–2s |
| Memory Usage | < 100MB |

---

## 🛠️ Troubleshooting / FAQ

**Q: DNS module throws `NXDOMAIN` for a domain I know exists.**
A: Confirm there's no typo and that the domain resolves via `nslookup`/`dig` outside the tool. Some registrars use non-standard NS delegation that can briefly fail on first query — try again after a few seconds.

**Q: Subdomain enumeration returns nothing or times out.**
A: crt.sh occasionally rate-limits or is briefly slow to respond under heavy public load. Wait a moment and retry, or reduce request frequency if scanning multiple domains in a loop.

**Q: Port scan shows no open ports on a target I expect to be reachable.**
A: Check that the target isn't blocking ICMP/TCP probes via a firewall, and that you're not scanning from a network that blocks outbound connections on those ports.

**Q: `whois` module raises an error or returns empty data.**
A: Some TLDs (e.g. certain ccTLDs) restrict WHOIS data or require a different WHOIS server. This is a known limitation of the underlying `whois` library, not the tool itself.

**Q: `ModuleNotFoundError` when running a script.**
A: Make sure you've activated your virtual environment and run `pip install -r requirements.txt` before executing any module.

---

## 🔮 Roadmap

- [ ] Multithreading for faster scans
- [ ] Export reports in PDF/HTML formats
- [ ] GUI version for non-technical users
- [ ] Integration with vulnerability databases
- [ ] Dockerized deployment for easy portability
- [ ] Configurable port ranges and scan profiles
- [ ] JSON output option for tool-chaining

---

## 🤝 Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes with clear messages
4. Open a pull request describing what you changed and why

For larger changes, please open an issue first to discuss what you'd like to add or modify.

---

## 👥 Team & Credits

| Name | Role |
|---|---|
| **Mahad Zulfiqar** | Active Recon |
| **Tooba Zainab** | Passive Recon |
| **Minahil Nadeem** | Reporting & CLI |
| **Abdullah Nasir** | Mentorship, Review & Feedback |

*Developed as part of the Reverse 4K Summer Training Camp cybersecurity internship.*

---

## 🙏 Acknowledgments

- [crt.sh](https://crt.sh) — certificate transparency log search
- [dnspython](https://www.dnspython.org/) — DNS toolkit for Python
- [python-whois](https://pypi.org/project/python-whois/) — WHOIS lookup library
- [OWASP Recon Guide](https://owasp.org/) — methodology reference
- [Sublist3r](https://github.com/aboul3la/Sublist3r) — inspiration for subdomain enumeration approach

---

## 🔗 GitHub Repositories

- https://github.com/mahadzulfiqar/custom-recon-tool
- https://github.com/mahadzulfiqar/Active-Recon-Module
- https://github.com/mahadzulfiqar/Passive-Recon-Module

---

## 📬 Contact & Support

For bugs, questions, or feature requests, please open an issue on the [GitHub repository](https://github.com/mahadzulfiqar/custom-recon-tool/issues). For anything else, reach out to the team via the repository's discussions tab.

---

## ⚠️ Disclaimer

ReconX is intended **strictly for educational purposes and authorized security testing**. Running this tool against systems or domains without **explicit, written permission** from the owner is illegal and unethical.

The authors and contributors accept no liability for misuse or damage caused by this tool. Always operate within the scope of a signed engagement or authorization agreement.

---

## 📜 License

This project is released under the [MIT License](LICENSE) — feel free to use, modify, and distribute with attribution.
