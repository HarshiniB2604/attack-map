# Notes

## Friday: how the internet works

### 1. What is an IP address, and who hands them out?

An IP address is the address that identifies a device on a network. There are two types: **public** (the one address the internet sees, which belongs to the router) and **private** (the addresses the router gives each device inside the home network). The router swaps private addresses for its public one using **NAT**.

A packet carries the destination IP address, but it doesn't know the whole route. Each router it reaches reads the address and decides the next step.

IP addresses are handed out in a chain: **IANA → 5 Regional Internet Registries** (APNIC for India) **→ ISPs** (like Jio or Airtel) **→ my router** (public IP) **→ my devices** (private IPs, given by the router).

### 2. What is a packet, and why does data get split into them?

A packet is a small chunk of a message or request, sent from one device to another. Routers pass it along the way. Each packet has a **header** (from-address, to-address, sequence number) and a **payload** (the actual data).

Data is split into packets because:
- the data is too big to send in one piece
- if one packet is lost, only that piece needs to be resent, not everything
- many people's packets can share the same wires at the same time

The **sequence number** lets the receiver put the packets back in order and spot missing ones.

### 3. What is a port? Why does SSH use 22 and HTTPS use 443?

A port leads the packet to the right application: the IP leads to the device, and the port leads to the application or server running on it. A program waiting on a port is **listening**. Ports go from 0 to 65535.

SSH uses 22 because its inventor picked it in 1995: it sits between **FTP (21)** and **Telnet (23)**, older services with no encryption that SSH was built to replace. HTTPS got 443 because that number was free and was registered for it.

These numbers are a **convention registered with IANA**. Everyone agrees on them, so a browser knows to knock on 443 without being told.
