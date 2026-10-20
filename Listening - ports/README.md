# Listening - ports-README.md
A hands-on networking lab using Kali Linux in UTM to inspect TCP listening ports and UDP sockets with `ss`, understand network interface bindings, and explore how local services are identified during basic security investigations.
# Project 02: Local Listening Ports and Network Services

## Status
Initial inspection completed — service identification in progress.

## Objective
Inspect TCP listening sockets and UDP sockets on a Kali Linux virtual machine and understand how services bind to network interfaces.

## Environment
- Operating system: Kali GNU/Linux Rolling
- Virtualization: UTM
- VM IPv4 address: `192.168.83.2`

## Command Executed
```bash
ss -tulp
```

## Observations

### TCP sockets
- SSH was listening on port 22 on IPv4 (`0.0.0.0`) and IPv6 (`[::]`).
- TCP ports 6789, 6791, 46873, and 34755 were listening on `127.0.0.1`.

### UDP sockets
- A DHCP client socket was shown on `192.168.83.2:bootpc`.
- IPv6 DHCP client sockets were also present.

## Analysis
The SSH service is bound to all local IPv4 interfaces and IPv6 interfaces. Its actual network accessibility depends on firewall rules, routing, and other configuration.

The other observed TCP sockets were bound to the IPv4 loopback address, indicating that they listen locally rather than on all IPv4 interfaces.

The UDP sockets are consistent with DHCP client activity.

The process names associated with the TCP ports have not yet been identified.

## SOC Relevance
Reviewing listening sockets helps analysts understand which services are exposed locally and identify services that may require further investigation. Unexpected listening services should be investigated in context rather than assumed to be malicious.

## Next Steps
- Identify the processes associated with the listening TCP ports.
- Review whether SSH is required and appropriately configured.
- Record the results and capture terminal evidence.

## Limitations
This inspection identifies local sockets only. It does not establish whether services are vulnerable, externally reachable, or malicious.
# Local Listening Ports and Network Services

## Objective
Investigate TCP listening ports, UDP sockets, associated processes, SSH configuration, and firewall status on a Kali Linux virtual machine.

## Environment
- Operating System: Kali GNU/Linux Rolling
- Virtualization: UTM
- Network Interface: eth0
- IPv4 Address: 192.168.83.2

## Commands Executed

```bash
sudo ss -tulp
sudo ss -tulpn
sudo ps -fp 974,621,741,1041,2627,805,704
sudo ps -ww -p 974 -o pid,ppid,user,args
sudo ss -lntup
sudo systemctl status elastic-agent ssh --no-pager
sudo sshd -T | grep -E '^(port|permitrootlogin|passwordauthentication|pubkeyauthentication)'
sudo nft list ruleset
sudo ufw status verbose
systemctl is-active ufw
systemctl is-active firewalld
```

## Observations

### 1. Listening Services

- SSH daemon (`sshd`, PID 741) was listening on TCP port 22 on IPv4 and IPv6 wildcard addresses.
- Elastic Agent (PID 621) was listening on TCP ports 6789 and 6791, bound to 127.0.0.1.
- Elastic OpenTelemetry Collector (PID 974) was listening on TCP ports 46717 and 38959, bound to 127.0.0.1.
- The `dhcpcd` process had UDP sockets associated with DHCPv4 and DHCPv6 client activity.
![Listening ports and network services](./Listening%20ports.png)
### 2. SSH Configuration

The effective SSH configuration showed:

- Port: 22
- PermitRootLogin: prohibit-password
- PubkeyAuthentication: yes
- PasswordAuthentication: yes

Password authentication was enabled, and public-key authentication was also enabled.

### 3. Service Status

Both Elastic Agent and SSH were reported as active and running during the investigation.

### 4. Firewall Checks

- `sudo nft list ruleset` returned no output.
- UFW reported inactive.
- Firewalld reported inactive.

These checks did not identify active firewall rules. Other filtering mechanisms, including UTM networking or upstream controls, have not yet been verified.

## Initial Security Assessment

SSH was listening on all IPv4 and IPv6 interfaces, and password authentication was enabled. If SSH is reachable from another machine, password-based access may increase exposure to password-guessing or credential-reuse attempts.

The Elastic Agent and OpenTelemetry Collector listeners were bound to the IPv4 loopback interface, limiting direct access through those bindings to the local machine.

No confirmed vulnerability or compromise was identified. Further checks are needed to establish SSH reachability, firewall coverage, and whether the current configuration is appropriate.

## Recommended Next Steps

1. Verify the UTM virtual machine's network mode.
2. Investigate applicable host and network firewall controls.
3. Confirm the purpose of the collector listener on port 38959.
4. Review whether SSH password authentication is necessary.
5. Document any further testing and results.

## SOC Relevance

This investigation demonstrates basic service enumeration, process identification, SSH configuration review, and firewall-status checking. These skills support defensive investigations into unexpected listening ports and potentially exposed services.

## Disclaimer

This project was conducted in my own virtual machine environment for cybersecurity learning purposes.
