# Day 01 — SOC L2 Apprenticeship
**Date:** [7th May 2026]
**Topic:** Networking Fundamentals — TCP/IP, OSI, Protocols & Attacks
**Category:** Theory + Lab
**Time Spent:** [Hours]

---

## SECTION 1: CORE CONCEPTS

### What Is a Network?
[A network is simply two or more devices that are connected to each other and can share information.]

### TCP/IP Model — 4 Layers
| Layer # | Layer Name | Protocols | My SOC Use |
|---------|-----------|-----------|------------|
| 4 | Application |HTTP, HTTPS, DNS, SMTP, FTP, SSH | Most attacks are delivered at this layer — phishing emails (SMTP), web-based attacks (HTTP), malware C2 communication (DNS, HTTPS). |
| 3 | Transport |TCP, UDP  | Port scanning, SYN floods, and service exploitation all happen at this layer. When an alert says "connection to Port 3389 from external IP" — that's a Transport Layer alert.|
| 2 | Internet |IP, ICMP | Every SIEM alert includes Source IP and Destination IP — both Internet Layer concepts. When you investigate who attacked your server, you start at this layer. IP-based attacks like IP spoofing and reconnaissance ping sweeps happen here.
| 1 | Network Access |Ethernet, Wi-Fi, MAC addresses | When an attacker physically connects a rogue device to your network — say, a Raspberry Pi hidden under a desk — that device will have a MAC address. If you're monitoring network access logs, you'll see a new, unknown MAC address appear. That's a red flag. MAC address changes (called MAC spoofing, where attackers fake their MAC to impersonate a trusted device) are also something you'll investigate.|

### OSI Model — 7 Layers
| Layer # | Layer Name | Protocol Example | Attack at This Layer |
|---------|-----------|-----------------|---------------------|
| 7 | Application | HTTP, DNS, SMTP, FTP, and SSH| Phishing emails use SMTP. Web application attacks (SQL injection, XSS) use HTTP. Malware C2 communication often uses HTTPS or DNS to blend in with normal traffic.|
| 6 | Presentation | SSL/TLS for security, XDR/NDR for data representation, and multimedia standards like JPEG, MPEG| SSL/TLS Striping (Forcing a browser to use HTTP instead of HTTPS.), Malware injection|
| 5 | Session | NetBIOS, RPC, PPTP, L2TP, PAP, and RTCP, which handle dialogue control| Session hijacking|
| 4 | Transport | TCP, UDP| Port scanning, SYN floods, connection-based attacks all happen here. Firewall rules are mostly written at this layer: "Block all inbound TCP connections to Port 3389 from external IPs|
| 3 | Network | IP, ICMP, ARP, RARP| IP-based attacks (IP spoofing, DDoS, ping sweeps, network scanning) all happen here. When you block an attacker's IP at the firewall, you're making a Layer 3 decision.|
| 2 | Data Link | Ethernet, Wi-Fi (802.11), ARP (Address Resolution Protocol).| ARP poisoning attacks, MAC spoofing, and rogue device detection all happen at this layer. Switches maintain a MAC address table — if you see an unknown MAC address appearing on a port, that's a Layer 2 security event.|
| 1 | Physical | Ethernet (IEEE 802.3), Wi-Fi (IEEE 802.11), Bluetooth| A rogue device physically plugged into your network is a Layer 1 concern. Someone tapping a physical cable is a Layer 1 attack. Electromagnetic interference causing network outages can be a physical layer issue or a deliberate attack.|

┌─────┬─────────────────┬────────────────────────────────────────────────┐
│  7  │   APPLICATION   │ User-facing protocols                          │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  6  │  PRESENTATION   │ Data formatting, encryption, compression       │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  5  │    SESSION      │ Managing communication sessions                │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  4  │   TRANSPORT     │ TCP/UDP, Port numbers, reliability             │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  3  │    NETWORK      │ IP addresses, routing                          │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  2  │   DATA LINK     │ MAC addresses, switches, ARP                   │
├─────┼─────────────────┼────────────────────────────────────────────────┤
│  1  │    PHYSICAL     │ Cables, Wi-Fi signals, bits                    │
└─────┴─────────────────┴────────────────────────────────────────────────┘

### IP Addresses
Private ranges I must memorise:
1. 10.0.0.0 to 10.255.255.255
2. 172.16.0.0 to 172.31.255.255
3. 192.168.0.0 to 192.168.255.255

Special IPs:
- 127.0.0.1 = → Loopback address. Your device talking to itself.Seeing this as source/destination in external traffic = suspicious.
- 8.8.8.8 = Means "all IP addresses on this device" or "any IP"  → Used in server configuration: "listen on 0.0.0.0" means listen on all interfaces
- 255.255.255.255 = → Broadcast address. Send to everyone on the local network.
                    → Normal in DHCP (devices broadcast to find a DHCP server)
                    → Abnormal if sent repeatedly or from an unexpected device.

If you see a non-private IP in a SIEM alert as the source, an external party is involved.
The NAT Concept (Why You Have Both)
Your home router has one public IP (assigned by your ISP). But your home might have 10 devices — phone, laptop, TV, tablet, etc. Each device gets a private IP. When any of your devices accesses the internet, the router performs NAT (Network Address Translation) — it replaces the private IP with the router's public IP before sending the packet out. When the response comes back, the router knows which internal device to send it to.
This is why all 10 devices in your home can access the internet using just one public IP address.
SOC Relevance: When investigating an outbound alert (internal device connecting to external server), the source IP will be a private IP (internal device). When investigating an inbound alert (external attacker connecting to your server), the source IP will be a public IP (the attacker). This immediately tells you the direction and nature of the threat.

### Critical Port Numbers
| Port | Protocol | Why SOC Cares |
|------|---------|---------------|
| 22 | SSH | |
| 53 | DNS | |
| 80 | HTTP | |
| 443 | HTTPS | |
| 3389 | RDP | |
| 4444 | Metasploit | |

### TCP 3-Way Handshake
Step 1: Client sends _______ 
Step 2: Server sends _______
Step 3: Client sends _______
Result: Connection ________

### TCP Flags and Their Attacks
| Flag | Legitimate Use | Attack Use |
|------|---------------|------------|
| SYN | | |
| ACK | | |
| FIN | | |
| RST | | |
| NULL | | |
| XMAS | | |

### Protocols Summary
| Protocol | Port | Transport | Attack SOC Watches For |
|---------|------|-----------|----------------------|
| HTTP | 80 | TCP | |
| HTTPS | 443 | TCP | |
| DNS | 53 | UDP | |
| ICMP | none | — | |
| ARP | none | — | |

---

## SECTION 2: LAB RESULTS

### Lab 1 — My Network Stack
- MAC Address: 
- Private IP: 192.168.18.42
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.18.1
- DNS Server: 

### Lab 2A — IP Classification Exercise
1. 10.20.30.40 → private
2. 8.8.8.8 → public
3. 192.168.0.1 → private
4. 172.25.0.100 → private
5. 185.220.101.45 → public
6. 127.0.0.1 → loopback

### Lab 2B — Ping Test Results
| Target | Reachable | Time |
|--------|-----------|------|
| google.com | yes |49 ms |
| 8.8.8.8 | yes |53 ms |
| 127.0.0.1 | yes |<1 ms |
| [my gateway] | yes |10 ms |

My analysis of what these results mean: [These results means that the google is reachable and DNS and internet working properly.]

### Lab 2C — Traceroute to Google
- Commands used: tracert for windows and traceroute for linux
- Total hops: 16
- First hop IP (my router): confidential
- Any timed out hops: no
- Observation: My router is using ipv6 and there is jump occur on hop 7 and 9.

Note: * * * on a line means that router blocks ICMP packets (it doesn't respond to pings). This is normal for many routers — it doesn't mean the connection is broken.   

### Lab 2D — Threat Intel: 185.220.101.45
- AbuseIPDB Score: 84%
- Country: Germany
- Reports: 6592
- SOC Action I Would Take: The IP 185.220.101.45 is a known malicious Tor exit node, frequently associated with anonymization services and brute-force attacks.

Validation (True or False Positive) 
If Inbound (Blocked): If our firewall blocked it, I would document it, check for other similar IPs, and close the incident as a benign/expected threat.
If Inbound (Successful): If there are successful logins from this IP, especially to administration panels, I would immediately raise this to a True Positive and treat it as a potential account takeover or breach.
If Outbound: If internal hosts are connecting, it suggests malware communicating with a Tor exit node.

1. Immediate Actions
Check SIEM logs: Identify what type of traffic was seen (e.g., inbound connection attempts, outbound traffic, or internal system communication).
Correlate with threat intel feeds: Confirm if the IP is flagged in multiple sources (it is).
Block if unnecessary: If your organization does not rely on Tor, block this IP at the firewall or intrusion prevention system (IPS).

2. Investigation
Identify target systems: Was the traffic directed at critical assets (e.g., SSH servers, VoIP systems)?
Check for successful logins: Ensure brute-force attempts did not succeed.
Look for lateral movement: If compromised, attackers may pivot internally.

3. Containment & Mitigation
Blacklist IP: Add to your SIEM correlation rules and firewall blocklists.
Enable geo-blocking/anonymizer detection: Consider blocking Tor exit nodes if your business does not require them.
Harden exposed services: Enforce strong authentication (MFA, key-based SSH), rate limits, and intrusion detection.

4. Reporting & Documentation
Raise an incident ticket: Document findings, actions taken, and potential impact
Update threat intelligence feeds: Share with your team to improve detection.
Communicate to stakeholders: If sensitive systems were targeted, escalate appropriately.

⚠️ Risks & Considerations
False positives: Tor traffic can be legitimate (privacy-conscious users, journalists). Blocking all Tor exit nodes may affect accessibility.
Persistent attackers: Even if blocked, attackers may rotate to other Tor exit IPs.
Compliance: Ensure blocking aligns with organizational policies and legal requirements.

### Lab 3A — Open Ports on My Machine
| Port | Protocol | State | Service |
|------|---------|-------|---------|
| | | | |
| | | | |
| | | | |

Suspicious ports found: [Yes/No — explain]

### Lab 4A — DNS Investigation
- google.com resolves to: 
- DNS server used: 
- MX record for google.com: 
- Reverse lookup of 8.8.8.8: 
- Suspicious domain result: 

### Lab 4B — ARP Cache
| IP Address | MAC Address | Device |
|-----------|-------------|--------|
| | | |
| | | |
| | | |

Any duplicate IPs: [Yes/No]

### Lab 4C — Threat Intel Comparison
| | AbuseIPDB | VirusTotal |
|---|-----------|------------|
| Score | | |
| Country | | |
| Verdict | | |

### Lab 4D — HTTP Headers (example.com)
- Status Code: 
- Server type: 
- Content-Type: 
- User-Agent I sent: 

---

## SECTION 3: ATTACK REFERENCE (MY OWN WORDS)

**SYN Flood:** [Explain in your own words — 2 sentences]

**DNS Tunneling:** [Explain in your own words — 2 sentences]

**ARP Poisoning:** [Explain in your own words — 2 sentences]

**Port Scanning:** [Explain in your own words — 2 sentences]

---

## SECTION 4: KEY TAKEAWAYS

1. The most important thing I learned today: 
2. The concept that was hardest to understand: 
3. How I finally understood it: 
4. How I'll use this in a real SOC: 

---

## SECTION 5: INTERVIEW QUESTION PRACTICED

**Q:** [Copy question from below]
**My answer:** [Write in your own words, not memorised]
**What I'd improve:** 

---
*Day 1 of 80 | SOC L2 Apprenticeship*
*Next: Day 2 — IP Addressing, Subnetting & CIDR Notation*
