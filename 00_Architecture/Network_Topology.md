
> [!WARNING]
> Server network topolgy is currently being updated with full vlan segmentation. With this in mind, the IP Addressing table is not valid anymore. IP Addressing is my next step to implement. For now there is new network topology schema created at the bottom of this file although it is not fully developed yet.

## Description And Project History

This project was developed with strong purpose and focus to learn Cybersecurity, IAM and network mechanisms in practice. The main objective was to create safe and isolated home subnet run fully virtually using dedicated unit equipped with Proxmox bare-metal hypervisor.

> [!NOTE]
>		Project objective was to create safe, monitored infrastructure based on Active Directory and SIEM (Wazuh) integration, with isolation from home network.

### Main Goals
- Isolation: The Lab uses two virtual bridges (vmbr0, vmbr1). Whole outside communication is filtered by pfSense firewall.
- Identity & Access Management: Central point of management for AD is Windows Server 2022 unit (DC-01), it is used for GPO and authorization inside the domain.
- Monitoring & SOC: Ubuntu Server equipped with Wazuh Manager, centralized point of capturing logs from all virtual machines.

	
>[!WARNING] 
>		(currently being fixed)The laboratory subnet include technical debt. In production environment, there should be separate network subnet dedicated for security monitoring purposes, although, I decided to leave this issue because of possible Wazuh agents setup corruption and other significant problems.

### IP Addressing

### WAN

| VMachine     | IP Address       | System             | Role                                      |
| ------------ | ---------------- | ------------------ | ----------------------------------------- |
| 501-WAN-Kali | DHCP 192.168.0.x | Kali Linux 2025.04 | Attacks Simulation from WAN(home network) |


### VLAN 10

| VMachine          | IP Address       | System              | Role                             |
| ----------------- | ---------------- | ------------------- | -------------------------------- |
| pfSense           | 192.168.1.1      | FreeBSD             | Firewall / Gateway               |
| DC-01             | 192.168.1.10     | Windows Server 2022 | AD/DNS/GPO                       |
| Wazuh-SOC         | 192.168.1.11     | Ubuntu Server 22.04 | SIEM / Manager                   |
| 201-ADWorkstation | 192.168.1.20     | Windows Server 2022 | Simulating Workstation(endpoint) |
| 202-ADWorkstation | 192.168.1.21     | Windows Server 2022 | Simulating Workstation(endpoint) |


#### VLAN 20

| VMachine     | IP Address     | System             | Role                                      |
| ------------ | -------------- | ------------------ | ----------------------------------------- |
| 2001-db-host | **192.168.2.10 | Ubuntu Server 22.4 | Database Host Machine, Docker, PostgreSQL |

# Network Schema (old schema)

```mermaid
graph TD
subgraph Outside_World [Remote / Home Network]
    direction LR
    Mac[Endpoint Device<br/>Remote Control]
    Kali[501-WAN-Kali<br/>Offensive Testing]
end

Outside_World --- pfWAN

subgraph Proxmox [Proxmox VE - Hypervisor]
    
    subgraph outside_network [vmbr0 - WAN Zone]
        pfWAN[pfSense WAN<br/>192.168.0.113]
    end

    subgraph DB_Zone [vmbr1 - Database]
        Postgres[PostgreSQL - Docker<br/>192.168.2.10]
    end

    subgraph Lab_isolated [vmbr1 - Lab LAN Zone]
        pfLAN[pfSense LAN<br/>192.168.1.1]
        
        DC[DC-01 Server<br/>192.168.1.10]
        Wazuh[Wazuh-SOC Manager<br/>192.168.1.11]
        WK1[201-ADWorkstation<br/>192.168.1.20]
        WK2[202-ADWorkstation<br/>192.168.1.21]
    end
end

pfWAN <--> pfLAN

pfLAN --- DC
pfLAN --- Wazuh
pfLAN --- WK1
pfLAN --- WK2
pfLAN --- Postgres

%% Logs centralization flow
WK1 -.->|Sysmon| Wazuh
WK2 -.->|Sysmon| Wazuh
DC -.->|Sysmon| Wazuh

Postgres -.-o|Future Automation - check concepts| DC
```

# Network Schema (new)
```mermaid
graph TD
    subgraph Access_Layer [REMOTE/HOME ACCESS]
        direction LR
        Remote[Tailscale VPN Client]
        Kali[Kali Linux from WAN = as home network]
    end

    Access_Layer --> pfSense

    subgraph Gateway_Layer [ROUTER]
        pfSense{pfSense Firewall/Router}
    end

    pfSense ==> V20[VLAN 20: DATABASE]
    pfSense ==> V30[VLAN 30: IDENTITY / AD]
    pfSense ==> V40[VLAN 40: SIEM / SOC]
    pfSense ==> V10[VLAN 50: WORKSTATIONS]

    subgraph Segment_10 [Net: 192.168.5.0/24]
        WK1[Workstation 201]
        WK2[Workstation 202]
    end

    subgraph Segment_20 [Net: 192.168.2.0/24]
        Postgres[PostgreSQL DB]
    end

    subgraph Segment_30 [Net: 192.168.3.0/24]
        DC[DC-01 Domain Controller]
    end

    subgraph Segment_40 [Net: 192.168.4.0/24]
        Wazuh[Wazuh Manager]
    end

    V10 --- Segment_10
    V20 --- Segment_20
    V30 --- Segment_30
    V40 --- Segment_40


```
