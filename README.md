<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# Active Directory Domain Services Lab — Azure Homelab

This project deploys and configures a Windows Server domain controller in Microsoft Azure to simulate an on-premises Active Directory environment — the exact technology most mid-size and large companies use to manage employee logins, and the single most common thing a help desk technician touches.

## Overview

Two virtual machines were built on the same virtual network: **DC01**, promoted to a domain controller establishing a new Active Directory forest (`corp.andrewlab.local`), and **CL01**, a domain-joined Windows 11 machine representing a regular employee's computer. Beyond the base domain join, the environment was built out with organizational units, a dedicated admin account, several sample employee accounts, and a security group — turning an empty lab into something that behaves like a real company directory.

## Environment

| Component | Detail |
|---|---|
| Cloud platform | Microsoft Azure |
| Resource group | `rg-adds-lab` |
| Virtual network | `adds-vnet` |
| Domain controller | `DC01` (`adds-dc01`) — Windows Server 2022 Datacenter: Azure Edition, Standard_B2s |
| Client machine | `CL01` (`adds-cl01`) — Windows 11 Pro, Standard_B2s |
| Domain / Forest | `corp.andrewlab.local` (NetBIOS: `CORP`) |
| Admin account | `azureadmin` |
| Networking | Static private IP on both VMs; virtual network DNS pointed at DC01 |
| Remote access | RDP (port 3389) |

**Architecture:**
<img width="1920" height="1759" alt="image" src="https://github.com/user-attachments/assets/a120e895-1ab1-4a09-b939-e6e501fdc9d3" />


## Skills Demonstrated

- Windows Server administration and Active Directory Domain Services (AD DS) role installation
- Promoting a server to a domain controller and establishing a new forest/domain
- DNS configuration in a domain environment, including pointing a virtual network's DNS at a domain controller
- Static private IP assignment for infrastructure that other systems need to reliably find
- Organizational Unit (OU) design for structuring accounts and computers
- Security group creation and least-privilege admin account practices (a dedicated `azureadmin` account rather than the built-in Administrator)
- Client-to-domain join process and cross-machine authentication (`CORP\username`)
- Understanding of standard AD DS deployment steps — DSRM recovery password, NetBIOS naming, and the DNS delegation warning that appears in a lab environment
- Azure fundamentals: resource groups, virtual networking, VM provisioning, inbound NSG rules (RDP), cost control

## Build Process

1. **Provisioned the resource group and virtual network** — Created `rg-adds-lab` and `adds-vnet` to hold and connect everything in this project.
2. **Created the domain controller VM (DC01)** — Deployed `adds-dc01` (Windows Server 2022 Datacenter: Azure Edition, Standard_B2s) with RDP (3389) allowed inbound.
3. **Created the client VM (CL01)** — Deployed `adds-cl01` (Windows 11 Pro, Standard_B2s) on the same virtual network, also with RDP (3389) allowed inbound.
4. **Assigned static private IPs** — Switched both VMs' private IP assignment from Dynamic to Static, since the rest of the network would need to reliably find DC01 by a fixed address.
5. **Pointed the virtual network's DNS at DC01** — Set `adds-vnet`'s DNS servers to DC01's static private IP so every VM on the network would resolve domain names through it, then restarted both VMs to pick up the change.
6. **Installed AD DS and promoted DC01 to a domain controller** — Added the Active Directory Domain Services role, then promoted the server to create a new forest, `corp.andrewlab.local` (NetBIOS `CORP`), setting a DSRM recovery password and acknowledging the standard DNS delegation warning along the way.
7. **Verified the domain controller** — Logged back into DC01 using `CORP\azureadmin`, then confirmed Active Directory Users and Computers and the DNS forward lookup zone were both working correctly.
8. **Joined CL01 to the domain** — Changed CL01 from a workgroup to the `corp.andrewlab.local` domain using domain administrator credentials, then restarted it.
9. **Built out the directory structure** — Created two organizational units (`Employees`, `IT`), 5–8 sample employee user accounts (each requiring a password change at first logon), and a security group (`IT-Support`) with members added to it — organizing the directory the way a real IT department would rather than as a flat list.
10. **Confirmed a new domain user could log in end-to-end** — Signed into CL01 as one of the newly created domain users (`CORP\username`), completed the forced password change, and verified the desktop loaded successfully — proving DNS, AD DS, the domain join, and authentication all worked together.

## What Was Configured

- **Forest/Domain:** `corp.andrewlab.local`, NetBIOS `CORP`, established on DC01
- **Organizational Units:** `Employees`, `IT`
- **Security group:** `IT-Support`, with sample users added as members
- **Admin account:** `azureadmin`
- **Sample users:** 5–8 employee accounts created with a forced password change at first logon
- **Client join:** `CL01` joined to the domain with DNS pointed at DC01
- **Remote access:** RDP (3389) allowed inbound on both VMs

## Security Notes

- A dedicated `azureadmin` account was used for administration rather than the built-in Administrator account.
- The DSRM (Directory Services Restore Mode) recovery password was set and stored separately from domain login credentials.
- New user accounts were created with a forced password change at first logon rather than a reusable static password.
- RDP (3389) is the only inbound port opened on either VM's network security group.

## Screenshots

_Add screenshots here to round out the portfolio:_
- Active Directory Users and Computers showing the `Employees`/`IT` OU structure and sample users
- The `IT-Support` security group and its members
- A successful domain login on CL01 (`CORP\username`)

## Cleanup

Both VMs were stopped ("Stopped (deallocated)") in the Azure Portal when not actively in use, and the `rg-adds-lab` resource group was deleted once the project was fully documented, to ensure the lab stopped billing entirely.

## Related Projects

- [osTicket Help Desk Ticketing System Lab](https://github.com/AndrewJohnson-1/osTicketLab) — companion Azure homelab covering Linux server administration, a LAMP stack build, and help desk ticketing operations.
