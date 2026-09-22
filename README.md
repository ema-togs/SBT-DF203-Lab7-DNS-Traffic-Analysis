# SBT-DF203-Lab7: DNS Traffic Analysis

## Overview
This repository contains the forensic artifacts, packet captures, and analytical reports for **Lab 7: DNS Traffic Analysis**. The primary objective of this investigation is to capture, parse, and correlate DNS network traffic metadata across baseline utilities, browser sessions, and protocol-dependent services (SMTP).

## Investigation Findings Summary

| Field | Finding |
| :--- | :--- |
| **Configured Resolver IP** | `1.1.1.1` |
| **Client Source Port** | Dynamic UDP Ephemeral Port (e.g., `51234`) |
| **Resolver Destination Port** | `53` (UDP) |
| **Transaction ID** | Hexadecimal 16-bit identifier (Query/Response matched) |
| **Query Name & Type** | `httpforever.com` (Type `1` / `A` Record) |
| **Response Code** | `0` (`NOERROR`) |
| **Answer IP(s)** | `172.67.132.115`, `104.21.4.210` |
| **TTL** | `300` seconds |
| **Query-Response Delta** | `33.02 ms` |
| **Subsequent Connection Correlation** | No TCP SYN frames captured due to kernel-level `-f 'port 53'` capture filter |
| **Normal Variations Observed** | Multi-IP resolution via Cloudflare CDN load balancing; clean single `A` query execution |

## Repository Structure


SBT-DF203-Lab7/
├── evidence/        # Baseline and browser pcapng packet captures
├── exported/        # Extracted objects and reconstructed forensic data
├── reports/         # Processed TSV inventories and correlation text logs
├── screenshots/     # Visual proof of lab execution and terminal output
├── scripts/         # Command-line workflows and Tshark extraction queries
└── working/         # Temporary analysis workspace files


## Forensic Conclusion
The forensic analysis of our DNS traffic packet captures demonstrates the 
complete operational lifecycle and behavioral metadata of domain resolution 
across both baseline CLI tools and modern web browsers. By inspecting the 
pcapng files using `tshark`, I verified that standard DNS resolutions 
complete a rapid query-response exchange with matching transaction IDs, 
while subsequent connection correlation highlights how specific capture filters, 
such as restricting collection strictly to port 53, omit lower-layer 
TCP handshakes at the kernel level despite valid IP answers being returned 
by Cloudflare's CDN. Comparing these evidence files against application-level 
captures from mail services confirms how host system mechanisms rely 
on pre-connection `A` record lookups to locate target endpoints before initiating 
protocol-specific sessions like SMTP. To ensure comprehensive forensic visibility 
and evidence integrity, I resolved local filesystem permission barriers, properly 
managed capture duration windows, and verified that query inventories accurately 
reflected live network activity without background interference. 
This investigation proves that you cannot assume an empty packet capture or 
missing connection payload indicates a network failure; you must evaluate capture 
filter scopes, local caching, and resolver behaviors to accurately interpret wire-level 
telemetry. Finally, because this evidence relies strictly on captured network packets, 
it provides an exact account of DNS transactions on the wire during testing but does not 
record offline operating system cache state, local hosts file overrides, or internal application 
logs outside the monitored network interfaces.
