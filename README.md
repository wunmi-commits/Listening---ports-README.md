# Listening---ports-README.md
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
