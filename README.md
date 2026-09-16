<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# Active Directory Domain Services Lab — Azure Homelab

This project builds a Windows Server domain controller in Azure to simulate a real company's Active Directory setup — the technology most mid-size and large companies run their employee logins on, and the thing a help desk technician touches more than anything else.

## Overview

Two VMs on the same network: **DC01**, promoted to a domain controller running a new forest, `corp.andrewlab.local`, and **CL01**, a Windows 11 machine joined to that domain the way a regular employee's computer would be. Past the basic join, the directory got organizational units, a dedicated admin account, a handful of sample employee accounts, and a security group — the stuff that separates an empty lab from something that looks like an actual company directory.

## Environment

| Component | Detail |
|---|---|
| Cloud platform | Microsoft Azure |
| Resource group | `rg-adds-lab` |
| Virtual network | `adds-vnet` |
| Domain controller | `DC01` (`adds-dc01`) — Windows Server 2025 Datacenter: Azure Edition, Standard_D2s |
| Client machine | `CL01` (`adds-cl01`) — Windows 11 Pro, Standard_B2s |
| Domain / Forest | `corp.andrewlab.local` (NetBIOS: `CORP`) |
| Admin account | `azureadmin` |
| Networking | Static private IP on both VMs; virtual network DNS pointed at DC01 |
| Remote access | RDP (port 3389) |

**Architecture:**
<img width="1920" height="1759" alt="image" src="https://github.com/user-attachments/assets/6709a3c8-8f46-43cb-8037-92af7e39f0cc" />




## Skills Demonstrated

- Windows Server administration and Active Directory Domain Services (AD DS) role installation
- Promoting a server to a domain controller and standing up a new forest/domain
- DNS configuration in a domain environment, including pointing a virtual network's DNS at a domain controller
- Static private IP assignment for infrastructure other systems need to find reliably
- Organizational Unit (OU) design for structuring accounts and computers
- Security group creation and least-privilege admin practices — a dedicated `azureadmin` account instead of the built-in Administrator
- Client-to-domain join process and cross-machine authentication (`CORP\username`)
- Working knowledge of AD DS deployment mechanics: DSRM recovery password, NetBIOS naming, the DNS delegation warning that shows up in a lab environment
- Azure fundamentals: resource groups, virtual networking, VM provisioning, inbound NSG rules (RDP), cost control

## Build Process

1. **Set up the resource group and network.** `rg-adds-lab` and `adds-vnet` — everything else in this project lives inside them.
2. **Created the domain controller VM.** `adds-dc01` (DC01), Windows Server 2022 Datacenter: Azure Edition, Standard_B2s, RDP open on 3389.
3. **Created the client VM.** `adds-cl01` (CL01), Windows 11 Pro, Standard_B2s, same network, RDP opened the same way.
4. **Made both private IPs static.** Switched DC01 and CL01 from Dynamic to Static, since the rest of the network needed a fixed address to point at for DC01 specifically.
<img width="2880" height="1620" alt="image" src="https://github.com/user-attachments/assets/370bcd26-3105-4bfb-9e0c-f2aef3025604" />


5. **Pointed the network's DNS at DC01.** Set `adds-vnet`'s DNS server to DC01's static IP and restarted both VMs so the change actually took effect.
6. **Installed AD DS and promoted DC01.** Added the Active Directory Domain Services role, then promoted the server to stand up a new forest, `corp.andrewlab.local` (NetBIOS `CORP`) — set a DSRM recovery password along the way and clicked past the DNS delegation warning, which is expected for a lab like this.
7. **Checked that it actually worked.** Logged back into DC01 as `CORP\azureadmin`, then confirmed Active Directory Users and Computers and the DNS forward lookup zone were both there.
8. **Joined CL01 to the domain.** Switched it from a workgroup to `corp.andrewlab.local` using domain admin credentials, then restarted it.
9. **Built out the directory.** Two OUs (`Employees`, `IT`), 5–8 sample employee accounts each forced to change their password at first logon, and a security group, `IT-Support`, with a couple of those users added to it.
10. **Logged in as a brand-new domain user.** Signed into CL01 as one of the accounts from step 9, went through the forced password change, and landed on the desktop — proof that DNS, AD DS, the domain join, and authentication were all actually working together, not just installed.

## What Was Configured

- **Forest/Domain:** `corp.andrewlab.local`, NetBIOS `CORP`, on DC01
- **Organizational Units:** `Employees`, `IT`
- **Security group:** `IT-Support`, with sample users added
- **Admin account:** `azureadmin`
- **Sample users:** 5–8 employee accounts, forced password change at first logon
- **Client join:** `CL01` joined to the domain, DNS pointed at DC01
- **Remote access:** RDP (3389) open on both VMs

## Security Notes

- `azureadmin` handled administration instead of the built-in Administrator account.
- The DSRM recovery password was set and kept separate from domain login credentials.
- New accounts were forced to change their password at first logon rather than keep a static temporary one.
- RDP (3389) is the only inbound port open on either VM.

## Screenshots

Screenshots to add:
- Active Directory Users and Computers showing the `Employees`/`IT` OUs and sample users
- The `IT-Support` group and its members
- A successful domain login on CL01 (`CORP\username`)

## Cleanup

Stopped both VMs ("Stopped (deallocated)") in the Azure Portal whenever they weren't in use, then deleted `rg-adds-lab` once the project was documented, so nothing kept racking up charges in the background.

## Related Projects

- [osTicket Help Desk Ticketing System Lab](https://github.com/AndrewJohnson-1/osTicketLab) — companion Azure homelab covering Linux server administration, a LAMP stack build, and help desk ticketing operations.
