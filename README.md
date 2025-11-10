GlobalCorp Inc. Enterprise Network Implementation
1. Project Summary
This project simulates the design, implementation, and verification of a scalable, resilient, and secure enterprise network using Cisco Packet Tracer. The topology connects a new Corporate Headquarters (HQ), a Regional Branch Office, and a centralized Data Center (DC).
The design successfully implements a dual-stack (IPv4/IPv6) environment, advanced IP addressing with VLSM, and high-availability protocols. Key technologies include VLANs for segmentation, HSRP for gateway redundancy, Router-on-a-Stick (ROAS) for a cost-effective branch design, static routing with summarization for efficient WAN connectivity, and centralized DHCP services.
2. Business Scenario & Requirements
GlobalCorp Inc. is a growing multi-site organization. As part of its expansion, the company needed to build a new network infrastructure from the ground up to connect its main HQ, a new regional office, and a dedicated server farm.
The primary business requirements were:
    • High Availability: The Corporate HQ network is mission-critical. Any gateway or core device failure must not result in downtime for employees.
    • Security & Segmentation: Departments (e.g., Sales, Risk Management) must be logically isolated to prevent unauthorized data access and contain broadcast traffic. Voice traffic at the branch must be on its own dedicated network.
    • Efficient IP Management: The company required an IP addressing scheme that conserved addresses and was scalable for future growth. They also wanted to manage all IP leases from a single, central location.
    • Cost-Effectiveness: The smaller Regional Office needed full segmentation capabilities (like the HQ) but without the high cost of purchasing redundant Layer 3 switches.
    • Future-Proofing: The entire network needed to be compatible with IPv6 to prepare for the eventual exhaustion of IPv4 and modern internet standards.
3. Network Design & Topology
The network was designed as a full mash WAN topology.
    • Site 1: Corporate HQ (Main Branch)
        ◦ Design: A three-tier hierarchical model was used for high performance and scalability.
        ◦ Core/Distribution Layer: Two redundant Multilayer Switches (MLS) handle all high-speed Inter-VLAN routing.
        ◦ Access Layer: Three access switches provide end-device connectivity for the various departments.
    • Site 2: Regional Office (Branch 2)
        ◦ Design: A Router-on-a-Stick (ROAS) model was implemented.
        ◦ Rationale: This is a highly cost-effective solution for smaller sites. It uses a single router port, configured as an 802.1q trunk, to route traffic for multiple VLANs (Operations, Voice, Management) via logical sub-interfaces.
    • Site 3: Data Center (Branch 3)
        ◦ Design: A simple, secure server farm topology.
        ◦ Purpose: This site hosts critical, centralized company resources, including the main DHCP server and internal web servers.
4. IP Addressing & VLAN Strategy
VLAN Segmentation
The network is logically segmented by department and function.
    • Rationale: This design choice was crucial for security (isolating departments), performance (reducing the size of broadcast domains), and policy management (allowing for future access-control lists per-department).
IP Addressing
A Variable Length Subnet Masking (VLSM) scheme was used for the IPv4 plan.
    • Rationale: VLSM allows for the precise allocation of IP addresses based on the host requirements of each department. This eliminates the waste associated with traditional classful subnetting and ensures the IP plan is efficient and scalable. The network is also dual-stacked with IPv6 to ensure future-readiness.
   
Site 1: Corporate HQ

VLAN ID	Department	Hosts Req.	IPv4 Network	IPv6 Network

VLAN 10	Sales		192.168.0.0/20	2001:db8:acad::/64

VLAN 20	Customer Service	192.168.16.0/21	2001:db8:acad:1::/64

VLAN 30	Risk Management		192.168.24.0/22	2001:db8:acad:2::/64

VLAN 40	IT	512	192.168.28.0/23	2001:db8:acad:3::/64

VLAN 99	Management	192.168.30.0/28	2001:db8:acad:4::/64

Site 2: Regional Office

VLAN ID	Department	Hosts Req.	IPv4 Network	IPv6 Network

VLAN 10	Operations	192.168.32.0/26	2001:db8:acad:10::/64

VLAN 20	Voice	192.168.32.64/26	2001:db8:acad:11::/64

VLAN 99	Management	 	192.168.32.128/30	2001:db8:acad:12::/64

Site 3: Data Center

VLAN ID	Service	IPv4 Network	IPv6 Network

VLAN 10	Server Group A	192.168.33.0/29	2001:db8:acad:20::/64

VLAN 20	Server Group B	192.168.33.8/29	2001:db8:acad:21::/64

6. Core Technology Implementation & Rationale
This section details why specific technologies were chosen to meet the business requirements.
High Availability: HSRP (Hot Standby Router Protocol)
    • What: HSRP was configured on the two Multilayer Switches (MLS) at the HQ for all five VLAN gateways.
    • Why: This directly meets the high-availability requirement. All end devices at the HQ use a single virtual IP address as their default gateway.
        ◦ One switch (MLS 1) acts as the Active router for VLANs 10 and 20.
        ◦ The other switch (MLS 2) acts as the Active router for VLANs 30, 40, and 99 (providing simple load balancing).
        ◦ If the Active router for any VLAN fails, the Standby router instantly takes over its role. This ensures zero effective downtime for users and maintains continuous network connectivity.
Centralized Services: DHCP Relay
    • What: A single DHCP server was built in the Data Center (192.168.33.2) and configured with IP pools for every VLAN in the entire company.
    • Why: This design centralizes and simplifies IP address management. Instead of managing 10+ different DHCP servers at each site, the IT team can manage all leases, scopes, and reservations from one device. This is more secure and vastly more efficient.
    • How: The ip helper-address 192.168.33.2 command was configured on every SVI (at HQ) and sub-interface (at the Branch) to forward client DHCP requests across the WAN to the central server.
WAN Connectivity: Static Routing & Summarization
    • What: Static routes were used to connect the three sites.
    • Why: For a fixed Full-mash topology of this size, static routing provides simplicity, security, and precise control. It avoids the configuration overhead and security risks of a dynamic routing protocol.
    • Optimization: Route summarization (supernetting) was used. For example, the HQ router has a single static route (192.168.32.0/25) to reach the entire Regional Office, rather than three separate routes. This keeps routing tables small and makes packet forwarding highly efficient.
7. Testing & Validation 
The network's functionality was confirmed through a rigorous testing plan:
    1. DHCP Verification: All PCs in all VLANs at all sites successfully leased the correct IP address, subnet mask, and default gateway from the central server (192.168.33.2).
    2. Inter-VLAN Connectivity: Devices in different VLANs at the same site (e.g., HQ Sales to HQ IT) could successfully ping each other, confirming Inter-VLAN routing was working.
    3. Site-to-Site Connectivity: All devices could successfully ping devices at other sites (e.g., HQ PC to Branch PC, Branch PC to DC Server), confirming the static WAN routes were correct.
    4. HSRP Failover Test: A continuous ping was run from an HQ PC to a DC server. The active MLS for that PC's VLAN was manually disconnected. The ping dropped for only 1-2 packets before the standby MLS took over and connectivity was restored, successfully proving the high-availability design works.
8. Key Skills Demonstrated
    • Network Design: Hierarchical (Core/Distribution/Access) and Router-on-a-Stick (ROAS) topologies.
    • IP Addressing: IPv4 (VLSM, Subnetting), IPv6, and Dual-Stack implementation.
    • Layer 2 Switching: VLANs, 802.1q Trunking, Access Ports.
    • Layer 3 Routing: Static Routing, Route Summarization (Supernetting), Inter-VLAN Routing, SVI configuration.
    • Network Redundancy: First-Hop Redundancy Protocol (HSRP) configuration and validation.
    • Network Services: DHCP Server configuration and DHCP Relay (ip helper-address).
    • Simulation & Verification: End-to-end testing and troubleshooting in Cisco Packet Tracer (ping, traceroute, show commands).
