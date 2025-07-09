# K8S - Networking:

## Linux Networking: Switching, Routing and Gateway:

### What is network switching ?

Switching is a process that occurs at the data link layer (Layer 2) of the OSI model. In the context of a local area network (LAN), a switch acts as a central point that connects multiple devices, such as computers, servers, and printers. It forwards network packets between devices within the same network (or broadcast domain) based on their MAC (Media Access Control) addresses. Switches are responsible for efficiently and accurately delivering packets to their intended destinations within the same LAN.

For example, let’s say you have a small office LAN with multiple computers connected to a switch. When computer A wants to send a network packet to computer C, it sends the packet to the switch. The switch examines the MAC address of the packet, determines which port computer C is connected to, and forwards the packet only to that port. This allows for efficient communication between devices within the same LAN without flooding unnecessary packets to all devices.


<p align="center">
  <img src="images/k8s-71.JPG" alt="Description of my awesome image" width="600">
</p>

### Important network commands:

1. Display the configuration of all network interfaces
```
$ ip link show
```
2. View the IP addresses of all network interfaces
```
$ ip addr show
```
3. Add an IP address to a network interface
```
$ ip addr add <ip_address>/<subnet_mask> dev <network_interface>
```
**<ip_address>**: The IP address that you want to assign to the network interface.

**<subnet_mask>**: The subnet mask that defines the network's address range. It is expressed in CIDR (Classless Inter-Domain Routing) notation, where the number after the forward slash ("/") specifies the number of bits in the subnet mask. For example, "/24" corresponds to a subnet mask of 255.255.255.0, which means that the network has 256 IP addresses available (2^8 - 2).

**<network_interface>**: The name of the network interface (e.g., eth0, eth1, enp0s1, etc.) to which you want to assign the IP address.

### Network Route:

Routing is the process of directing network traffic from one network to another. It involves the use of routers, which are network devices that make decisions about how to forward data packets across different networks based on their IP addresses. Routers operate at the network layer (Layer 3) of the OSI model and use routing tables to determine the most efficient path for packet delivery.

For example, consider a scenario where a computer A in a local area network (LAN) wants to send data to a computer F in another LAN.

* computer A: IP address 192.168.1.1
* computer F: IP address 192.168.2.3
* switch1 with IP address 192.168.1.0
* switch2 with IP address 192.168.2.0
* router IP address to reach switch1: 192.168.1.5
* router IP address to reach switch2: 192.168.2.5

<p align="center">
  <img src="images/k8s-72.JPG" alt="Description of my awesome image" width="600">
</p>
<p align="center">
  <img src="images/k8s-73.JPG" alt="Description of my awesome image" width="600">
</p>

4. Configure the gateway for computer A:
```
$ ip route add 192.168.2.0/24 via 192.168.1.5 dev eth0
```

`This command adds a route to the 192.168.2.0/24 network via the IP address of the router to reach Switch1 (192.168.1.5) through the “eth0” network interface of computer A.`

5. Configure the gateway for computer F:
```
$ ip route add 192.168.1.0/24 via 192.168.2.5 dev eth0
```

`This command adds a route to the 192.168.1.0/24 network via the IP address of the router to reach Switch2 (192.168.2.5) through the “eth0” network interface of computer F.`

Now, computer A can communicate with computer F, and the packet will be routed through the routers connected to Switch1 and Switch2, enabling inter-network communication between the two computers.

<p align="center">
  <img src="images/k8s-74.JPG" alt="Description of my awesome image" width="600">
</p>
<p align="center">
  <img src="images/k8s-75.JPG" alt="Description of my awesome image" width="600">
</p>

6. Add a routing entry to the IP routing table. It allows you to define how network traffic should be directed for a specific destination network.

```
$ ip route add <destination_network_ip>/<subnet_mask> via <gateway_ip> dev <network_interface>
```

* <**destination_network_ip**>: This specifies the IP address of the destination network you want to add to the routing table.
* <**subnet_mask**>: This specifies the subnet mask for the destination network, which determines the range of IP addresses that belong to the network.
* <**gateway_ip**>: This specifies the IP address of the gateway (router) that should be used to reach the destination network.
* <**network_interface**>: This specifies the network interface through which the traffic should be routed to reach the destination network.

7. View the IP routing table
```
$ route
```

### Gateway

A gateway is a network device, often a router, that connects a local network (e.g., LAN or VLAN) to other networks such as the Internet. 
It acts as a “doorway” for traffic to flow in and out of the local network, enabling communication between devices in different networks.

### Default Gateway

A default gateway, also known as a default route, is a special type of gateway that is used in computer networking to provide a default path for network traffic that is destined for a network outside of the local network. 

* In other words, it is the IP address of the router that is used as the exit point for network traffic that does not have a specific route in the routing table.
* When a device, such as a computer or a network switch, needs to send data to a destination network that is not part of its local network, it checks its routing table to determine the appropriate path.
* If there is no specific route for that destination network, the device forwards the traffic to the default gateway. The default gateway then forwards the traffic to the appropriate destination network based on its own routing table.


**Note:** The default gateway is typically displayed as the destination “0.0.0.0” or deafult with a netmask of “0.0.0.0”.

## Linux Networking: DNS

- In Linux networking, hostname resolution can be done through either the local /etc/hosts file, which maps hostnames to IP addresses, or by using DNS, a distributed system for resolving hostnames. 
- Configure DNS servers and search domains in /etc/resolv.conf. We can customize the lookup order in which /etc/hosts and DNS are used for hostname resolution in /etc/nsswitch.conf.
- Domain names are hierarchical, with top-level domains at the highest level. 
- A search domain automatically appends a domain name to an unqualified hostname to simplify access to local network resources. 
- Record types like A, AAAA, and CNAME provide different information for DNS lookups.

**/etc/hosts file**

The /etc/hosts file on Linux and Unix-based systems maps hostnames to IP addresses. It is used by the system’s resolver library to resolve hostnames without consulting a Domain Name System (DNS) server. Each line contains an IP address followed by one or more hostnames separated by whitespace. The IP address and hostname mapping is entered manually by a system administrator.

```
$ cat /etc/hosts
# This is a sample hosts file used by Linux operating systems

# IP address    hostname
127.0.0.1       localhost
127.0.1.1       myhostname.localdomain  myhostname
192.168.1.100   myserver.example.com  myserver
```

<p align="center">
  <img src="images/k8s-76.JPG" alt="Description of my awesome image" width="600">
</p>
<p align="center">
  <img src="images/k8s-77.JPG" alt="Description of my awesome image" width="600">
</p>

While editing the /etc/hosts file is a simple way to map hostnames to IP addresses on a local system, it becomes impractical in larger networks where multiple systems need to access different hosts. Maintaining a large number of entries in the /etc/hosts file for each system in the network can be a tedious and error-prone task.

`In these cases, a centralized DNS server is often used to provide a more scalable and flexible solution for hostname resolution.`

**Domain Name System (DNS)**

Domain Name System(DNS) enables the translation of human-readable domain names into IP addresses that machines can use to communicate with each other.

<p align="center">
  <img src="images/k8s-78.JPG" alt="Description of my awesome image" width="600">
</p>

When a Linux system needs to resolve a domain name, it sends a query to a DNS server. The DNS server then responds with the IP address associated with the domain name. If the DNS server does not have a record for the domain name, it can forward the query to other DNS servers until it finds the correct IP address.

**Configure DNS servers**:

<p align="center">
  <img src="images/k8s-79.JPG" alt="Description of my awesome image" width="600">
</p>

To configure the DNS server in Linux, we can modify the /etc/resolv.conf file. This is a plain text file that contains the IP address of the DNS server that the system should use for hostname resolution. To add a DNS server, we simply need to insert a “nameserver” line followed by the IP address of the DNS server.

For instance, to use the Google Public DNS server with IP address 8.8.8.8, we can add the following line to /etc/resolv.conf:

```
nameserver 8.8.8.8
```
If we want to use multiple DNS servers, we can add additional “nameserver” lines, one for each DNS server, like this:
```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Once we save the changes to the /etc/resolv.conf file, the system will use the specified DNS server(s) for hostname resolution.

**Hostname-to-IP Resolution Order:**

The order of hostname-to-IP mapping lookup can be specified in the /etc/nsswitch.conf file. This file specifies the order of the lookup methods used by the system’s Name Service Switch (NSS) library.

By default, the file includes a line that specifies “hosts: files dns”.

```
# /etc/nsswitch.conf
hosts:          files dns
```

<p align="center">
  <img src="images/k8s-80.JPG" alt="Description of my awesome image" width="600">
</p>

The system first looks in the /etc/hosts file for hostname-to-IP mappings, and if the hostname is not found in the file, it queries a DNS server to resolve the hostname.

However, the order can be changed by modifying the “hosts” line in /etc/nsswitch.conf to “hosts: dns files”.

```
# /etc/nsswitch.conf
hosts:          dns files
```

`The system first queries the DNS server for hostname-to-IP mappings, and if the hostname is not found in the DNS server, it looks in the /etc/hosts file.`

**Domain Name**:

A domain name is a hierarchical label used to identify a network domain on the Internet. It consists of multiple levels, separated by dots, with the rightmost label representing the top-level domain (TLD), and the other labels representing subdomains.

For example, mail.yahoo.com is a domain name where com is the top-level domain, yahoo is the second-level domain and also the subdomain of com, and mail the third-level domain and a subdomain of yahoo.

<p align="center">
  <img src="images/k8s-81.JPG" alt="Description of my awesome image" width="600">
</p>

* Top-level domains (TLDs): These are the highest level domains in the hierarchical Domain Name System.
* Second-level domains (SLDs): These are the domains directly below the TLDs.
* Subdomains: These are domains that are part of a larger domain and can be created by the owner of the domain.





