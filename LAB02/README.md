# Report 1
___
## 1. How many interfaces does the host have? List all the interfaces found, give their names, and explain their functions briefly.
## 2. What are the MTUs of the interfaces on h0?
* Total Interfaces Found: 4

Active Interfaces:

| Interface | IP Address     | Netmask       | MTU   | Status | Function                     |
|-----------|----------------|---------------|-------|--------|------------------------------|
| eth0      | 10.177.0.67    | 255.255.0.0   | 1500  | UP     | Primary Ethernet (Class B)   |
| eth1      | 128.238.66.100 | 255.255.255.0 | 1500  | UP     | Secondary Ethernet (Class C) |
| lo        | 127.0.0.1      | 255.0.0.0     | 65536 | UP     | Loopback (internal comms)    |

Inactive Interfaces:

| Interface | MTU   | Status | Function                     |
|-----------|-------|--------|------------------------------|
| docker0   | 1500  | DOWN   | Docker virtual bridge         |

## 3. Is network subnetted? What is the reasoning for your answer? What the experimental are the reasons for subnetting?
* Yes, based on Different Network Classes:
  - eth0: 10.177.0.67/16 (Class B private range)
  - eth1: 128.238.66.100/24 (Class C public range)
  
| Reason                 | Technical Benefit                 |
|------------------------|-----------------------------------|
|  **Traffic Isolation** | Reduces broadcast domains         |
| **Security**           | Limits attack surface             |
| **Address Efficiency** | Optimizes IP usage               |
| **Protocol Testing**   | Enables parallel experiments     |
| **QoS Management**     | Prioritizes critical traffic     |

# Report 2
___
## 1. From the ping output, is the 127.0.0.1 interface on? Can you see any ICMP message sent from your host in the tcpdump output? Why?
* Successful ping replies confirm the interface is working
* ICMP messages are visible in tcpdump 
* When we execute ping 127.0.0.1, the kernel creates real ICMP packets (Echo Request)

# Report 3
___
## 1. Calculate the average collision rate over all the hosts for the set of statistics you collected in this exercise.
* Total collisions = 0 (eth0 on host 1) + 0 (eth1 on host 1) + 0 (eth0 on host 2) + 0 (eth1 on host 2) = 0
* Total number of interfaces = 4
* Average collision rate = Total collisions / Total number of interfaces = 0 / 4 = 0

# Report 4
___
## 1. From the saved wireshark output, explain how ARP operates. Draw the format of a captured, ARP request and reply including each field and the value. Your report should include the answers for the following questions.
* What is the target IP address in the ARP request?
  * 128.238.66.107
* At the MAC layer, what is the destination Ethernet address of the frame carrying the ARP request?
  * Broadcast (ff:ff:ff:ff:ff:ff)
* What is the frame type field in the Ethernet frame?
  * ARP (0x0806)
*  Who sends the ARP reply?
  * Owner of the target IP

* ***ARP table before ping:***
  - ? (128.238.66.103) at 50:00:00:e1:00:01 [ether] on eth1
  - ? (128.238.66.102) at 50:00:00:db:00:01 [ether] on eth1
  - ? (128.238.66.106) at 50:00:00:dd:00:01 [ether] on eth1
  - ? (128.238.66.100) at 50:00:00:09:00:01 [ether] on eth1
  - ? (128.238.66.105) at 50:00:00:de:00:01 [ether] on eth1
  - ? (128.238.66.104) at 50:00:00:df:00:01 [ether] on eth1

* ***ARP table after ping:***
  - ? (128.238.66.103) at 50:00:00:e1:00:01 [ether] on eth1
  - ? (128.238.66.102) at 50:00:00:db:00:01 [ether] on eth1
  - ? (128.238.66.107) at 50:00:00:dc:00:01 [ether] on eth1
  - ? (128.238.66.106) at 50:00:00:dd:00:01 [ether] on eth1
  - ? (128.238.66.100) at 50:00:00:09:00:01 [ether] on eth1
  - ? (128.238.66.105) at 50:00:00:de:00:01 [ether] on eth1
  - ? (128.238.66.104) at 50:00:00:df:00:01 [ether] on eth1
  
# Report 5
___
## 1. From the saved wireshark output, describe how the ARP timeout and retransmission were performed. How many attempts were made to resolve a non-existing IP address?
* Total Attempts: 6
* Delays increase between retries
* After retries, the sender marks the entry as \<incomplete> (visible in arp -a)

# Report 8????
___
## 1. What ICMP messages are used by ping ?
| ICMP Message            | Type | Code | Direction           | Purpose                                                                 |
|-------------------------|------|------|---------------------|-------------------------------------------------------------------------|
| **Echo Request**        | 8    | 0    | Source → Destination | Probe if the target host is reachable ("Are you there?").               |
| **Echo Reply**          | 0    | 0    | Destination → Source | Confirm reachability ("I’m here!").                                     |
| **Destination Unreachable** | 3    | *    | Router → Source      | Indicates failure (e.g., host down, no route, port blocked).            |
| **Time Exceeded**       | 11   | 0    | Router → Source      | TTL expired (used in `traceroute`; rare in `ping` unless TTL is very low). |

**Notes:**
- **Type 3 Codes**: `0` (Net Unreachable), `1` (Host Unreachable), `2` (Protocol Unreachable), etc.
- **Silent Drops**: No reply if firewalls block ICMP or host is offline (only Echo Requests appear).  