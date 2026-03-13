---
title: ReconHunter – Automated Reconnaissance Framework
date: 2026-03-12 00:00:00 +0530
category: [Projects]
tags: [reconnaissance, bug-bounty, automation, cybersecurity, bash]
---

## Overview

![ReconHunter Report](/assets/img/reconhunter/reconhunter_v_1_3_2.png)

**ReconHunter** is an automated reconnaissance framework designed to streamline the early stages of web security testing and bug bounty reconnaissance. The tool orchestrates several well-known security utilities into a single automated pipeline that performs attack surface discovery and vulnerability scanning.

The goal of ReconHunter is to reduce the manual effort required during reconnaissance and provide structured outputs that help security researchers quickly analyze targets.

---

## Motivation

During bug bounty reconnaissance, researchers typically run multiple tools separately to enumerate assets, discover open ports, identify technologies, and scan for vulnerabilities. This manual workflow can be time-consuming and repetitive.

ReconHunter was built to automate this workflow by chaining multiple reconnaissance tools together and producing organized reports that make analysis easier.

---

## Features

* Automated **subdomain enumeration**
* **Live host detection**
* **Port scanning and service discovery**
* **Technology detection**
* **Directory brute forcing**
* **Parameter discovery**
* **Automated vulnerability scanning**
* **Screenshot capture of discovered hosts**
* **HTML report generation for visualization**

---

## Recon Pipeline

The tool follows a structured reconnaissance workflow:

1. Subdomain Enumeration
2. Live Host Detection
3. Port Scanning
4. Service Detection
5. Technology Fingerprinting
6. Screenshot Capture
7. URL Collection
8. Parameter Discovery
9. Directory Bruteforcing
10. Vulnerability Scanning
11. Report Generation

---

## Tools Used

ReconHunter integrates several popular reconnaissance tools:

* Subfinder
* Assetfinder
* Amass
* Naabu
* Nmap
* HTTPX
* WhatWeb
* Gowitness
* FFUF
* Arjun
* Nuclei
* GAU
* Waybackurls

These tools work together to provide a complete view of the target's attack surface.

---

## Installation

Clone the repository:

```
git clone https://github.com/yourusername/reconhunter.git
cd reconhunter
```

Run the installation script:

```
chmod +x install.sh
./install.sh
```

The script automatically installs required dependencies and tools.

---

## Usage

Run the tool against a target domain:

```
./reconhunter.sh example.com
```

ReconHunter will automatically execute the reconnaissance pipeline and store results inside the `reports` directory.

---

## Output Structure

Example output directory:

```
reports/
 ├── subdomains.txt
 ├── alive_http.txt
 ├── open_ports.txt
 ├── nmap_service_scan.txt
 ├── technologies.txt
 ├── arjun_params.txt
 ├── ffuf_results.txt
 ├── nuclei_results.txt
 ├── screenshots/
 └── report.html
```

The generated HTML report helps visualize reconnaissance results and quickly identify potential attack vectors.

---

## Example Screenshot

![ReconHunter Report](/assets/img/reconhunter/dashboard_1.png)
![ReconHunter Report](/assets/img/reconhunter/dashboard_2.png)
![ReconHunter Report](/assets/img/reconhunter/dashboard_3.png)

---

## Future Improvements

Planned enhancements for ReconHunter include:

* JavaScript endpoint extraction
* Secret detection in JS files
* Automated XSS parameter discovery
* Improved HTML reporting dashboard
* Parallelized scanning pipeline

---

## Conclusion

ReconHunter simplifies the reconnaissance phase of security testing by combining multiple tools into a single automated workflow. The framework enables security researchers to quickly discover attack surfaces and focus on vulnerability analysis instead of repetitive reconnaissance tasks.

---

## GitHub Repository

Source code available here:

https://github.com/lakshay-bhatnagar/ReconHunter

---
