# 🔍 OSINT Reconnaissance Report — owasp.org

> **Purpose:** Educational OSINT (Open Source Intelligence) exercise on `owasp.org`.  
> **Scope:** Passive reconnaissance and legal active scanning of a sanctioned target (Metasploitable2).  
> **Date:** June 22, 2026  
> **Author:** watcher  
> **Status:** Completed  

---

## ⚠️ Disclaimer

This report is produced **strictly for educational purposes**. All information gathered on `owasp.org` was collected using **passive, publicly available tools and data sources only** — no unauthorized access was attempted. The Nmap scan was performed against **Metasploitable2**, a legally sanctioned, intentionally vulnerable virtual machine used for practice in a controlled lab environment.

---

## 📋 Table of Contents

1. [Scope & Methodology](#1-scope--methodology)
2. [WHOIS Lookup](#2-whois-lookup)
3. [DNS Reconnaissance — dig](#3-dns-reconnaissance--dig)
4. [DNS Dumpster — Subdomain Enumeration](#4-dns-dumpster--subdomain-enumeration)
5. [MX & NS Records](#5-mx--ns-records)
6. [TXT Records](#6-txt-records)
7. [Shodan Intelligence — owasp.org](#7-shodan-intelligence--owasporg)
8. [Shodan — Login Pages Discovered](#8-shodan--login-pages-discovered)
9. [Nmap Scan — Metasploitable2 (Legal Target)](#9-nmap-scan--metasploitable2-legal-target)
10. [Summary of Findings](#10-summary-of-findings)
11. [Tools Used](#11-tools-used)

---

## 1. Scope & Methodology

### Target (OSINT — Passive)
| Field | Value |
|---|---|
| Domain | `owasp.org` |
| Recon Type | Passive (OSINT only) |
| Tools | `whois`, `dig`, DNSDumpster, Shodan |

### Target (Active Scan — Legal Lab)
| Field | Value |
|---|---|
| Host | `10.100.136.48` |
| Machine | Metasploitable2 |
| Scan Type | Active (`nmap -sV`) |
| Authorization | Yes — local lab environment |

### Methodology
Reconnaissance was carried out in the following order:

```
WHOIS → DNS (dig) → Subdomain Enum (DNSDumpster) → MX/NS/TXT Records → Shodan OSINT → Nmap (lab target)
```

---

## 2. WHOIS Lookup

**Command:**
```bash
whois owasp.org
```

### Results

| Field | Value |
|---|---|
| Domain Name | `owasp.org` |
| Registry Domain ID | REDACTED |
| Registrar WHOIS Server | `http://whois.godaddy.com` |
| Registrar | GoDaddy.com, LLC |
| Registrar IANA ID | 146 |
| Creation Date | 2001-09-21T00:00:36Z |
| Updated Date | 2024-07-07T13:31:38Z |
| Registry Expiry Date | 2031-09-21T17:00:36Z |
| Name Server 1 | `fay.ns.cloudflare.com` |
| Name Server 2 | `west.ns.cloudflare.com` |
| DNSSEC | Unsigned |
| Domain Status | clientDeleteProhibited, clientRenewProhibited, clientTransferProhibited, clientUpdateProhibited |

### Key Observations
- Domain has been active since **2001** — well-established.
- Managed by **GoDaddy**, DNS handled by **Cloudflare**.
- DNSSEC is **unsigned** — no cryptographic signing of DNS records.
- All four `clientXxxProhibited` statuses are set, protecting against unauthorized modification or transfer.
- Registrant contact information is **REDACTED** (GDPR/privacy protection applied).

---

## 3. DNS Reconnaissance — dig

**Command:**
```bash
dig @1.1.1.1 owasp.org
```

### Output Summary

| Section | Record | TTL | Class | Type | Value |
|---|---|---|---|---|---|
| QUESTION | `owasp.org.` | — | IN | A | — |
| ANSWER | `owasp.org.` | 300 | IN | A | `104.20.44.163` |
| ANSWER | `owasp.org.` | 300 | IN | A | `172.66.157.115` |

**Query Metadata:**

| Field | Value |
|---|---|
| DNS Server Used | `1.1.1.1#53` (Cloudflare — UDP) |
| Status | `NOERROR` |
| Query Time | 52 ms |
| Message Size | 70 bytes |
| Flags | `qr rd ra` |
| EDNS Version | 0, UDP 1232 |

### Key Observations
- Two A records returned — consistent with **Cloudflare Anycast** load balancing.
- IP `104.20.44.163` and `172.66.157.115` are both Cloudflare-owned ranges.
- Short TTL of **300 seconds** allows rapid DNS updates.
- Direct querying of the origin IP is shielded by Cloudflare's proxy.

---

## 4. DNS Dumpster — Subdomain Enumeration

**Tool:** [dnsdumpster.com](https://dnsdumpster.com)  
**Query:** `owasp.org`

### Infrastructure Overview (from DNSDumpster)

| Category | Details |
|---|---|
| Primary Hosting | Cloudflare (dominant) |
| Secondary Hosting | Google, Amazon (AWS), DigitalOcean, Automattic |
| Server Locations | United States, Australia |
| Free Plan Limit | 50 results per domain |

### Sample A Records (Subdomains)

| Subdomain | IP Address | ASN | Provider |
|---|---|---|---|
| `20thanniversary.owasp.org` | `104.20.44.163` | AS13335 | Cloudflare, Inc. |
| `aivss.owasp.org` | `172.66.157.115` | AS13335 | Cloudflare, Inc. |
| `aos.owasp.org` | `104.20.44.163` | AS13335 | Cloudflare, Inc. |
| `asvs.owasp.org` | `172.66.157.115` | AS13335 | Cloudflare, Inc. |
| `austin.owasp.org` | `172.66.157.115` | AS13335 | Cloudflare, Inc. |

### Key Observations
- All enumerated subdomains resolve to **Cloudflare IP ranges** — origin IPs are hidden behind CDN.
- HTTP headers on all subdomains return `"Direct IP access not allowed"` — confirming Cloudflare enforces host-header validation.
- Multiple project-specific subdomains exist (ASVS, AOS, etc.) — each representing an OWASP project or chapter.

---

## 5. MX & NS Records

**Tool:** DNSDumpster  

### MX Records (Mail Exchangers)

| Priority | Hostname | IP Address | ASN | Provider |
|---|---|---|---|---|
| 1 | `aspmx.l.google.com` | `64.233.180.26` | AS15169 | Google LLC |
| 10 | `alt3.aspmx.l.google.com` | `192.178.223.27` | AS15169 | Google LLC |
| 10 | `alt4.aspmx.l.google.com` | `172.253.157.27` | AS15169 | Google LLC |
| 5 | `alt1.aspmx.l.google.com` | `108.177.123.27` | AS15169 | Google LLC |
| 5 | `alt2.aspmx.l.google.com` | `172.253.116.26` | AS15169 | Google LLC |

### NS Records (Authoritative Name Servers)

| Hostname | IP Address | ASN | Provider |
|---|---|---|---|
| `fay.ns.cloudflare.com` | `172.64.32.115` | AS13335 | Cloudflare, Inc. |
| `west.ns.cloudflare.com` | `108.162.193.247` | AS13335 | Cloudflare, Inc. |

### Key Observations
- OWASP uses **Google Workspace** (G Suite) for email — all MX records point to Google infrastructure.
- DNS is fully delegated to **Cloudflare's two nameservers** (`fay` and `west`).
- The SPF record (see TXT section) also authorizes Google and Amazon SES for sending.

---

## 6. TXT Records

**Tool:** DNSDumpster

### Discovered TXT Records

| Record | Notes |
|---|---|
| `google-site-verification=ubHJGF1N2ylOhYxQnIzEIIFaqUodqsIdTLXF-rCX9ps` | Google Search Console ownership verification |
| `google-site-verification=hJ9eCIFoexfh1sb-WVBkVB5PEND3JiaojOVyaNpyWK8` | Additional Google verification token |
| `google-site-verification=_slXlbOCopK1Ss9VQEoxdsNxpScVKvXVB_JtPpyL3eQ` | Additional Google verification token |
| `google-site-verification=I9qx_X9EKlR_rfceG25-iXHBXJvLrmeNbkEdy182iI` | Additional Google verification token |
| `google-site-verification=1zT9Of9pBuTj1rgeGCxMbya3iQQMxFE9-DzUBhftUVQ` | Additional Google verification token |
| `google-site-verification=kmxuuCvLW4gII8YaV-3ilTOLUvjZa3uaipm0tmpVGpU` | Additional Google verification token |
| `atlassian-domain-verification=BhaFKFKoRcW20xvi6UJ3U0CKocKOCgLH6LSuiBYPQ5A53cSCUN6gcbzcKSOmlVGs` | Atlassian (Jira/Confluence) domain ownership |
| `RrGYbfHtHhF55ld5k5Rw87iuBu7wAWOX4GR9zffrTh4=` | Unidentified verification token |
| `MS=ms73859685` | Microsoft 365 / Azure AD domain verification |
| `v=spf1 include:_spf.google.com include:servers.mcsv.net include:amazonses.com -all` | SPF email policy |

### SPF Record Analysis

```
v=spf1 include:_spf.google.com include:servers.mcsv.net include:amazonses.com -all
```

| Mechanism | Meaning |
|---|---|
| `include:_spf.google.com` | Allows Google mail servers to send on behalf of owasp.org |
| `include:servers.mcsv.net` | Allows **Mailchimp** to send (newsletters/marketing) |
| `include:amazonses.com` | Allows **Amazon SES** to send (transactional/automated email) |
| `-all` | Hard fail — all other senders are unauthorized |

### Key Observations
- Multiple Google site-verification tokens suggest **different teams/admins** have claimed the domain over time in Google Search Console.
- Atlassian verification confirms use of **Jira or Confluence** internally.
- Microsoft verification (`MS=`) confirms use of **Microsoft 365 or Azure AD** services.
- Mailchimp in SPF record reveals use of a **bulk email/newsletter platform**.
- The `-all` (hard fail) SPF policy is a good security posture — unauthorized senders will be rejected.

---

## 7. Shodan Intelligence — owasp.org

**Tool:** [Shodan.io](https://www.shodan.io)  
**Query:** `hostname:owasp.org`  
**Total Results:** 25 indexed hosts

### Geographic Distribution

| Country | Count |
|---|---|
| United States | 22 |
| Ireland | 3 |

### Hosting Providers

| Organization | Count |
|---|---|
| DigitalOcean, LLC | 10 |
| Amazon Technologies Inc. | 8 |
| Amazon.com, Inc. | 3 |
| Amazon Data Services Ireland Limited | 2 |
| Cloudflare, Inc. | 2 |

### Open Ports

| Port | Count | Likely Service |
|---|---|---|
| 443 | 18 | HTTPS |
| 80 | 4 | HTTP |
| 22 | 3 | SSH |

### Software & Technologies

| Category | Item | Count |
|---|---|---|
| **Products** | nginx | 7 |
| | Apache httpd | 6 |
| | OpenSSH | 3 |
| | Nextcloud | 1 |
| **Tags** | cloud | 23 |
| | cdn | 4 |
| **OS** | Linux | 2 |
| | Ubuntu | 1 |
| **Web Technologies** | Google Tag Manager | 7 |
| | Nginx | 7 |
| | Onsen UI | 7 |
| | Apache HTTP Server | 5 |
| | HSTS | 3 |
| **Website Titles** | OWASP Nest | 6 |
| | Bad Request | 5 |
| | 302 Found | 3 |
| | Login | 2 |
| | Nextcloud | 1 |

### Key Observations
- OWASP infrastructure is distributed across **DigitalOcean and AWS**, with a smaller presence on Cloudflare.
- The majority of exposed services run on **port 443 (HTTPS)**.
- **Nginx** and **Apache** are the predominant web servers — a common dual-deployment pattern.
- **"Login"** appearing as a page title on 2 hosts prompted further targeted Shodan queries (see next section).
- **Nextcloud** instance detected — a self-hosted file sharing/collaboration platform.
- **HSTS** (HTTP Strict Transport Security) present on 3 hosts — enforces HTTPS connections.

---

## 8. Shodan — Login Pages Discovered

**Query:** `hostname:owasp.org http.title:"Login"`  
**Total Results:** 2

### Result 1

| Field | Value |
|---|---|
| IP | `3.171.117.98` |
| Hostname | `secureflag.owasp.org` |
| Associated Domains | `secureflag.com`, `learn.cybermentis.com`, `cyberdefense.levelblue.com` |
| Hosting | Amazon.com, Inc. — **East Point, United States** |
| SSL Cert Issuer | Amazon RSA 2048 M01 |
| SSL Common Name (Subject) | `secureflag.com` |
| Supported TLS | TLSv1.2, TLSv1.3 |
| HTTP Response | 200 OK, Content-Length: 8557 |
| Last Seen | 2026-06-21 |

### Result 2

| Field | Value |
|---|---|
| IP | `18.172.122.43` |
| Hostname | `secureflag.owasp.org` |
| Associated Domains | `secureflag.com`, `learn.cybermentis.com`, `cyberdefense.levelblue.com` |
| Hosting | Amazon.com, Inc. — **Chicago, United States** |
| SSL Cert Issuer | Amazon RSA 2048 M01 |
| SSL Common Name (Subject) | `secureflag.com` |
| Supported TLS | TLSv1.2, TLSv1.3 |
| HTTP Response | 200 OK, Content-Length: 8557 |
| Last Seen | 2026-06-16 |

### Key Observations
- Both results correspond to `secureflag.owasp.org` — OWASP's **SecureFlag** platform for application security training.
- Two distinct IPs serve the same application — indicating **active-active multi-region deployment** on AWS.
- Both instances use **Amazon-issued TLS certificates** (not Cloudflare), meaning these are direct-IP exposed services (not behind CDN proxy).
- The `Set-Cookie` header includes `AWSALBTG=` cookies — confirming **AWS Application Load Balancer** routing.
- Identical `Content-Length: 8557` on both confirms they serve identical content — consistent load-balanced nodes.

---

## 9. Nmap Scan — Metasploitable2 (Legal Target)

> ⚠️ **This scan was performed on Metasploitable2, an intentionally vulnerable VM in a local lab environment. All findings below relate exclusively to this legal practice target — NOT to owasp.org.**

**Target:** `10.100.136.48`  
**Command:**
```bash
nmap -sV 10.100.136.48 -p- -T4
```

**Host Confirmed Up:** ✅ (0.0073s latency)  
**Closed Ports:** 65,505  
**Open Ports Found:** 29  

### Open Ports & Services

| Port | State | Service | Version / Notes |
|---|---|---|---|
| 21/tcp | open | FTP | vsftpd 2.3.4 |
| 22/tcp | open | SSH | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0) |
| 23/tcp | open | Telnet | Linux telnetd |
| 25/tcp | open | SMTP | Postfix smtpd |
| 53/tcp | open | DNS | ISC BIND 9.4.2 |
| 80/tcp | open | HTTP | Apache httpd 2.2.8 (Ubuntu) DAV/2 |
| 111/tcp | open | RPCbind | 2 (RPC #100000) |
| 139/tcp | open | NetBIOS-SSN | Samba smbd 3.X–4.X (workgroup: WORKGROUP) |
| 445/tcp | open | NetBIOS-SSN | Samba smbd 3.X–4.X (workgroup: WORKGROUP) |
| 512/tcp | open | exec | netkit-rsh rexecd |
| 513/tcp | open | login | — |
| 514/tcp | open | tcpwrapped | — |
| 1099/tcp | open | Java-RMI | GNU Classpath grmiregistry |
| 1524/tcp | open | bindshell | **Metasploitable root shell** |
| 2049/tcp | open | NFS | 2–4 (RPC #100003) |
| 2121/tcp | open | FTP | ProFTPD 1.3.1 |
| 3306/tcp | open | MySQL | MySQL 5.0.51a-3ubuntu5 |
| 3632/tcp | open | distccd | distccd v1 (GNU 4.2.4, Ubuntu 4.2.4-1ubuntu4) |
| 5432/tcp | open | PostgreSQL | PostgreSQL DB 8.3.0–8.3.7 |
| 5900/tcp | open | VNC | VNC (protocol 3.3) |
| 6000/tcp | open | X11 | access denied |
| 6667/tcp | open | IRC | UnrealIRCd |
| 6697/tcp | open | IRC | UnrealIRCd |
| 8009/tcp | open | AJP13 | Apache Jserv (Protocol v1.3) |
| 8180/tcp | open | HTTP | Apache Tomcat/Coyote JSP engine 1.1 |
| 8787/tcp | open | DRb | Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb) |
| 43658/tcp | open | status | 1 (RPC #100024) |
| 44414/tcp | open | mountd | 1–3 (RPC #100005) |
| 57920/tcp | open | nlockmgr | 1–4 (RPC #100021) |
| 59807/tcp | open | Java-RMI | GNU Classpath grmiregistry |

### Host Identification

| Field | Value |
|---|---|
| MAC Address | `08:00:27:AE:48:0B` (Oracle VirtualBox virtual NIC) |
| OS Info | Unix, Linux |
| CPE | `cpe:/o:linux:linux_kernel` |
| Hostnames | `metasploitable.localdomain`, `irc.Metasploitable.LAN` |

### Notable Vulnerabilities (for learning)

| Service | Port | Known Vulnerability / Risk |
|---|---|---|
| **vsftpd 2.3.4** | 21 | Contains a backdoor (CVE-2011-2523) — triggers on smiley `:)` username |
| **Metasploitable root shell** | 1524 | Direct root shell access — **no authentication required** |
| **UnrealIRCd** | 6667/6697 | Contains a backdoor (CVE-2010-2075) — allows remote code execution |
| **Samba 3.X** | 139/445 | Vulnerable to usermap_script exploit (CVE-2007-2447) |
| **distccd** | 3632 | Allows arbitrary command execution (CVE-2004-2687) |
| **Telnet** | 23 | Cleartext protocol — credentials transmitted unencrypted |
| **rexecd / rsh** | 512/513 | Legacy, unauthenticated remote execution — no encryption |
| **NFS** | 2049 | May allow unauthenticated mount of shares |
| **VNC 3.3** | 5900 | Outdated protocol — weak authentication |
| **X11** | 6000 | Exposed display server — potential screen capture risk |
| **Ruby DRb** | 8787 | Allows remote code execution in some configs |

### ifconfig Output (Metasploitable2)

```
eth0:  inet addr: 10.100.136.48   Bcast: 10.100.136.255   Mask: 255.255.255.0
       inet6 addr: 2409:40f3:208a:4b3a:a00:27ff:feae:480b/64 Scope:Global
lo:    inet addr: 127.0.0.1   Mask: 255.0.0.0
```

---

## 10. Summary of Findings

### owasp.org — OSINT Summary

| Area | Finding |
|---|---|
| Registrar | GoDaddy — registered since 2001, expires 2031 |
| DNS Provider | Cloudflare (fay + west nameservers) |
| CDN | Cloudflare — real IPs hidden behind proxy |
| IP Addresses | `104.20.44.163`, `172.66.157.115` (Cloudflare Anycast) |
| Email Provider | Google Workspace (G Suite) |
| Email Marketing | Mailchimp (via SPF record) |
| Transactional Email | Amazon SES (via SPF record) |
| Internal Tools | Atlassian (Jira/Confluence), Microsoft 365 |
| Hosting | DigitalOcean (primary), AWS (secondary), Cloudflare |
| Web Servers | Nginx, Apache httpd |
| Notable Services | SecureFlag training platform (`secureflag.owasp.org`) on AWS |
| DNSSEC | Not configured |
| TLS | TLSv1.2 and TLSv1.3 supported |
| Attack Surface | Largely minimized by Cloudflare proxy — direct IP access blocked |

### Metasploitable2 — Scan Summary

| Area | Finding |
|---|---|
| Total Open Ports | 29 |
| Critical Risk Ports | 1524 (root shell), 21 (backdoor), 6667 (IRC backdoor) |
| Services | FTP, SSH, Telnet, HTTP, SMB, NFS, MySQL, PostgreSQL, VNC, IRC, Java-RMI |
| OS | Linux (Ubuntu-based) |
| Hypervisor | Oracle VirtualBox |
| Overall Risk | **Critical — intentionally vulnerable, lab use only** |

---

## 11. Tools Used

| Tool | Purpose | Type |
|---|---|---|
| `whois` | Domain registration lookup | CLI |
| `dig` | DNS record query | CLI |
| [DNSDumpster](https://dnsdumpster.com) | Subdomain enumeration, MX/NS/TXT records | Web |
| [Shodan.io](https://shodan.io) | Internet-connected device intelligence | Web |
| `nmap -sV` | Service/version detection scan | CLI |
| `ifconfig` | Network interface info on target | CLI |

---

## 📚 References

- [OWASP Foundation](https://owasp.org)
- [Shodan Documentation](https://help.shodan.io)
- [DNSDumpster](https://dnsdumpster.com)
- [Metasploitable2 Overview — Rapid7](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [ICANN WHOIS](https://lookup.icann.org)

---

*Report generated for educational purposes only. No unauthorized systems were accessed during this exercise.*
