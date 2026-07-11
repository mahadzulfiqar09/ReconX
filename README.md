🛰️ ReconX — Custom Reconnaissance Tool

ReconX is a lightweight, modular reconnaissance toolkit built during the Reverse 4K Summer Training Camp cybersecurity internship. It automates the information-gathering phase of penetration testing by combining passive and active recon modules behind a simple, unified command-line interface.


📌 Table of Contents


Overview
Objectives
Scope & Limitations
Features
Project Structure
Technology Stack
Installation
Usage
Module Details
Reporting
Testing & Performance
Future Enhancements
Team & Credits
GitHub Repositories
Disclaimer
License



🧭 Overview

ReconX streamlines the reconnaissance phase of a penetration test by automating common OSINT and network-scanning techniques. It pulls domain, DNS, and subdomain intelligence passively, then performs active checks such as port scanning, banner grabbing, and technology fingerprinting — outputting everything into structured, timestamped reports.

🎯 Objectives


Build a command-line interface (CLI) reconnaissance tool
Automate key recon techniques: WHOIS, DNS queries, subdomain enumeration, port scanning, and banner grabbing
Implement a modular structure for scalability and easy debugging
Provide clean, structured reports for penetration testers
Maintain ethical boundaries through non-intrusive, passive-first scanning


🔒 Scope & Limitations

ReconX focuses purely on reconnaissance — it does not include vulnerability exploitation or payload delivery. It operates over the internet and may be affected by rate-limiting on third-party APIs or restricted network access. All modules rely on legal, publicly available data sources and are intended for use only on authorized targets.


🧠 Features

🔍 Passive Recon

ModuleDescriptionWHOIS LookupRetrieves domain registration, registrar, and expiry data via the whois libraryDNS EnumerationResolves A, MX, TXT, and NS records using dnspythonSubdomain EnumerationQueries crt.sh certificate transparency logs to passively discover subdomains (no brute-forcing)

🔧 Active Recon

ModuleDescriptionPort ScanningRaw TCP socket scan across common ports to detect open servicesBanner GrabbingConnects to open ports and reads service banners to identify running software/versionsTechnology DetectionSends an HTTP GET request and inspects response headers (Server, X-Powered-By) to fingerprint the web stack

📄 Reporting


Aggregates results from all modules into a single, timestamped .txt report
Output saved to the /report/ directory with clearly divided sections
Designed for easy extension into .html format


⚙️ Modularity & CLI Control


Every module runs independently and is callable via CLI flags
cli.py uses Python's argparse with subparsers for active and passive modes
main.py acts as a controller that runs all modules sequentially with graceful error handling



📁 Project Structure

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
└── README.md                # Project documentation


🛠️ Technology Stack


Language: Python 3
Libraries: requests, socket, dnspython, argparse, whois
External APIs: crt.sh (certificate transparency logs) for subdomain enumeration
Environment: Linux (Kali), CLI-based



🚀 Installation

Clone the repository and install dependencies:

bashgit clone https://github.com/mahadzulfiqar/custom-recon-tool.git
cd custom-recon-tool
pip install -r requirements.txt


Recommended: use a virtual environment to keep dependencies isolated.



bashpython3 -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
pip install -r requirements.txt


🚀 Usage

Run everything at once

bashpython3 main.py

This prompts for a target (domain or IP) and runs all active and passive modules sequentially, printing results for each.

Run specific modules via the CLI

The CLI is built with argparse subparsers — choose a mode (active / passive) and a tool.

Active Recon

bashpython3 cli.py active --tool portscan
python3 cli.py active --tool banner
python3 cli.py active --tool tech

Passive Recon

bashpython3 cli.py passive --tool whois
python3 cli.py passive --tool dns
python3 cli.py passive --tool subdomain

Run individual scripts directly

bashpython3 passive/whois_lookup.py
python3 passive/dns_enum.py
python3 passive/subdomain_enum.py
python3 active/port_scan.py
python3 active/banner_grab.py
python3 active/tech_detect.py

Each script will interactively prompt for the target domain, host, or IP/port.

Example Commands

bashpython3 cli.py passive --tool whois
python3 cli.py passive --tool dns
python3 cli.py active --tool portscan


🔍 Module Details

Subdomain Enumeration

Queries crt.sh (https://crt.sh/?q=%25.{domain}&output=json) for certificate transparency records, parses the name_value field, and prints/writes a de-duplicated, sorted list of subdomains. Purely passive — no DNS brute-forcing involved.

WHOIS Lookup

Uses whois.whois(domain) to return registrar, creation/expiry dates, name servers, and contact details in a structured format.

DNS Enumeration

Iterates over A, MX, TXT, and NS record types using dns.resolver.resolve(), handling NoAnswer and NXDOMAIN exceptions gracefully.

Port Scanning

Attempts a TCP connection (socket.connect_ex()) against a configurable list of ports (default: 21, 22, 80, 443, 8080) with a 1-second timeout, reporting which ports respond as open.

Banner Grabbing

Opens a socket to a given IP/port, reads up to 1024 bytes from the initial response, and decodes/prints the service banner — useful for identifying software and version info.

Technology Detection

Sends an HTTP GET request to the target and inspects the Server and X-Powered-By response headers to identify the underlying web technology stack.


📄 Reporting

Each module's results are written to a timestamped .txt file inside /report/, with clearly labeled sections (e.g. WHOIS Lookup, DNS Enumeration, Port Scan) for easy readability and downstream analysis.

pythonwhois_result = perform_whois(target)
write_report("WHOIS Lookup", whois_result, target)


🧪 Testing & Performance

Test Strategy

Each module was tested individually against sample domains (e.g. example.com) as well as live domains with known open ports, covering both offline and online conditions.

Test Cases


WHOIS returns a valid domain creation date
DNS enumeration lists A, MX, and NS records correctly
Port scan detects standard open ports (22, 80, 443)
Banner grab correctly identifies running services (e.g. Apache, nginx)


Performance Metrics

ModuleApprox. TimeWHOIS Lookup< 1sDNS Records~0.5sSubdomain Scan2–3sPort Scan (top ports)3–5sBanner Grab1–2sMemory Usage< 100MB


🔮 Future Enhancements


Multithreading for faster scans
Export reports in PDF/HTML formats
GUI version for non-technical users
Integration with vulnerability databases
Dockerized deployment for easy portability



👥 Team & Credits

NameRoleMahad ZulfiqarActive ReconTooba ZainabPassive ReconMinahil NadeemReporting & CLIAbdullah NasirMentorship, Review & Feedback

Developed as part of the Reverse 4K Summer Training Camp cybersecurity internship.


🔗 GitHub Repositories


https://github.com/mahadzulfiqar/custom-recon-tool
https://github.com/mahadzulfiqar/Active-Recon-Module
https://github.com/mahadzulfiqar/Passive-Recon-Module



⚠️ Disclaimer

ReconX is intended strictly for educational purposes and authorized security testing. Running this tool against systems or domains without explicit, written permission from the owner is illegal and unethical.

The authors and contributors accept no liability for misuse or damage caused by this tool. Always operate within the scope of a signed engagement or authorization agreement.


📜 License

This project is released under the MIT License — feel free to use, modify, and distribute with attribution.
