# Week 1

# Initial Installation and Setup


## Main Server Setup

To begin building my homelab, I repurposed an old gaming PC with the following specs:

* CPU: AMD Ryzen 5 3600X (6 cores)

* RAM: 32 GB

* Storage: 1 TB SSD

The machine was formatted and a fresh install of Proxmox VE was completed. Proxmox now acts as the main hypervisor hosting all virtual machines (VMs) used in my lab. This setup provides snapshotting, backups, VLAN-aware bridges, and resource-efficient VM/container management—all essential for a flexible and isolated cybersecurity environment.
##  Network Architecture

Because this homelab is focused on cybersecurity, I placed strong emphasis on isolation, segmentation, and firewalling. I did not want my potentially compromised lab systems to impact my main home network.
##  Gateway: MikroTik Router

To handle inter-VLAN routing, segmentation, DHCP, and firewall enforcement, I configured an old MikroTik hAP ac router as the homelab gateway. It’s physically connected to my home LAN and functions as the only bridge between the lab environment and the outside world.

* Home LAN IP: 192.168.1.240/24 (static)

* The MikroTik is directly connected to my home router and manages three VLANs internally.

## VLAN Layout
| VLAN Name      | VLAN ID | Purpose                                                               |
| -------------- | ------- | --------------------------------------------------------------------- |
| **Management** | 10      | Access to Proxmox GUI, SSH, Router config. Trusted admin access only. |
| **Attack**     | 20      | Offensive tools and pentest machines like Kali Linux.                 |
| **Vulnerable** | 30      | Simulated target machines with known vulnerabilities.                 |

This segmentation ensures:

* The attack tools are isolated from management and victim infrastructure.

* Vulnerable VMs can be safely exposed to attacks without risk to the lab's control plane.

* All traffic flow is explicitly governed by firewall rules.

### Firewall Rules (MikroTik)

To enforce strict traffic control, I created a MikroTik firewall configuration that:

* Denies traffic from the Attack VLAN to the Home LAN.

* Allows internet access only from the Attack VLAN.

* Permits full access from the Management VLAN to all other VLANs.

* Blocks unsolicited new connections from vulnerable systems.

* Enables return traffic (via established,related) to function correctly.

These rules allow secure yet practical operation of the lab. Here’s a high-level summary:

 * ✅ Allow: Management → Any

 * ✅ Allow: Attack → Vulnerable

 * ✅ Deny: Attack → Home LAN

 * ✅ Allow: Established, Related

 * ❌ Drop: Invalid or New Unauthorized Connections

The firewall is configured with a default deny policy and selectively opens what's needed.

### Connectivity Options

I use two methods to connect to the homelab from my laptop (which sits on the Home LAN):

* Port Forwarding: I forward port 2222 on the MikroTik to port 22 (SSH) on the Kali Linux attack VM. This enables SSH access from the home LAN securely without exposing the VM to the full network.

* Lab Wi-Fi (Optional): The MikroTik’s Wi-Fi can be enabled and bound to the Attack VLAN. This lets me wirelessly join the Attack VLAN with my laptop or phone for hands-on network recon, tool testing, or phishing payload delivery.


## Future Expansion

As I continue progressing in my cybersecurity journey, I have a clear vision for how this homelab will evolve. The current foundation provides stability and flexibility, but several key features and tools are planned to significantly enhance realism, automation, and coverage of both offensive and defensive capabilities.
## Advanced Proxmox Usage

I aim to fully leverage what Proxmox offers:

* Backups: Automate daily or weekly full backups of key VMs and lab states to an external drive or NAS.

* Snapshots: Create pre-configured snapshots for lab scenarios to allow quick resets after testing tools or exploits.

* Templates: Turn commonly used VMs (like Kali, Windows 10, Metasploitable) into templates for faster deployment.

* Cloud-init: Use cloud-init or provisioning scripts to automatically configure lab VMs at first boot.

## VPN or Bastion Access

To access the lab securely from outside the house:

* Deploy a VPN server (WireGuard or OpenVPN) on the router or a dedicated VM.

* Optionally use a jump box (bastion host) in the Management VLAN with multi-factor SSH.

## Monitoring and Visibility

Introduce visibility into what's happening inside the network:

* Use ELK Stack or Grafana + Loki to collect logs and metrics from various machines.

* Monitor network traffic via Suricata, Zeek, or ntopng.

* Generate alerts for potential attacks or abnormal behavior.

## Windows-Focused Scenarios

Because I struggle with Windows and Active Directory (AD), I plan to:

* Get hands-on with Windows Server 2019 as a domain controller.

* Set up Windows 10 clients and join them to the domain.

* Practice enumeration, privilege escalation, and common AD attack paths (via tools like BloodHound, impacket, CrackMapExec, Rubeus).
