# CodersArmy Infra

**Enterprise Office Network Infrastructure Design and Implementation**

CodersArmy Infra is an enterprise network and IT infrastructure design project for a software development company. The project focuses on designing, simulating, documenting, and validating a secure, scalable and manageable office infrastructure.

The initial design supports 30–50 employees, with an expansion capacity of approximately 75–100 employees.

The project covers network architecture, VLAN segmentation, dual-WAN connectivity, firewall policies, routing, network security, server infrastructure, centralized storage, backups and monitoring.

> **Project status:** Active development  
> **Simulation platform:** Cisco Packet Tracer 8.2.2  
> **Production firewall target:** OPNsense

## Objectives

- Design a scalable enterprise office network.
- Separate departments and infrastructure services using VLANs.
- Implement redundant ISP connectivity.
- Configure routing, NAT/PAT, and WAN failover.
- Establish perimeter firewall protection.
- Implement internal network segmentation and access-control policies.
- Design secure infrastructure for application servers, databases, NAS, CCTV, and backups.
- Plan centralized monitoring and infrastructure management.
- Maintain technical documentation and implementation evidence.

## Network Architecture

The current Packet Tracer topology follows this architecture

```text
                 ISP 1                  ISP 2
              500 Mbps               500 Mbps
                  |                      |
                  +----------+-----------+
                             |
                       Cisco R1
                    Dual-WAN Router
                    Routing + PAT
                             |
                        FW1 - ASA
                    Perimeter Firewall
                             |
                    SW1 - L3 Core
                   VLANs + Routing
                             |
                  +----------+----------+
                  |                     |
              SW2 - Access          SW3 - Access
                  |                     |
             Departments         Servers and Devices
```

### Network Components

| Component       | Device / Technology | Responsibility                                |
| --------------- | ------------------- | --------------------------------------------- |
| Primary WAN     | ISP1                | Primary internet connection                   |
| Secondary WAN   | ISP2                | Secondary internet connection                 |
| Edge router     | Cisco 2911          | WAN routing, PAT, failover                    |
| Firewall        | Cisco ASA 5505      | Perimeter traffic filtering                   |
| Core switch     | Cisco Catalyst 3560 | Layer 3 routing and VLAN gateways             |
| Access switches | Cisco Catalyst 2960 | End-device connectivity                       |
| End devices     | PC-PT / Server-PT   | Network testing and infrastructure simulation |

**Production architecture note:** The ASA 5505 is used as a Packet Tracer simulation substitute. The intended production firewall is OPNsense, which will handle dual-WAN management, NAT/PAT, perimeter security, and firewall policies.

## IP Addressing and VLAN Design

The internal network uses the private address block

`172.16.0.0/22`

The VLAN allocation is

| VLAN | Department / Purpose | Network         | Gateway      |
| ---: | -------------------- | --------------- | ------------ |
|   10 | IT                   | 172.16.0.0/26   | 172.16.0.1   |
|   20 | HR                   | 172.16.0.64/26  | 172.16.0.65  |
|   30 | Administration       | 172.16.0.128/26 | 172.16.0.129 |
|   40 | Accounts & Finance   | 172.16.0.192/26 | 172.16.0.193 |
|   50 | Development          | 172.16.1.0/26   | 172.16.1.1   |
|   60 | DevOps               | 172.16.1.64/26  | 172.16.1.65  |
|   70 | Application Servers  | 172.16.1.128/27 | 172.16.1.129 |
|   80 | NAS                  | 172.16.1.160/27 | 172.16.1.161 |
|   90 | CCTV                 | 172.16.1.192/26 | 172.16.1.193 |
|  100 | Employee Wi-Fi       | 172.16.2.0/24   | 172.16.2.1   |
|  110 | Guest Wi-Fi          | 172.16.3.0/26   | 172.16.3.1   |
|  120 | Network Management   | 172.16.3.64/27  | 172.16.3.65  |
|  130 | Printers             | 172.16.3.96/27  | 172.16.3.97  |
|  140 | IoT                  | 172.16.3.128/27 | 172.16.3.129 |
|  150 | Backup               | 172.16.3.160/27 | 172.16.3.161 |

Each VLAN has a dedicated gateway on the Layer 3 core switch.

## WAN Architecture

The network uses two simulated ISP connections.

| Connection | Network         | Router IP    | ISP Gateway  |
| ---------- | --------------- | ------------ | ------------ |
| ISP1       | 203.0.113.0/30  | 203.0.113.2  | 203.0.113.1  |
| ISP2       | 198.51.100.0/30 | 198.51.100.2 | 198.51.100.1 |

The current simulation implements

- Primary default routing through ISP1.
- Floating static default route through ISP2.
- Interface down failover testing.
- Recovery to the primary route.
- Outbound PAT on R1.

**Known limitation:** The current Packet Tracer implementation demonstrates interface down failover, not upstream health monitoring or true WAN load balancing. These capabilities are planned for the production OPNsense architecture.

## Security Architecture

Security is implemented through multiple network layers.

### Perimeter Security

FW1 (Cisco ASA 5505 simulation) provides

- Inside and outside security zones.
- Outside inbound access-control lists.
- Stateful ICMP inspection.
- Filtering of unsolicited external traffic destined for the office network.

### Internal Network Security

The Layer 3 core switch is responsible for internal VLAN routing and will enforce department-level access restrictions through ACLs.

Planned policies include

- Guest Wi-Fi internet restrictions.
- IT-only network management access.
- Restricted database access.
- Controlled application-to-database communication.
- CCTV access restrictions.
- Printer access controls.
- Isolated backup infrastructure.

The Packet Tracer ASA 5505 has simulation and license limitations. It is not treated as a complete replacement for production OPNsense.

## Current Implementation Status

| Feature                        | Status                      |
| ------------------------------ | --------------------------- |
| Initial network topology       | Completed                   |
| ISP1 connectivity              | Verified                    |
| ISP2 connectivity              | Verified                    |
| WAN floating static routes     | Configured                  |
| Interface-down WAN failover    | Tested                      |
| 15 VLANs                       | Configured                  |
| Core and access switching      | Configured                  |
| Inter-VLAN routing             | Verified                    |
| R1 outbound PAT                | Verified                    |
| ASA perimeter firewall         | Configured                  |
| ASA outside inbound ACL        | Verified in Simulation Mode |
| Guest Wi-Fi restrictions       | Pending                     |
| Internal security ACLs         | Pending                     |
| Server infrastructure          | Planned                     |
| NAS and backup architecture    | Planned                     |
| Monitoring and observability   | Planned                     |
| Production OPNsense deployment | Planned                     |

## Verification Evidence

The following network tests have been performed:

| Test                        | Expected Result          | Observed Result    |
| --------------------------- | ------------------------ | ------------------ |
| PC-IT to PC-HR              | Inter-VLAN communication | Successful         |
| PC-IT to ISP1 endpoint      | Internet connectivity    | Successful         |
| PC-IT through R1 PAT        | Address translation      | Verified           |
| PC-EXTERNAL to ISP1 gateway | External connectivity    | Successful         |
| PC-EXTERNAL to R1 WAN       | External connectivity    | Successful         |
| PC-EXTERNAL to PC-IT        | Blocked by ASA ACL       | Denied and dropped |

Screenshots and test evidence will be maintained in the `evidence/` directory.

## Planned Infrastructure

The future production infrastructure will include:

- OPNsense firewall with dual-WAN gateway management.
- Cisco Layer 3 core and access switching.
- Proxmox VE virtualization.
- Internal application and database servers.
- Centralized NAS storage.
- Dedicated backup infrastructure.
- Prometheus and Grafana monitoring.
- Network and infrastructure automation using Ansible.
- Centralized DNS, DHCP, and NTP services.
- Restricted outbound access and internal data protection policies.

## Repository Structure

```text
codersArmy-infra/
├── README.md
├── docs/
│   ├── architecture/
│   ├── network/
│   ├── security/
│   ├── infrastructure/
│   └── implementation/
├── diagrams/
│   ├── network-topology/
│   ├── vlan-architecture/
│   └── security-architecture/
├── packet-tracer/
│   ├── configurations/
│   └── exports/
├── tests/
│   ├── connectivity/
│   ├── routing/
│   ├── nat/
│   ├── firewall/
│   └── failover/
├── evidence/
│   ├── topology/
│   ├── connectivity/
│   ├── security/
│   └── failover/
```

Directories will be populated as their corresponding implementation milestones are completed.

## Development Approach

This project follows an incremental implementation workflow:

1. Design the architecture.
2. Implement the network topology.
3. Configure routing and switching.
4. Implement network segmentation.
5. Configure firewall policies.
6. Verify connectivity and security.
7. Document implementation evidence.
8. Develop the production infrastructure architecture.

Each meaningful milestone will be documented and committed independently using Conventional Commits.

This repository documents the design decisions, configurations, implementation progress, verification results and production architecture of the infrastructure project.
