# Purpose

This server environment was designed, build and it's continously being upgraded by me.
I decided to dive deep into security and networking by building this infrastructure step by step, making many design mistakes in the mean-time because I understood that by reading and watching I won't learn to do stuff on my own.

> [!NOTE]
I'm spending my free time developing, upgrading and implementing new mechanism and tools, for better safety and performance expanding my knowledge and troubleshooting skillset.


# Technology

- ***Wazuh SIEM*** - used for Logs Cetralization from all VMs.
- ***pfSense firewall*** equipped with ***Suricata IDS*** for network monitoring.
- ***Postgre SQL*** Engine implemented inside ***Docker*** Container (Database-build ready).
-  ***Tailscale Mesh VPN*** - used for secure remote connection using pfSense subnet routing and ***WireGuard***.
- ***Active Directory*** - currently not being actively developed because of focus shift into networking and infrastructure rebuild and hardening.
- ***Proxmox VE*** - Bare-Metal type 1 hypervisor


# Whole server infrastructure is run on a single cluster:

Dell Inspiron 15 7000 Gaming:
- i7 7700HQ
- GTX 1050ti
- 32GB RAM 2666GHz
- 256GB SSD m2