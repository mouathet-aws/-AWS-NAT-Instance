# -AWS-NAT-Instance
# AWS Custom VPC Architecture with Linux NAT Instance

## 📌 Project Overview
This repository contains the complete documentation and step-by-step configuration for building a highly secure, custom Network Architecture on *AWS. The main objective is to host backend servers inside a **Private Subnet* (isolated from the public internet) while allowing them to fetch updates and outbound patches safely using a custom-configured *Linux NAT Instance*.

---

## 🛠️ Architecture Components
- *Custom VPC:* my-vpc-mouath-01
- *Public Subnet:* Hosts the NAT Instance with an internet gateway route.
- *Private Subnet:* Isolated environment hosting secure workload EC2 instances.
- *NAT Instance:* Amazon Linux 2023 EC2 instance configured as a router (t2.micro / t3.micro).

---

## 🚀 Step-by-Step Implementation

### Step 1: NAT Instance Provisioning & AWS Network Tweaks
1. Launched an EC2 Instance in the *Public Subnet* named NAT-instance-free-mouath-03.
2. *Critical Security Step:* Disabled *Source/Destination Check* on the NAT instance (Actions -> Networking -> Change source/destination check -> *Disable*) to allow it to route third-party traffic.

### Step 2: Linux OS Kernel & Routing Configuration (Amazon Linux 2023)
Amazon Linux 2023 utilizes nftables by default. To implement standard routing rules, the compatibility package was installed:

bash
# 1. Enable IP Forwarding in the Linux Kernel
sudo sysctl -w net.ipv4.ip_forward=1

# 2. Install iptables compatibility packages via DNF
sudo dnf install iptables -y

# 3. Identify the active network interface name
ip link show  # Identified as 'ens5'

# 4. Configure IP Masquerading using the correct network interface
sudo iptables -t nat -A POSTROUTING -o ens5 -j MASQUERADE


### Step 3: Route Table Configurations
1. Updated the *Private Route Table* associated with the Private Subnet.
2. Added a default route pointing all outbound internet traffic (0.0.0.0/0) to the *NAT EC2 Instance ID* (i-0324fabf0d8f6a0da).

---

## 🔍 Verification & Troubleshooting
To verify the architecture, I connected to the isolated instance inside the *Private Subnet* via EC2 Instance Connect Endpoint and ran a connectivity test:

bash
ping google.com

*Result:* Successful ICMP packet transmission with low latency (time=1.66 ms), proving that outbound internet routing via the custom NAT Instance is fully operational.

---
Documented with 💻 by Mouath - Cloud Infrastructure Learning Journey.
