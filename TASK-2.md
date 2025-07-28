# How to Set Up a Site-to-Site VPN Using Hyper-V

This document provides a step-by-step guide to configuring a Site-to-Site (S2S) VPN between your on-premises environment (using Hyper-V) and a remote network, such as Microsoft Azure. This guide focuses on using Windows Server’s Routing and Remote Access Service (RRAS) running as a Hyper-V virtual machine, which is a common and flexible approach for site-to-site connectivity.

## Prerequisites

- At least two locations: one on-premises (your Hyper-V environment) and a remote site (such as Azure).
- Each site must have non-overlapping IP address ranges.
- Administrative access to Hyper-V and your target remote environment.
- A Windows Server VM (2016/2019/2022) on Hyper-V, with RRAS installed.
- The public IP address of your remote site’s VPN gateway and a shared key.
- For Azure: an active subscription and a supported VPN type.

## 1. Prepare Hyper-V Environment

- Open Hyper-V Manager.
- Create a new virtual machine to act as your VPN server (running Windows Server).
- Add two network adapters to this VM:
  - One mapped to your local LAN (private/internal Hyper-V switch).
  - One mapped to your WAN/Internet (external Hyper-V switch).

## 2. Configure Networking for the VM

- Assign appropriate static IP addresses to each network adapter in the VM:
  - **LAN adapter**: address in your local network range.
  - **WAN adapter**: address that can access the Internet.

## 3. Install and Configure RRAS

- In your Windows Server VM:
  - Open **Server Manager** → **Add Roles and Features** → **Network Policy and Access Services** → **Routing and Remote Access Service (RRAS)**.
- Open **Routing and Remote Access** from Administrative Tools.
- Right-click the server name and select **Configure and Enable Routing and Remote Access**.
- Choose **Secure connection between two private networks**.
- Select **Yes** for demand-dial connections.

## 4. Create the Demand-Dial (Site-to-Site VPN) Interface

- In the RRAS console, right-click **Network Interfaces** → **New Demand-Dial Interface**.
- Name the interface (e.g., “Site-to-Site VPN”).
- Select **Connect using virtual private networking (VPN)** and choose **IKEv2** as the VPN type, unless otherwise required by your remote site.
- Enter the destination public IP of the remote VPN gateway.
- Specify the shared key/password that will be used at both ends.
- Specify the remote network address ranges that will be reachable via the VPN.

## 5. Configure Routing

- In RRAS, configure static routes to ensure that traffic to the remote network is routed through the VPN interface.
- On the remote site (e.g., Azure), ensure routes are set for your on-premises network as well.

## 6. Complete and Test the Connection

- Finish the RRAS wizard.
- Right-click the new VPN interface in RRAS and select **Connect**.
- On successful connection, you should be able to ping or RDP/SSH between the networks.
- Check the status in the Hyper-V VM, as well as the remote site’s VPN connection dashboard (e.g., Azure portal).

## 7. (Optional) Secure and Harden

- Restrict access on the RRAS server using Windows Firewall.
- Use strong shared keys and periodically rotate them.

## Key Reference Steps (Azure Example)

| Step                        | Where to Configure                        | Notes                        |
|-----------------------------|------------------------------------------|------------------------------|
| Create Virtual Network      | Azure Portal                             | For the cloud side            |
| Create Local Network Gateway| Azure Portal                             | Represents on-prem network    |
| Create VPN Gateway          | Azure Portal                             | Route-based recommended       |
| Create Connection           | Azure Portal (or RRAS on Hyper-V)       | Use shared key, IKEv2         |
| Static Routes               | RRAS & Azure                            | Ensure bidirectional routing  |


## Summary

By following these steps, you can interconnect two sites (on-premises using Hyper-V and a cloud or remote branch) using a secure, standards-based VPN. This setup is commonly used for hybrid cloud scenarios, disaster recovery, and securely connecting multiple office locations.



