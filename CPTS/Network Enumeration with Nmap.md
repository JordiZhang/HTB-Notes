- [[#Introduction to Nmap|Introduction to Nmap]]
	- [[#Introduction to Nmap#Uses|Uses]]
	- [[#Introduction to Nmap#Nmap Architecture|Nmap Architecture]]
	- [[#Introduction to Nmap#Syntax|Syntax]]
- [[#Host Discovery|Host Discovery]]
	- [[#Host Discovery#Scan Network range|Scan Network range]]
	- [[#Host Discovery#Scan an IP list|Scan an IP list]]
	- [[#Host Discovery#Scan Multiple IPs|Scan Multiple IPs]]
	- [[#Host Discovery#Scan Single IP|Scan Single IP]]
- [[#Host and Port Scanning|Host and Port Scanning]]
	- [[#Host and Port Scanning#Discovering Open TCP Ports|Discovering Open TCP Ports]]
		- [[#Discovering Open TCP Ports#TCP Connect Scan|TCP Connect Scan]]
		- [[#Discovering Open TCP Ports#Filtered Ports|Filtered Ports]]
	- [[#Host and Port Scanning#Discovering Open UDP Ports|Discovering Open UDP Ports]]
- [[#Saving the Results|Saving the Results]]
	- [[#Saving the Results#Different Formats|Different Formats]]
- [[#Service Enumeration|Service Enumeration]]
	- [[#Service Enumeration#Service Version Detection|Service Version Detection]]
	- [[#Service Enumeration#Banner Grabbing|Banner Grabbing]]
- [[#Nmap Scripting Engine|Nmap Scripting Engine]]
- [[#Performance|Performance]]
	- [[#Performance#Timeouts|Timeouts]]
	- [[#Performance#Max Retries|Max Retries]]
	- [[#Performance#Rates|Rates]]
	- [[#Performance#Timing|Timing]]
- [[#Firewall and IDS/IPS Evasion|Firewall and IDS/IPS Evasion]]
	- [[#Firewall and IDS/IPS Evasion#Firewalls|Firewalls]]
	- [[#Firewall and IDS/IPS Evasion#IDS/IPS|IDS/IPS]]
	- [[#Firewall and IDS/IPS Evasion#Determine Firewalls and Their Rules|Determine Firewalls and Their Rules]]
	- [[#Firewall and IDS/IPS Evasion#Detect IDS/IPS|Detect IDS/IPS]]
	- [[#Firewall and IDS/IPS Evasion#Decoys|Decoys]]
	- [[#Firewall and IDS/IPS Evasion#DNS Proxying|DNS Proxying]]
- [[#Easy Lab|Easy Lab]]
- [[#Medium Lab|Medium Lab]]
- [[#Hard Lab|Hard Lab]]

# Introduction to Nmap
---

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
| -oA tnet         | Outputs results in all formats.    |
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
By default, `Nmap` scans the top 1000 TCP ports with the SYN scan (`-sS`). However, this is only the default when we run Nmap as root because it requires socket permissions to create the TCP packets. Otherwise, the TCP scan (`-sT`) is performed by default. 

When scanning ports, we can define ports one by one (`-p 22,25,30,46`), by range (`-p 22-250`), by top ports (`--top-ports=10`) from the Nmap database that have been signed as most frequent, by scanning all ports (`-p-`) or by defining a fast port scan (`-F`), which contains the top 100 ports.

When doing a SYN scan, to have a clear view of it, we disable ICMP echo requests (`-Pn`), DNS resolution (`-n`) and ARP ping scan (`--disable-ARP-ping`).
### TCP Connect Scan
The Nmap TCP connect scan (`-sT`) uses the TCP three-way handshake to determine if a port on target host is open or closed. The scan sends a `SYN` packet to the target and waits for a response. If the target responds with a `SYN-ACK` packet, it is considered open. If the target responds with a `RST` packet then it is considered closed. 

The connect scan is useful because it is the most accurate way to determine the state of a port and it is also the stealthiest. This is because the connect scan does not leave any unfinished connections or unsent packets on the target host, which makes it less likely to be detected. It is useful when we want to map a network without disturbing the services. It is also useful when the target host has a personal firewall that drops incoming packets but allows outgoing packets. In this case, a Connect scan can bypass the firewall and accurately determine the state of the target ports.
### Filtered Ports
When a port is shown as filtered, it is most likely due to a firewall's rule set to handle specific connections. The packets can be either dropped or rejected. When using a TCP scan we get two distinctions.
`Dropped`: When tracking the packets for a filtered port like this, we can see 2 sent TCP packets but no received ones. We see 2 because by default Nmap has a `--max-retries` option of 1. We can also see that it takes much longer for the scan. 
`Rejected`: When tracking the packets for a filtered port like this, we can see a sent TCP packet and a ICMP received packet. We can see that the reply packet has a `type 3` and error `code 3`, which indicates that the port is unreachable. If we know that the host is alive, then we can strongly assume that the firewall is rejecting the packets.
## Discovering Open UDP Ports
User Datagram Protocol (`UDP`) is a stateless protocol and does not require a three-way handshake like TCP. In this way, we do not receive any acknowledgement. Consequently, the timeout is much longer, making the whole UDP scan (`-sU`) much slower than a TCP scan. Another disadvantage of UDP is that we often do not get a response back. This is because Nmap sends empty datagrams to the scanned UDP ports, and these do not receive a response. So we cannot determine if the UDP packet has arrived or not. If the UDP port is open, we only receive a response if the application is configured to do so.
Example: `sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason `
# Saving the Results
## Different Formats
Nmap can save the results in 3 different formats:
- Normal output (`-oN`) with the `.nmap` file extension
- Grepable output (`-oG`) with the `.gnmap` file extension
- XML output (`-oX`) with the `.xml` file extension
Using the `-oA` will save the results in all the formats.
`-oA <file_name>` will save the results in all the formats, starting the name of each file with `<file_name>`. If no full path is given, the results will be stored in the current directory.
With the XML output, we can easily create HTML reports using the tool `xsltproc`:
`xsltproc target.xml -0 target.html`. This results in an easily readable report.
# Service Enumeration
## Service Version Detection
It is recommended to perform a quick port scan first, which gives us a small overview of the available ports. This causes significantly less traffic, which is advantageous for us because otherwise we can be discovered and blocked by the security mechanisms. We can deal with these first and run a port scan in the background, which shows all open ports (`-p-`). We can use the version scan to scan the specific ports for services and their versions (`-sV`). To view the scan status, we can press the `[Space Bar]` during the scan. Another option (`--stats-every=5s`) that we can use is defining how periods of time the status should be shown. We can also increase the `verbosity level` (`-v` / `-vv`).
## Banner Grabbing
Primarily, `Nmap` looks at the banners of the scanned ports and prints them out. If it cannot identify versions through the banners, `Nmap` attempts to identify them through a signature-based matching system, but this significantly increases the scan's duration. One disadvantage to `Nmap`'s presented results is that the automatic scan can miss some information because sometimes `Nmap` does not know how to handle it. 

It can happen because, after a successful three-way handshake, the server often sends a banner for identification. This serves to let the client know which service it is working with. At the network level, this happens with a `PSH` flag in the TCP header. However, it can happen that some services do not immediately provide such information. It is also possible to remove or manipulate the banners from the respective services. If we `manually` connect to the SMTP server using `nc`, grab the banner, and intercept the network traffic using `tcpdump`, we can see what `Nmap` did not show us.
`nc -nv <ip> <port>`
# Nmap Scripting Engine
Nmap Scripting Engine (`NSE`) is another handy feature of `Nmap`. There are a total of 14 categories into which these scripts can be divided:

| Category  | Description                                                                                                                                   |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| auth      | Determination of authentication credentials.                                                                                                  |
| broadcast | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans.       |
| brute     | Executes scripts that try to log in to the respective service by brute-forcing with credentials.                                              |
| default   | Default scripts executed by using the `-sC` option.                                                                                           |
| discovery | Evaluation of accessible services.                                                                                                            |
| dos       | These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services. Denial of Service. |
| exploit   | This category of scripts tries to exploit known vulnerabilities for the scanned port.                                                         |
| external  | Scripts that use external services for further processing.                                                                                    |
| fuzzer    | This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.           |
| intrusive | Intrusive scripts that could negatively affect the target system.                                                                             |
| malware   | Checks if some malware infects the target system.                                                                                             |
| safe      | Defensive scripts that do not perform intrusive and destructive access.                                                                       |
| version   | Extension for service detection.                                                                                                              |
| vuln      | Identification of specific vulnerabilities.                                                                                                   |
https://nmap.org/nsedoc/index.html
# Performance
Scanning performance plays a significant role when we need to scan an extensive network or are dealing with low network bandwidth. We can use various options to tell `Nmap` how fast (`-T <0-5>`), with which frequency (`--min-parallelism <number>`), which timeouts (`--max-rtt-timeout <time>`) the test packets should have, how many packets should be sent simultaneously (`--min-rate <number>`), and with the number of retries (`--max-retries <number>`) for the scanned ports the targets should be scanned.
## Timeouts
When Nmap sends a packet, it takes some time (`Round-Trip-Time` - `RTT`) to receive a response from the scanned port. Generally, `Nmap` starts with a high timeout (`--min-RTT-timeout`) of 100ms. If the timeout is set too short, it can cause Nmap to miss some ports.
## Max Retries
Another way to increase the scans' speed is to specify the retry rate of the sent packets (`--max-retries`). The default value for the retry rate is `10`, we can reduce this. When all retries are used up, if Nmap doesn't receive a response for a port, it will not send any more packets to the port and will be skipped.
## Rates
During a white-box penetration test, we may get whitelisted for the security systems to check the systems in the network for vulnerabilities and not only test the protection measures. If we know the network bandwidth, we can work with the rate of packets sent, which significantly speeds up our scans with `Nmap`. When setting the minimum rate (`--min-rate <number>`) for sending packets, we tell `Nmap` to simultaneously send the specified number of packets. It will attempt to maintain the rate accordingly.
## Timing
Because such settings cannot always be optimized manually, as in a black-box penetration test, `Nmap` offers six different timing templates (`-T <0-5>`) for us to use. These values (`0-5`) determine the aggressiveness of our scans. This can also have negative effects if the scan is too aggressive, and security systems may block us due to the produced network traffic. The default timing template used when we have defined nothing else is the normal (`-T 3`).
- `-T 0` / `-T paranoid`
- `-T 1` / `-T sneaky`
- `-T 2` / `-T polite`
- `-T 3` / `-T normal`
- `-T 4` / `-T aggressive`
- `-T 5` / `-T insane`
These templates contain options that we can also set manually, and have seen some of them already. The developers determined the values set for these templates according to their best results, making it easier for us to adapt our scans to the corresponding network environment. The exact used options with their values we can find here: [https://nmap.org/book/performance-timing-templates.html](https://nmap.org/book/performance-timing-templates.html)
# Firewall and IDS/IPS Evasion
`Nmap` gives us many different ways to bypass firewalls rules and IDS/IPS. These methods include the fragmentation of packets, the use of decoys, and others that we will discuss in this section.
## Firewalls
A firewall is a security measure against unauthorized connection attempts from external networks. Every firewall security system is based on a software component that monitors network traffic between the firewall and incoming data connections and decides how to handle the connection based on the rules that have been set. It checks whether individual network packets are being passed, ignored, or blocked. This mechanism is designed to prevent unwanted connections that could be potentially dangerous.
## IDS/IPS
Like the firewall, the intrusion detection system (`IDS`) and intrusion prevention system (`IPS`) are also software-based components. `IDS` scans the network for potential attacks, analyzes them, and reports any detected attacks. `IPS` complements `IDS` by taking specific defensive measures if a potential attack should have been detected. The analysis of such attacks is based on pattern matching and signatures. If specific patterns are detected, such as a service detection scan, `IPS`
may prevent the pending connection attempts.
## Determine Firewalls and Their Rules

We already know that when a port is shown as filtered, it can have several reasons. In most cases, firewalls have certain rules set to handle specific connections. The packets can either be `dropped`, or `rejected`. The `dropped` packets are ignored, and no response is returned from the host.

This is different for `rejected` packets that are returned with an `RST` flag. These packets contain different types of ICMP error codes or contain nothing at all. Such errors can be:
- Net Unreachable
- Net Prohibited
- Host Unreachable
- Host Prohibited
- Port Unreachable
- Proto Unreachable
Nmap's TCP ACK scan (`-sA`) method is much harder to filter for firewalls and IDS/IPS systems than regular SYN (`-sS`) or Connect scans (`sT`) because they only send a TCP packet with only the `ACK`
flag. When a port is closed or open, the host must respond with an `RST` flag. Unlike outgoing connections, all connection attempts (with the `SYN` flag) from external networks are usually blocked by firewalls. However, the packets with the `ACK` flag are often passed by the firewall because the firewall cannot determine whether the connection was first established from the external network or the internal network. Example ACK scan:
`sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace`
## Detect IDS/IPS
Unlike firewalls and their rules, the detection of IDS/IPS systems is much more difficult because these are passive traffic monitoring systems. `IDS systems` examine all connections between hosts. If the IDS finds packets containing the defined contents or specifications, the administrator is notified and takes appropriate action in the worst case. `IPS systems` take measures configured by the administrator independently to prevent potential attacks automatically. It is essential to know that IDS and IPS are different applications and that IPS serves as a complement to IDS.

Several virtual private servers (`VPS`) with different IP addresses are recommended to determine whether such systems are on the target network during a penetration test. If the administrator detects such a potential attack on the target network, the first step is to block the IP address from which the potential attack comes. As a result, we will no longer be able to access the network using that IP address, and our Internet Service Provider (`ISP`) will be contacted and blocked from all access to the Internet.
- `IDS systems` alone are usually there to help administrators detect potential attacks on their network. They can then decide how to handle such connections. We can trigger certain security measures from an administrator, for example, by aggressively scanning a single port and its service. Based on whether specific security measures are taken, we can detect if the network has some monitoring applications or not.
- One method to determine whether such `IPS system` is present in the target network is to scan from a single host (`VPS`). If at any time this host is blocked and has no access to the target network, we know that the administrator has taken some security measures. Accordingly, we can continue our penetration test with another `VPS`.

Consequently, we know that we need to be quieter with our scans and, in the best case, disguise all interactions with the target network and its services.
## Decoys
There are cases in which administrators block specific subnets from different regions in principle. This prevents any access to the target network. Another example is when IPS should block us. For this reason, the Decoy scanning method (`-D`) is the right choice. With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent. With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses. In the next example, our real IP address is therefore placed in the second position. Another critical point is that the decoys must be alive. Otherwise, the service on the target may be unreachable due to SYN-flooding security mechanisms. Example decoy scan:
`sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5`

The spoofed packets are often filtered out by ISPs and routers, even though they come from the same network range. Therefore, we can also specify our VPS servers' IP addresses and use them in combination with "`IP ID`" manipulation in the IP headers to scan the target.

Another scenario would be that only individual subnets would not have access to the server's specific services. So we can also manually specify the source IP address (`-S`) to test if we get better results with this one. Decoys can be used for SYN, ACK, ICMP scans, and OS detection scans. So let us look at such an example and determine which operating system it is most likely to be.
`sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0`
`-e tun0` sends all requests through the specified (`tun0`) interface.
## DNS Proxying
By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target. These DNS queries are also passed in most cases because the given web server is supposed to be found and visited. The DNS queries are made over the `UDP port 53`. The `TCP port 53` was previously only used for the so-called "`Zone transfers`" between the DNS servers or data transfer larger than 512 bytes. More and more, this is changing due to IPv6 and DNSSEC expansions. These changes cause many DNS requests to be made via TCP port 53.

However, `Nmap` still gives us a way to specify DNS servers ourselves (`--dns-server <ns>,<ns>`). This method could be fundamental to us if we are in a demilitarized zone (`DMZ`). The company's DNS servers are usually more trusted than those from the Internet. So, for example, we could use them to interact with the hosts of the internal network. As another example, we can use `TCP port 53` as a source port (`--source-port`) for our scans. If the administrator uses the firewall to control this port and does not filter IDS/IPS properly, our TCP packets will be trusted and passed through.
`sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53`
Now that we have found out that the firewall accepts `TCP port 53`, it is very likely that IDS/IPS filters might also be configured much weaker than others. We can test this by trying to connect to this port by using `Netcat`.
`ncat -nv --source-port 53 10.129.2.28 50000`
# Easy Lab
Asks us to find the operating system of the target. I did a fast TCP scan to find open ports and their services and then ran a version scan of said services. In the version, it told me for what OS the service was for and it was for ubuntu.
# Medium Lab
Asks us to find the DNS server version of the target. First instinct is to use the `-sV` option to find the version of the DNS port, that being port 53. The port shows up as filtered. I try to run a UDP scan. This shows the port as open. Therefore we use the UDP scan to do a version scan.
`nmap 10.129.152.147 -p 53 -sU -sV`
# Hard Lab
I am tasked to identify the version of the running services. A TDP scan shows 2 open ports, ssh and http. I found the versions of those two using `-sV` option and tried them as answers, wrong. I assume then that one of the filtered ports contains the service we are looking for. I try using the DNS port (53) as the source port and run a SYN scan (`-sS`). This results in a new service called ibm-db2. Since the firewall accepts TCP port 53, it is very likely that the IDS/IPS filters might also be configured in the same way, weaker than others. So we try using Netcat with source port 53 to check the version of the service. This results in the right answer.