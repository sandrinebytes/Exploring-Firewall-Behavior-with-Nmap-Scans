# Exploring-Firewall-Behavior-with-Nmap-Scans
This repository explores firewall behavior with Nmap scans and basic iptables configuration. It demonstrates identifying open and filtered ports, applying interface-specific rules, using port spoofing, and testing proxy routing. Screenshots 📸 and commands provide hands-on examples to understand how firewalls affect network traffic.

Tools: Nmap, Linux VM (or host), iptables/Windows Firewall Objective: Explore how firewalls affect network scanning, identify open/filtered ports, and practice basic firewall configuration and enumeration techniques.

## 💻Lab Setup
* Environment: Linux VM (VirtualBox)
* Network Mode: NAT / Host-Only / Bridged
* Firewall: iptables
* Notes: Screenshots were taken at each major step for documentation.📸

## 🎯Lab Goals
1. Flush existing firewall rules to start fresh.
2. Open ports 22 (SSH) and 80 (HTTP) to allow TCP responses.
3. Verify open ports using Netcat.
4. Use Nmap to scan and observe how the firewall affects results.
5. Document commands, outputs, and observations with screenshots 📸


## 🛠Commands & Steps

Step 1: Flush Existing Firewall Rules

# Flush all existing rules
sudo iptables -F

# Delete all user-defined chains
sudo iptables -X

📸Screenshot included documenting the flushed rules.
