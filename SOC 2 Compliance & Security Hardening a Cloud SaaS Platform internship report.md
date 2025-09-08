# Executive summary

Over two consecutive 2‑month internship projects at **Dotcom**, I worked on SOC 2 security compliance for a SaaS cloud platform: penetration testing and infrastructure hardening.  
I performed full‑cycle penetration testing and infrastructure hardening focused on making the company more resilient and aligned with SOC 2 controls.  

Deliverables included prioritized vulnerability reports, proof‑of‑concepts (PoCs) and automated scripts, board presentations with time‑maps and remediation plans, and demonstrable improvements to detection and attack surface management.  

I combined structured methodology (PDCA), focused OSINT and active testing, automation (bash tooling, container tooling), and secure configuration changes (Nginx, LXC/containers, PM2).

---

# Security Engineering & Hardening (2 months)

## Goal
Convert pentesting findings and other best practices into durable security controls; implement practical, repeatable solutions aligned with SOC 2 objectives.

## Solutions implemented
- Nginx hardening towards SOC 2 compliance (configuration) [GitHub].  
- Port Knocking solution to stop scanning open ports [GitHub].  
- iptables firewall rules making ports appear closed instead of filtered [GitHub].  
- Hiding Nginx version from headers and error pages; added necessary security headers [GitHub].  
- Protection against brute force & DDoS: rate & connection‑time limiting [GitHub].  
- Fail2ban to block IPs exceeding thresholds [GitHub].  
- Enforced unprivileged runtime, non‑root execution, and read‑only filesystems.  
- Converted privileged containers to unprivileged and enforced zero‑trust principles.  
- Monitored emerging CVEs (dark web, forums) and integrated mitigations into roadmap.  
- Debugging via logs and error monitoring.  
- Produced evidence for audits.

## Key activities & technical work

### SOC 2 readiness & checklists
- Created tailored SOC 2–oriented checklists and remediation plan for the company’s architecture.

### Nginx & reverse proxy hardening
- Enumerated Nginx leaks (headers, error pages, source files).  
- Implemented config changes: removed version headers, added security headers, fixed CSP issues.  
- Added connection/request rate limiting; integrated Fail2ban with a PoC to block abusive IPs.

### Port Knocking
- Designed and implemented port knocking to hide services and require sequences for access.  
- Made ports appear closed to scanning tools.  
- Automated setup with bash scripts across LXC containers.

### LXC / container hardening
- Converted privileged containers to unprivileged where possible.  
- Enforced non‑root runtime (pm2) and applied read‑only filesystems.  
- Automated container hardening with helper scripts.

### Operational security & best practices
- Promoted secure practices: no password sharing, config backups, zero‑trust enforcement, and CVE monitoring.  
- Maintained shared docs (Google Sheets/Docs) for remediation status and audit evidence.

### Continuous monitoring & validation
- Tracked daily CVEs (dark web, sploitus, exploitDB, NIST, OWASP).  
- Implemented log analysis for troubleshooting and validated fixes via PoCs on dev servers.

## Deliverables
- SOC 2–tailored checklist and remediation roadmap.  
- Automated bash scripts (Port Knocking, LXC helpers).  
- Hardened Nginx configs (headers, version hiding, CSP, rate limits).  
- Fail2ban rules with PoC.  
- Shared tracking docs with transparent statuses.

## Demonstrable outcomes
- Reduced attack surface via Nginx hardening, version hiding, port knocking, and container changes.  
- Improved detection & response with Fail2ban integration and reproducible PoCs.  
- Achieved management buy‑in through board presentations and timelines.  
- Delivered repeatable, automated hardening scripts for proxies and LXCs (GitHub in progress).

## Proven abilities
- End‑to‑end capability: offensive testing → defensive engineering → verification of fixes.  
- Audit‑oriented approach structured with PDCA and SOC 2.  
- Automation & scale: scripted and automated tasks across multiple containers/hosts.  
- Curiosity‑driven research: continuous CVE monitoring and exploration of new components (e.g., Proxmox internals).  
- Strong communication: presented technical outcomes to leadership and documented for cross‑functional teams.  
- Collaborative & independent: worked with leadership, devs, and independently to deliver PoCs and remediation.

---

*Document prepared by: [Your Name]*

