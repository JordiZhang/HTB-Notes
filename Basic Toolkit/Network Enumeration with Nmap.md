# Table of Contents
- [[#Introduction to Nmap|Introduction to Nmap]]
	- [[#Introduction to Nmap#Uses|Uses]]
	- [[#Introduction to Nmap#Nmap Architecture|Nmap Architecture]]
	- [[#Introduction to Nmap#Syntax|Syntax]]
- [[#Host Discovery|Host Discovery]]
	- [[#Host Discovery#Scan Network range|Scan Network range]]
					- [[#Note:|Note:]]
	- [[#Host Discovery#Scan an IP list|Scan an IP list]]
	- [[#Host Discovery#Scan Multiple IPs|Scan Multiple IPs]]
	- [[#Host Discovery#Scan Single IP|Scan Single IP]]
- [[#Host and Port Scanning|Host and Port Scanning]]
	- [[#Host and Port Scanning#Discovering Open TCP Ports|Discovering Open TCP Ports]]

# Introduction to Nmap
`Enumeration` is identifying all the ways to attack a target. It's not hard to get access to the target system once we know how to do it. Most of the ways we can get access, we can narrow down to the following two points:
- `Functions and/or resources that allow us to interact with the target and/or provide additional information.`
- `Information that provides us with even more important information to access our target.`
## Uses
- Audit the security aspects of networks
- Simulate penetration tests
- Check firewall and IDS settings and configurations
- Types of possible connections
- Network mapping
- Response analysis
- Identify open ports
- Vulnerability assessment as well.
## Nmap Architecture
- Host discovery
- Port scanning
- Service enumeration and detection
- OS detection
- Scriptable interaction with the target service (Nmap Scripting Engine)
## Syntax
`nmap <scan types> <options> <target>` 
# Host Discovery
When conducting an internal penetration test, we should first get an overview of which systems are online that we can work with. We can use the various Nmap host discovery options. There are many options but the most effective is to use `ICMP echo requests`. This is a ping utility for the Internet Control Message Protocol. It is always recommended to store every scan.
## Scan Network range
`sudo nmap -sn -oA tnet 10.129.2.0/24 | grep for | cut -d" " -f5`

| Scanning options | Description                        |
| ---------------- | ---------------------------------- |
| 10.129.2.0/24    | Target network range.              |
| -sn              | Ping scan, disables port scanning. |
| -oA tnet         | Outputs results in tnet format.    |
This scanning method works only if the firewalls of the hosts allow it.
###### Note: 
`/` in the IP address signifies a subnet mask. In this case it tells us that the first 24 bits of the address are used for network designation, while the last 8 are used for the various hosts inside that network. Data packet is first sent to the network at `10.129.2`. Upon arrival at the network, the router will consult a routing table and then send it to the right host address `0`.
## Scan an IP list
We are given a list of IP addresses, `hosts.lst`. To scan the list we can include the `-iL` option into the previous Nmap command. `-iL hosts.lst` will scan the IP addresses on the list. Hosts that are not active when scanned using this method may be due to the hosts ignoring the ICMP echo requests because of their firewall configurations so a different method will be needed.
## Scan Multiple IPs
Instead of using a list of IPs, we could just include multiple targets in the original Nmap command.
Using `<IP1> <IP2> <IP3>` as the target. We can use as many targets as we want, however it is preferable to use fewer. For a larger number of targets using an IP list is preferable.
If the IPs are next to each other we can define a range in the respective octet.
`10.129.2.18-20` for example has the IPS starting with `10.129.2` and ending in `18`,`19` and `20`. Ranges are inclusive.
## Scan Single IP
Before scanning a host for open ports and its services, we first check if its alive or not. We can use same method as before. 
`sudo nmap 10.129.2.18 -sn -oA host `
However we change the output option to output the results in the host format. `-oA host` instead of `-oA tnet`. 

By disabling port scan(`-sn`), Nmap automatically will ping scan with ICMP echo requests. To ensure ICMP echo requests are sent we can use the `-PE` option. We can also use the `--reason` to display the reason for a specific result. Using `--packet-trace` we can show all packets sent and received to inspect more closely. A common result happens when before the ICMP echo request can be sent, an ARP request is sent and a reply is received, thus Nmap determines the IP as alive. We can disable ARP requests using `--disable-arp-ping`. 

From a scan like this, we can determine the operating system the IP belongs to by looking at the Time To Live (ttl) value. Different OS have different initial values. Most commonly:

| OS                               | TTL value |
| -------------------------------- | --------- |
| Linux/Mac OS                     | 64        |
| Windows                          | 128       |
| Network devices <br>like routers | 255       |
# Host and Port Scanning
There are a total of 6 different states for a scanned port:

| State            | Description                                                                                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| open             | This indicates that the connection to the scanned port has been established. These connections can be **TCP connections**, **UDP datagrams** as well as **SCTP associations**.                          |
| closed           | When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an `RST` flag. This scanning method can also be used to determine if our target is alive or not. |
| filtered         | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.                  |
| unfiltered       | This state of a port only occurs during the **TCP-ACK** scan and means that the port is accessible, but it cannot be determined whether it is open or closed.                                           |
| open\|filtered   | If we do not get a response for a specific port, `Nmap` will set it to that state. This indicates that a firewall or packet filter may protect the port.                                                |
| closed\|filtered | This state only occurs in the **IP ID idle** scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.                                           |
## Discovering Open TCP Ports
