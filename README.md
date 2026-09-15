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
