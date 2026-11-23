# Glossary

Every technical term learned while building this project, in plain words, with where it shows up.

| Term | In plain words | Where it shows up in this project |
| --- | --- | --- |
| IP address | A number that identifies a device on a network, like a postal address for a computer (e.g. `142.250.183.14`) | Every arc on the globe starts at an attacker's IP address |
| Public IP | The one address the whole internet sees for your home or office network; it belongs to the router | The IPs AbuseIPDB reports are public IPs |
| Private IP | An address that only works inside a local network, like `192.168.1.5`; each device gets its own | Never appears in our data: the internet never sees private IPs |
| IPv4 | The original IP format: four numbers 0–255 separated by dots (`45.12.88.10`). Only ~4.3 billion exist, and they have run out | Most attacker IPs from AbuseIPDB; masked as `45.12.88.x` in Block 4 |
| IPv6 | The newer, much longer IP format (`2404:6800:4009::200e`) with enough addresses for every device to have its own | Some AbuseIPDB IPs are IPv6, so masking code must handle both formats |
| Firewall | Rules that decide which network traffic is allowed in or out; the real security tool (NAT only protects by accident) | Not in our code; relevant to the v2 honeypot idea |
| NAT (Network Address Translation) | The router swapping each device's private address for its single public address on the way out, and back again on the way in | Explains why one IP on the map can be many people, so it isn't the same as one person. This is why we mask IPs in Block 4 |
| Packet | A small chunk of data sent across a network, like one numbered postcard of a book | A DDoS (Block 1) is sending more packets than the target can handle; API replies arrive as many packets |
| Header | The label part of a packet: from-address, to-address, sequence number | Spoofing (Block 1) means faking the from-address in the header |
| Payload | The actual data inside a packet | The JSON data AbuseIPDB sends us is the payload |
| Port | A number 0–65535 that tells a computer which program a packet is for; the IP is the building, the port is the flat number | Port scans and SSH brute-force (port 22) are most of the arcs on our map |
| Listening | A program waiting behind a port for incoming connections; an "open port" means something is listening | What a port scan looks for |
| SSH (port 22) | A service for logging into a computer remotely and typing commands | The most attacked service in our data (AbuseIPDB category 22 = SSH) |
| HTTP / HTTPS (ports 80 / 443) | The protocols for websites: unencrypted / encrypted | Our globe page is served over HTTPS by GitHub Pages |
| Routing / router | Routers read a packet's destination IP and forward it one step closer; no packet knows its whole route | Attack packets on our map cross many routers to reach their targets |
| Protocol | An agreed set of rules for how computers talk: what to send, in what order and in what format | Our script uses HTTPS over TCP over IP to talk to AbuseIPDB |
| Network layer (IP) | Gets the packet to the right *machine*, using IP addresses | Where attacker IPs come from |
| Transport layer (TCP / UDP) | Gets the data to the right *program*, using ports; TCP checks delivery, UDP doesn't | Protocol attacks like SYN floods (Block 1) target this layer |
| Application layer (HTTP, SSH, DNS) | What the programs are actually saying to each other | Application-layer DDoS (Block 1); our API calls are HTTPS |
| Source IP / destination IP | The "From" and "To" addresses in a packet's header | The attacker (source) and target (destination) ends of each arc |
| DNS (Domain Name System) | Turns names like `api.abuseipdb.com` into IP addresses, like a phone's contacts app; usually UDP port 53 | Happens silently before every API call in `fetch.py`; DNS servers get abused in amplification attacks (Block 1) |
| Spoofing | Faking the source IP in a packet, like writing someone else's address in the "From" box | Easy with UDP (no handshake), hard with TCP; the key to amplification attacks (Block 1) |
| Sequence number | A number in the header that lets the receiver put packets back in order and spot missing ones | Used by TCP but not UDP, which matters for attacks in Block 1 |
