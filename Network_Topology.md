
## Essentials

> [!NOTE]
This project was developed with strong purpose and focus to learn Cybersecurity, Networks and infrastructure management in practice. The main objective was to create safe and isolated network simulating enterprise environment run fully virtually using dedicated unit equipped with Proxmox bare-metal hypervisor.

### IP Addressing

### WAN

| VMachine     | IP Address       | System             | Role                                                                      |
| ------------ | ---------------- | ------------------ | ------------------------------------------------------------------------- |
| 501-WAN-Kali | DHCP 192.168.0.x | Kali Linux 2025.04 | Attacks Simulation from WAN(home network should be interpreted as my WAN) |

### LAN / VLAN

| VMachine          | VLAN TAG | IP Address   | System              | Role                                      |     |
| ----------------- | -------- | ------------ | ------------------- | ----------------------------------------- | --- |
| pfSense           | ----     | 192.168.1.1  | FreeBSD             | Firewall / Gateway                        |     |
| DC-01             | 30       | 192.168.3.10 | Windows Server 2022 | AD/DNS/GPO                                |     |
| Wazuh-SOC         | 40       | 192.168.4.10 | Ubuntu Server 22.04 | SIEM / Manager                            |     |
| 201-ADWorkstation | 50       | 192.168.5.10 | Windows Server 2022 | Simulating Workstation(endpoint)          |     |
| 202-ADWorkstation | 50       | 192.168.5.11 | Windows Server 2022 | Simulating Workstation(endpoint)          |     |
| 2001-db-host      | 20       | 192.168.2.10 | Ubuntu Server 22.4  | Database Host Machine, Docker, PostgreSQL |     |



# Network Schema
```mermaid
graph TD
subgraph Access_Layer [REMOTE/HOME ACCESS]
direction LR
Remote[Tailscale VPN Client]
Kali[Kali Linux from WAN = as home network]
end
Access_Layer --> pfSense

subgraph Gateway_Layer [ROUTER]
    pfSense{pfSense Firewall/Router <br/> GW: .1}
end

pfSense ==> V20[VLAN 20: DATABASE]
pfSense ==> V30[VLAN 30: IDENTITY / AD]
pfSense ==> V40[VLAN 40: SIEM / SOC]
pfSense ==> V10[VLAN 50: WORKSTATIONS]

subgraph Segment_10 [Net: 192.168.5.0/24]
    WK1[Workstation 201 <br/> 192.168.5.10]
    WK2[Workstation 202 <br/> 192.168.5.11]
end

subgraph Segment_20 [Net: 192.168.2.0/24]
    Postgres[PostgreSQL DB <br/> 192.168.2.10]
end

subgraph Segment_30 [Net: 192.168.3.0/24]
    DC[DC-01 Domain Controller <br/> 192.168.3.10]
end

subgraph Segment_40 [Net: 192.168.4.0/24]
    Wazuh[Wazuh Manager <br/> 192.168.4.10]
end

V10 --- Segment_10
V20 --- Segment_20
V30 --- Segment_30
V40 --- Segment_40
```
