VITALTECH — Secure Kerberos + NFS + Suricata IDS/IPS

A network lab demonstrating centralized Kerberos authentication, secure NFS file sharing (krb5p), and network inspection with Suricata, on a VLAN-segmented infrastructure.

1. Summary

This lab builds a small enterprise-style network to demonstrate defense-in-depth security principles: centralized identity management with Kerberos, encrypted and authenticated file sharing over NFS, and network traffic inspection with Suricata (IDS/IPS). The core result is empirical, not theoretical: a penetration test confirms that plain NFS (sec=sys) is vulnerable to UID spoofing and cleartext traffic interception, and that switching to Kerberos-secured NFS (sec=krb5p) closes both of these attack paths.

2. Architecture

(diagram: GNS3 topology screenshot)

VyOS-Router: DHCP, DNS (forward + reverse for vitaltech.local), NAT, and inter-VLAN routing (router-on-a-stick)
VLAN 10 (192.168.10.0/24): server segment — KDC-SRV, NFS-SRV
VLAN 20 (192.168.20.0/24): client segment — DebUser-1 (legitimate client), Atk-1 (Kali, attacker)
Suricata: positioned in-line between VyOS and the internal switch, inspecting all inter-VLAN traffic
3. Tech Stack
GNS3 (network orchestration)
VyOS (routing, DNS, DHCP, NAT)
MIT Kerberos 5 (realm VITALTECH.LOCAL)
NFSv4 (secure export with sec=krb5p)
Suricata (IDS/IPS)
Kali Linux (penetration testing)
4. What This Lab Demonstrates
Centralized authentication via Kerberos across multiple services
Role-based access control (RBAC) across 5 department groups, each with its own NFS export and Unix group
End-to-end encryption of NFS traffic (sec=krb5p)
Network segmentation with mandatory inspection (Suricata in-line, not bypassable)
A concrete, tested demonstration that Kerberos changes the outcome of a real attack — not just a theoretical claim
5. Penetration Test Results (summary)

Without Kerberos (sec=sys), two attacks succeeded:

UID spoofing: an attacker-controlled machine impersonated a legitimate user by matching their UID, with no password or ticket required
Cleartext interception: NFS traffic was readable in transit, with no encryption

With Kerberos (sec=krb5p), both attacks are prevented by design, since access requires a valid Kerberos ticket and all traffic is encrypted.

Full details: docs/pentest-report.md

6. Repository Structure
├── README.md
├── docs/
│   ├── architecture.md
│   ├── vyos-setup.md
│   ├── kerberos-setup.md
│   ├── nfs-setup.md
│   ├── suricata-setup.md
│   └── pentest-report.md
├── configs/
│   ├── vyos/
│   ├── suricata/
│   ├── nfs/
│   └── netplan/
├── screenshots/
└── LICENSE
7. Known Limitations / What I'd Do Differently
Configuration was done manually rather than with infrastructure-as-code (Ansible/Terraform) — the natural next step for a production-like setup
Suricata's IPS mode is functional but currently relies on generic Emerging Threats rules rather than tuned custom drop rules for active blocking
No high availability — each service (KDC, NFS-SRV, VyOS) is a single point of failure
8. Next Project

A follow-up lab will focus specifically on Suricata IDS/IPS with Prometheus and Grafana for monitoring and alerting, plus a dedicated exploration of Kerberos's own limitations.

9. Author

Kossi Didier Vital AYI
https://www.linkedin.com/in/vital-ayi
kossi.ayi.di@gmail.com

You can contact me if you need help.
