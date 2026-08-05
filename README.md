# ActiveDirectoryLab
Azure Account + DC01 Deployment

Project: Hybrid Active Directory / Entra ID Lab Date: [fill in] Time spent: ~4 hrs

Goal

Stand up the cloud foundation for the lab: an Azure subscription (which doubles as my Entra ID tenant) and a Windows Server 2022 VM (DC01) that will become the domain controller.

What I did
Created a free Azure account. This also automatically provisioned a Microsoft Entra ID tenant under the subscription, which I'll use later for the hybrid identity / Conditional Access work.
Installed Windows App on my Mac — Microsoft's current RDP client (it replaced the old "Microsoft Remote Desktop" app). Runs natively on Apple Silicon.
Deployed a Windows Server 2022 VM named DC01 from the Azure Portal quickstart, sized Standard B2s v2 (2 vCPU / 8 GiB RAM).
Configured the VM's Network Security Group to allow inbound RDP (port 3389), restricted to my home IP address.
Connected to DC01 using Windows App and confirmed I could log in.
Challenges & fixes
VM size: The size I originally planned to use, B2s, wasn't available to select. Turns out the original B-series (v1) is being retired across most Azure regions, and free-trial subscriptions can't request quota increases to unlock it. Used B2s v2 instead — same burstable family, more RAM (8 GiB vs 4 GiB), similar cost (~$0.08/hr).
[fill in: did the NSG/IP-restricted RDP rule cause any connection issues? Note what you saw and how you fixed it — e.g. "my home IP had changed since I created the rule; updated the NSG source IP and reconnected."]
Notes for later
Azure free-trial subscriptions cap out at 4 total vCPUs. DC01 uses 2, leaving exactly enough headroom for CLIENT01 (Day 3) at 2 vCPU each — no room for a third VM without shutting one down first.
Stopping (deallocating) the VM when not actively working keeps this well under the $200 / 30-day Azure credit.
