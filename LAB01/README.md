> ## Network Configuration
>| Command | Description |
>|---------|-------------|
>| `ip address add 128.238.66.100 dev eth1` | Assigns IP 128.238.66.100 to interface eth1 (modern method) |
>| `ifconfig -a` | Displays all network interfaces (legacy) |
>| `ifconfig eth1 192.168.0.1 netmask 255.255.255.0` | Sets IP 192.168.0.1 on eth1 (legacy method) |

>## Process Management
>| Command | Description |
>|---------|-------------|
>| `ps -e` | Lists all running processes |
>| `telnet &` | Runs telnet in background |
>| `ps -e \| grep telnet` | Finds telnet process in running processes |
>| `kill <PID>` | Terminates process with specified Process ID |
>| `fg` | Brings background process to foreground |
> 1. **Background/foreground**:
>     - Append `&` to run command in background (e.g., `telnet &`)
>     - Use `fg` to return to foreground
> 2. **Process control**:
>     - `ps -e` → View all processes
>     - `grep` → Filter output (e.g., `ps -e | grep telnet`)
>     - `kill` → Stop process by PID

> ## Telnet Service Commands
>| Command | Description |
>|---------|-------------|
>| `telnet example.com 80` | Connects to example.com on port 80 |
>| `telnet &` → `fg` | Runs telnet in background, then brings to foreground |

# Report 1
___
## 1. What is Internet Service Daemon (inetd)?
- **Definition**:
    - `inetd` (Internet Service Daemon) is a legacy **super-server** that manages incoming network connections for multiple services.
- **Function**:
    - Listens on multiple ports.
    - Spawns the appropriate service (e.g., `telnet`, `ftp`) only when a connection is requested.
    - Reduces system load by avoiding dedicated processes for rarely used services.
- **Replaced by**: Modern systems often use `xinetd` (eXtended inetd) or direct socket activation (systemd).

## 2. Is inetd Started in Your System? Why?

* ***Check Command:***
```bash
ps -e | grep inetd
```
* ***Result:***
```bash
42 ?        00:00:00 xinetd # This means inetd is not running
```
* ***Why inetd is Not Running:***
Deprecated - Replaced by `xinetd` (more secure) and `systemd`
Security Risks - No access controls or rate limiting
Performance - Newer solutions are more efficient

## 3. Is xinetd Started in Your System? What is its PID?

* ***Check Command:***
```bash
ps -e | grep xinetd
```
* ***Result:***
```bash
42 ?        00:00:00 xinetd # This means inetd is not running
```
* `xinetd` is running
* ***PID***: 42 

> ## Default Network Services Commands
>| Command | Action | Explanation | Expected Output |
>|---------|--------|-------------|-----------------|
>| **View services file** |
>| `more /etc/services` | Display network services | Shows service names with ports/protocols (paged output) | Paged terminal output |
>| **Create redirected copy** |
>| `more /etc/services > ser-more` | Save formatted output to file | Creates `ser-more` with terminal-formatted content | (No output - creates file) |
>| **Create direct copy** |
>| `cp /etc/services ser-cp` | Make exact file copy | Creates identical binary copy `ser-cp` | (No output if successful) |
>| **Compare files** |
>| `cmp ser-cp ser-more` | Compare file contents | Checks if files are byte-identical | No output = identical<br>Shows first difference if any |
>| **Combine files** |
>| `cat ser-cp ser-more > ser-cat` | Concatenate files | Merges both files into `ser-cat` | (No output - creates file) |
>| **Check file sizes** |
>| `ls -l ser*` | List file details | Shows sizes/dates of all `ser-*` files | Size listings for all files |

# Report 2
___
## 1. What are the sizes of files ser-more, ser-cp, and ser-cat?
* `ser-more` 18774
* `ser-cp` 18774
* `ser-cat` 37548

# Report 3
___
## 1. Explain the above commands briefly. (Two or three sentences per command would be adequate.)
| Command | Purpose | Key Options |
|---------|---------|-------------|
| **arp** | View/manipulate ARP cache | `-a` (show all), `-d` (delete entry), `-s` (add static) |
| **arping** | Send ARP requests to verify host reachability | `-I` (interface), `-c` (count), `-D` (duplicate detection) |
| **ifconfig** | Configure network interfaces *(legacy)* | `up/down`, `inet` (set IP), `netmask`, `mtu` |
| **tcpdump** | Capture network traffic | `-i` (interface), `-n` (no DNS), `-w` (save to file), `port`/`host` filters |
| **ping** | Test host connectivity via ICMP | `-c` (count), `-i` (interval), `-s` (packet size), `-t` (TTL) |
| **netstat** | Display network connections/routing tables *(legacy)* | `-t` (TCP), `-u` (UDP), `-l` (listening), `-r` (route) |
| **route** | View/modify IP routing table *(legacy)* | `add/del`, `-n` (numeric), `netmask`, `gw` (gateway) |
| **wireshark** | GUI packet analyzer | Filters: `tcp.port`, `ip.addr`, `arp`, `-k` (start immediately) |
| **iptables** | Configure firewall rules | `-A` (append rule), `-j` (target: ACCEPT/DROP), `-p` (protocol), `--dport` |

* ***Examples:***
```bash
# Test connectivity
ping -c 4 google.com

# Capture HTTP traffic
tcpdump -i eth0 port 80 -w capture.pcap

# List listening TCP ports
netstat -tuln
```

# Report 4
___
## 1. What is the value of the protocol field in the IP header of the packet you saved? What is the use of the protocol field?
* TCP (6)
* Next-header demultiplexing

## 2. What is the value of the frame type field in an Ethernet frame carrying an IP datagram?
*  IPv4 (0x0800)

# Report 5
___
## 1. What is the value of the frame type field in an Ethernet frame carrying an ARP request and in an Ethernet frame carrying an ARP reply, respectively?
* Both ARP requests and replies use 0x0806

## 2. What is the use of the frame type field?
Identifies the network protocol encapsulated in the Ethernet frame
Enables proper protocol demultiplexing by the receiver
Common values:
0x0800 = IPv4
0x0806 = ARP
0x86DD = IPv6   

# Report 6
___
## 1. Explain briefly the purposes of the following tcpdump expressions. If you are using wireshark explain the following filters:
| Filter Expression | Purpose | Protocol/Port Targeted |
|-------------------|---------|------------------------|
| `udp.port == 520` | Displays UDP packets using port 520 (RIP) | UDP/520 |
| `ip.proto == 89` | Shows OSPF protocol packets (IP protocol 89) | OSPF (IP 89) |
| `ip.addr == ip-addr1 and (ip.addr == ip-addr2 or ip.addr == ip-addr3)` | Filters traffic involving ip-addr1 AND either ip-addr2 OR ip-addr3 | Any IP traffic |
| `ip.addr == ip-addr1 and not ip.addr == ip-addr2` | Shows traffic involving ip-addr1 BUT NOT ip-addr2 | Any IP traffic |

* ***Common Protocol Number:***
- 89: OSPF

# Report 7
___
## 1. What are the port numbers used by the h1 (local machine) and the h2 (remote machine)?
## 2. Which port numbers matches the port number listed for telnet in the /etc/services file?
| Component      | Port Number | How to Verify               |
|----------------|-------------|-----------------------------|
| h1 (local)     | 40286       | Wireshark Source Port       |
| h2 (remote)    | 23          | Wireshark Destination Port  |
| Telnet Service | 23          | `grep telnet /etc/services` |
* Remote port (23) matches /etc/services Telnet entry

# Report 8
___
## 1. When you have two telnet sessions with your machine, what port number is used on the h2 (remote machine)?
* 23

## 2. Are both sessions connected to the same port number on the h2 (remote machine)?
* Yes, both use port 23

## 3. What port numbers are used in the h1 (local machine) for the first and second telnet , respectively?
* 43446 and 46910

## 4. Explain briefly what a socket is.
* Endpoint pair: (IP + Port) combination identifying a connection
