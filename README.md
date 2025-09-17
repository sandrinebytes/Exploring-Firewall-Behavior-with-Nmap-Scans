# Exploring-Firewall-Behavior-with-Nmap-Scans

⚠️ Note: All scanning and firewall testing demonstrated here are intended for educational or authorized testing purposes only. Do not use these techniques on networks you do not own or have permission to test.

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

# Step 1: Flush Existing Firewall Rules

Flush all existing rules:  
```bash
sudo iptables -F
```

Delete all user-defined chains:
```bash
sudo iptables -X
```
📸Screenshot showing flushed iptables rules on the left and Netcat listeners on ports 22 and 80 on the right. The Netcat listeners are included to simultaneously verify which ports respond, confirming the firewall state and open ports.
![Flushed Rules](flushed_rules.png)

⚡ Observation: All previous firewall rules are cleared. Netcat listeners allow us to check port availability in real time.

# Step 2: Allowing SSH and blocking other TCP
### Example: Allow SSH, block all other incoming TCP traffic

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -j DROP
```

### List current rules with verbose output
```bash
sudo iptables -L -v
```
⚡Observation: SSH is allowed, all other incoming TCP traffic blocked.

# Step 3: Open Ports 22 and 80

## Allow incoming TCP on SSH (22) and HTTP (80)
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```
## Allow responses for established connections
```bash
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
## Drop all other incoming traffic
```bash
sudo iptables -A INPUT -j DROP
```
## List current rules with verbose output

```bash
sudo iptables -L -v
```
📸Screenshot showing the iptables rules applied to block port 80 while allowing port 22.
![IPTables Block Port 80](iptables_block_80.png)
⚡ Observation: Ports 22 and 80 are open, others blocked.

# Step 4: Verify Open Ports with Netcat
I use Netcat listeners on ports 22 (SSH) and 80 (HTTP) to verify which ports respond in real time. No screenshot is included for this step. Running the following commands allows me to test port availability before scanning with Nmap:

## Netcat listener for SSH
```bash
nc -lvnp 22
```
## Netcat listener for HTTP
```bash
nc -lvnp 80
```
⚡ Observation: Ports that respond indicate they are open or reachable, confirming the firewall rules.

# Step 5: Scan Open Ports with Nmap
I open the firewall script in vim to review the rules, then run an Nmap scan to test port availability:
```bash
nmap -p22,80 10.0.2.15
```
📸Screenshot showing vim firewall.sh to review the firewall rules, followed by running nmap -p22,80. Port 22 is open (SSH), and port 80 is open (HTTP) before applying any additional rules.

![Netcat Open Ports](netcat_open_ports.png)

📸Screenshot showing Nmap scan results: port 22 is open (SSH), and port 80 is filtered (HTTP).

![Nmap Open & Filtered Ports](nmap_open_filtered_ports.png)

⚡ Observation: The scan confirms that firewall rules affect port visibility, with port 22 fully open and port 80 filtered.

# Understanding Scan Types and Firewall Detection

There are two approaches we can use to identify filtered versus open ports:
1. Regular Nmap Scan (TCP SYN Scan):
    * This is the default scan Nmap uses, which does not complete the full 3-way TCP handshake.
    * In our case, this scan gave enough information to determine which ports are open (22) and which are filtered (80).
2. ACK Scan (-sA):
    * An ACK scan can help identify if a firewall is blocking traffic.
    * It works by sending ACK packets to the target to see how the firewall responds, helping differentiate between filtered and unfiltered ports.
    * This is useful when you suspect a firewall might be interfering with connectivity.

Using the regular scan was sufficient to identify filtered and open ports, but an ACK scan is another method for confirming firewall behavior.

⚡ Observation: Default scan was enough, but ACK scan provides further firewall confirmation.

# Restricting Firewall Rules by Interface
* Interface-specific rules: iptables allows you to apply rules to a specific network interface.
* For example, you can block a port on one interface while leaving it open on another.
* The -e option in iptables lets you specify which interface the rule should apply to.
Example:

## Block TCP traffic on port 80 only on eth0
```bash
sudo iptables -A INPUT -i eth0 -p tcp --dport 80 -j DROP
```
