
## Description And Project History

This project was developed with strong purpose and focus to learn Cybersecurity, IAM and network mechanisms in practice. The main objective was to create safe and isolated home subnet run fully virtually using dedicated unit equipped with Proxmox bare-metal hypervisor.

> [!NOTE]
>		Project objective was to create safe, monitored infrastructure based on Active Directory and SIEM (Wazuh) integration, with isolation from home network.

### Main Goals
- Isolation: The Lab uses two virtual bridges (vmbr0, vmbr1). Whole outside communication is filtered by pfSense firewall.
- Identity & Access Management: Central point of management for AD is Windows Server 2022 unit (DC-01), it is used for GPO and authorization inside the domain.
- Monitoring & SOC: Ubuntu Server equipped with Wazuh Manager, centralized point of capturing logs from all virtual machines.

	
>		[!NOTE] 
>		The laboratory subnet include technical debt. In production environment, there should be separate network subnet dedicated for security monitoring purposes, although, I decided to leave this issue because of possible Wazuh agents setup corruption and other significant problems.

### IP Addressing

| VMachine          | IP Address       | System              | Role                             |
| ----------------- | ---------------- | ------------------- | -------------------------------- |
| pfSense           | 192.168.1.1     | FreeBSD             | Firewall / Gateway               |
| DC-01             | 192.168.1.10     | Windows Server 2022 | AD/DNS/GPO                       |
| Wazuh-SOC         | 192.168.1.11     | Ubuntu 22.04        | SIEM / Manager                   |
| 201-ADWorkstation | 192.168.1.20     | Windows Server 2022 | Simulating Workstation(endpoint) |
| 202-ADWorkstation | 192.168.1.21     | Windows Server 2022 | Simulating Workstation(endpoint) |
| 501-WAN-Kali      | DHCP 192.168.0.x | Kali Linux 2025.04  | Attacks Simulation               |

# Network Schema

```mermaid
graph TD
    %% Outside section - Remote Access and Testing
    subgraph Outside_World [Remote /Home Network]
        direction LR
        Mac[MacBook Pro<br/>Remote Control]
        Kali[501-WAN-Kali<br/>Offensive Testing]
    end

    Outside_World --- pfWAN

    subgraph Proxmox [Proxmox VE - Hypervisor]
        
        subgraph outside_network [vmbr0 - WAN Zone]
            pfWAN[pfSense WAN<br/>192.168.0.113]
        end

        subgraph Lab_isolated [vmbr1 - Lab LAN Zone]
            pfLAN[pfSense LAN<br/>192.168.1.1]
            
            DC[DC-01 Server<br/>192.168.1.10]
            Wazuh[Wazuh-SOC Manager<br/>192.168.1.11]
            WK1[201-ADWorkstation<br/>192.168.1.20]
            WK2[202-ADWorkstation<br/>192.168.1.21]
        end
    end

    %% Connection between firewall interfaces
    pfWAN <--> pfLAN

    %% Inside-lab connections
    pfLAN --- DC
    pfLAN --- Wazuh
    pfLAN --- WK1
    pfLAN --- WK2

    %% Logs centralization flow
    WK1 -.->|Sysmon| Wazuh
    WK2 -.->|Sysmon| Wazuh
    DC -.->|Sysmon| Wazuh

    %% Styling
    style Outside_World fill:#f9f9f9,stroke:#333,stroke-dasharray: 5 5
    style pfWAN fill:#ffcccc,stroke:#cc0000
    style pfLAN fill:#ffcccc,stroke:#cc0000
    style Kali fill:#e1f5fe,stroke:#01579b
    style Mac fill:#e1f5fe,stroke:#01579b
```
