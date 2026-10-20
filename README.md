A hands-on networking lab using Kali Linux in UTM to inspect TCP listening ports and UDP sockets with `ss`, understand network interface bindings, and explore how local services are identified during basic security investigations.
# Project 02: Local Listening Ports and Network Services

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
