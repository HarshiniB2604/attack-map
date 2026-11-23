# Study Notes

These notes go with the videos in the Plan tab, one section per block. Read the section **before** the videos, so you know what to look for, then reread it after.

Each idea follows the same pattern:

- **Think of it like:** an everyday comparison
- **The real term:** the proper word and what it means
- **In our project:** where it shows up in your code
- **Check yourself:** a question to answer in your own words

When something still doesn't click, ask Claude: "Explain *\[term\]* with a different analogy."

## Friday: how the internet works

Everything in this project is computers sending each other messages. These five ideas explain how.

### IP address

- **Think of it like:** a postal address for a computer.
- **The real term:** an **IP address** (Internet Protocol address) is a number that identifies a device on a network, like `142.250.183.14`. That format is **IPv4**: four numbers from 0 to 255, separated by dots. The world ran out of IPv4 addresses, so there's also **IPv6**, which is longer, like `2404:6800:4009::200e`.
- **Public vs private:** your laptop has a private address that only works inside your home Wi-Fi (often `192.168.x.x`). Your router has one public address that the internet sees. So everyone in your house shares one public IP.
- **In our project:** every attacker on your map is an IP address. Because many devices can share one public IP, an IP isn't the same as a person.
- **Check yourself:** why can't you tell exactly which person sent a message just from an IP address?

### Packet

- **Think of it like:** sending a book by post as hundreds of numbered postcards. Each one travels on its own, and the receiver puts them back in order.
- **The real term:** data is split into small chunks called **packets**. Each packet has a **header** (from address, to address, sequence number) and a **payload** (the actual data).
- **Why:** if one packet is lost, only that piece needs resending. And many people's packets can share the same wires at once.
- **In our project:** a DDoS is, at bottom, sending more packets than the target can handle.
- **Check yourself:** what's in a packet's header, and why does it need a sequence number?

### Routing

- **Think of it like:** sorting offices passing a letter along, each one sending it a step closer to its destination.
- **The real term:** **routers** read a packet's destination address and forward it to the next router. A packet might pass through 10 to 20 routers to cross the world.
- **Check yourself:** does a packet know its whole route when it leaves your laptop?

### Port

- **Think of it like:** the IP address is the building, and the port is the flat number. One computer runs many services, and each one listens behind its own numbered door.
- **The real term:** a **port** is a number from 0 to 65535 that identifies a specific service on a computer. Common ones: **22** = SSH (remote login), **80** = HTTP (websites), **443** = HTTPS (secure websites), **53** = DNS.
- **In our project:** attackers **scan ports** to find open doors, and they hammer port 22 trying to guess SSH passwords. These are the most common attacks you'll see.
- **Check yourself:** why do attackers care which ports are open?

### Protocol

- **Think of it like:** the agreed etiquette of a phone call: say hello, wait for a reply, take turns, say bye.
- **The real term:** a **protocol** is a set of rules for how computers communicate. IP is the protocol for addressing and delivery. TCP and UDP (Block 1) sit on top of it. HTTP is the protocol for web pages.
- **Check yourself:** name one protocol and say what it's responsible for.

### DNS

- **Think of it like:** your phone's contacts app. You tap "Mom", and it dials the number.
- **The real term:** **DNS** (Domain Name System) turns names like `google.com` into IP addresses, because computers only understand the numbers.
- **In our project:** DNS servers can be abused in **amplification attacks** (Block 1).
- **Check yourself:** what happens first when you type a website name into your browser?

## Block 1: how attacks work

To understand attacks, you first need the two ways computers send data: TCP and UDP.

### TCP vs UDP

- **Think of it like:** TCP is registered post: you get a receipt, and lost letters are resent. UDP is dropping flyers through letterboxes: fast, no receipts, no idea if they arrived.
- **The real term:** **TCP** (Transmission Control Protocol) is reliable. It sets up a connection first, numbers every packet, and resends lost ones. Websites, SSH and email use it. **UDP** (User Datagram Protocol) just fires packets off with no connection and no checking. It's used where speed matters more than perfection: video calls, online games, DNS lookups.
- **Why it matters for attacks:** because UDP doesn't check who you are, it's easy to **fake the sender's address**. That's what makes amplification attacks possible (below).
- **Check yourself:** why would a video call use UDP instead of TCP?

### The TCP three-way handshake

- **Think of it like:** starting a phone call. "Hello, can you hear me?" / "Yes, can you hear me?" / "Yes. Okay, let's talk."
- **The real term:** before any data is sent, TCP does a **three-way handshake**:
  1. **SYN** (synchronise): the client says "I want to connect."
  2. **SYN-ACK** (synchronise-acknowledge): the server says "Okay, I hear you," and reserves memory for this connection.
  3. **ACK** (acknowledge): the client says "Great," and the connection is open.
- **Half-open connection:** after step 2 but before step 3, the server is holding a slot open and waiting. Remember this for SYN floods.
- **Check yourself:** at which step does the server start using memory for the connection?

### DoS and DDoS

- **Think of it like:** one prankster keeps a pizza shop's phone line busy (DoS). Then 10,000 pranksters call at once from different numbers (DDoS), and the shop can't block them all or tell real customers from fake ones.
- **The real term:** a **DoS** (Denial of Service) attack makes a service unavailable to real users by overwhelming it. A **DDoS** (Distributed Denial of Service) does it from many machines at once, which makes it far harder to block.
- **Key idea:** DDoS attacks **availability**, meaning whether people can reach the service. Nothing is stolen and nobody breaks in.
- **Check yourself:** why is a DDoS much harder to stop than a DoS?

### Botnet

- **Think of it like:** a puppet master with thousands of puppets, where the puppets don't know they're puppets.
- **The real term:** a **botnet** is a network of infected devices (computers, routers, cameras, smart TVs) that an attacker controls remotely. Each infected device is a **bot**. The attacker's control server is the **C2** (command and control).
- **How devices get infected:** often through default passwords like `admin/admin`, or software that was never updated. Attackers scan the internet for these weak devices and break in automatically.
- **In our project:** many of the brute-force and scanning IPs on your map are probably bots: infected devices scanning for more devices to infect.
- **Check yourself:** why are cheap smart devices so often part of botnets?

### The three types of DDoS

| Type | Think of it like | What's being exhausted | Example |
| --- | --- | --- | --- |
| **Volumetric** | Blocking the road to the shop with traffic | Network bandwidth (the size of the internet pipe) | UDP floods, amplification |
| **Protocol** | Ringing every phone line and never speaking | The server's connection slots and memory | SYN flood |
| **Application-layer** | Ordering the most complicated pizza, again and again | The app's own processing (database, CPU) | Repeatedly loading a heavy search page |

Application-layer attacks are the sneakiest, because each request looks like a normal visitor.

**Check yourself:** a website is still online, but its login page takes 60 seconds to load. Which type is that most likely to be?

### SYN flood

- **Think of it like:** calling every phone line, saying hello, then going silent. Each line stays busy waiting for you.
- **The real term:** a **SYN flood** sends thousands of SYN packets but never sends the final ACK. The server keeps reserving slots for half-open connections until it runs out, and then real users can't connect.
- **Defence:** **SYN cookies.** The server doesn't reserve anything after the SYN. It encodes the connection details into its reply, and only sets up the connection once a valid ACK comes back.
- **Check yourself:** why doesn't a SYN flood need a lot of bandwidth to work?

### Amplification and spoofing

- **Think of it like:** mailing hundreds of "please send me your free catalogue" requests with your enemy's home address on them. You spend a few stamps; they get buried in heavy catalogues.
- **The real term:** **spoofing** means faking the sender's IP address in a packet. **Amplification** means sending a small request to a public server (such as DNS or NTP) with the *victim's* address as the sender, so the server sends a much bigger reply to the victim. Some protocols reply with tens or hundreds of times more data than the request.
- **Why UDP:** UDP has no handshake, so the server never checks that the sender is real. With TCP, a fake address could never complete the handshake.
- **Check yourself:** why does amplification only work with protocols that don't use a handshake?

### Port scans and brute-force attacks

- **Port scan:** like walking down a street trying every door handle. Attackers send packets to many ports to see which ones respond, and that reveals which services are running. Tools like `nmap` do this. It's often the first step of an attack.
- **Brute-force attack:** like trying every key on a giant keyring. Automated programs try thousands of username and password combinations, often on SSH (port 22) or website logins.
- **In our project:** these two will be **most of the arcs** on your map. That's real-world data: the internet's constant background noise is bots scanning and guessing, all day, every day.
- **Check yourself:** how could a port scan and a brute-force attack be connected to building a botnet?

## Block 2: threat intelligence and APIs

This block covers where your data comes from and how your code asks for it.

### Threat intelligence

- **Think of it like:** a neighbourhood watch group chat. When someone sees a suspicious person trying car doors, they post it, so everyone else can lock up.
- **The real term:** **threat intelligence** is information about attackers (their IPs, methods and targets) that defenders share so others can protect themselves. AbuseIPDB is **crowdsourced**: thousands of server owners report attacks they see.
- **Abuse confidence score:** AbuseIPDB's 0 to 100% estimate of how likely an IP is to be malicious, based on how many people reported it, and how recently.
- **Categories:** each report is tagged with a type, such as port scan, brute-force, SSH or DDoS. You'll use these to colour your arcs.
- **Why a reported IP might be innocent:** it could be a hacked device whose owner doesn't know, a shared IP (one bad user on a shared network gets everyone reported), or simply a wrong report.
- **In our project:** AbuseIPDB is your news feed. The reporter's country becomes the target end of each arc.
- **Check yourself:** why is crowdsourced data useful but not 100% reliable?

### API

- **Think of it like:** a restaurant waiter. You don't walk into the kitchen; you give the waiter your order in a set format, and they bring back your food.
- **The real term:** an **API** (Application Programming Interface) is a defined way for one program to ask another for data or actions. A **web API** works over the internet using URLs. Each URL you can call is an **endpoint**, such as AbuseIPDB's `check` and `blacklist`.
- **Request and response:** your code sends a **request** (which endpoint, plus details like which IP), and the server sends back a **response** (the data, plus a status code).
- **Check yourself:** in the restaurant analogy, what are the menu, the order and the food?

### API key

- **Think of it like:** a library card. It proves you're a member, and the library tracks how many books you borrow.
- **The real term:** an **API key** is a secret code sent with each request to identify you. The server uses it to allow access and to count your usage.
- **Golden rule:** never put a key directly in your code or upload it to GitHub. Bots scan GitHub for leaked keys within minutes. Store it in an **environment variable** (Block 4) or a **secret** (Block 5).
- **Check yourself:** what could someone do with your AbuseIPDB key?

### JSON

- **Think of it like:** a neatly labelled form. Every value has a label, so any program can read it.
- **The real term:** **JSON** (JavaScript Object Notation) is a text format for structured data, and most APIs reply in it. It uses `{ }` for objects (labelled values) and `[ ]` for lists:

```json
{
  "ipAddress": "45.12.88.10",
  "countryCode": "NL",
  "abuseConfidenceScore": 100,
  "categories": [18, 22]
}
```

- **In our project:** AbuseIPDB replies in JSON, and your own output `attacks.json` is JSON too. Python reads it as a dictionary; JavaScript reads it as an object.
- **Check yourself:** in the example, which value is a list?

### Rate limits and HTTP status codes

- **Think of it like:** a buffet with a "max 3 plates per person" rule.
- **The real term:** a **rate limit** caps how many requests you can make in a time window. The free AbuseIPDB plan allows 1,000 checks a day.
- **HTTP status codes** are the server's three-digit answer to each request:

| Code | Meaning | Think of it like |
| --- | --- | --- |
| 200 | OK, here's your data | Food served |
| 401 | Unauthorised: bad or missing key | "Members only" |
| 404 | Not found: wrong URL | No such dish |
| 429 | Too many requests: rate limited | "You've had your 3 plates" |
| 500 | Server error: their problem, not yours | Kitchen on fire |

- **In our project:** your script must handle 429 and 500 calmly: skip that IP and keep going instead of crashing.
- **Check yourself:** your script gets a 401. What's the most likely cause?

## Block 3: IP geolocation

This block covers how you go from an IP address to a dot on the globe, and why that dot is only an estimate.

### Who hands out IP addresses

- **Think of it like:** a phone company giving out blocks of numbers to different cities. If you know which block a number came from, you can guess the area.
- **The real term:** **IANA** gives large blocks of IP addresses to five **Regional Internet Registries (RIRs)**, one per region. **APNIC** covers Asia-Pacific, including India. RIRs give smaller blocks to **ISPs** (like Jio or Airtel), companies and cloud providers, and those hand individual addresses to customers.
- **Check yourself:** which RIR would an Indian ISP get its addresses from?

### How GeoIP databases work

- **Think of it like:** guessing where someone lives from their phone number's area code. Usually right about the region, often wrong about the street.
- **The real term:** **IP geolocation** estimates where an IP is. Companies like MaxMind build a database by combining RIR registration records, data from ISPs, network measurements and user-submitted corrections. **GeoLite2** is MaxMind's free version. It's a file (`.mmdb`) that you search locally: you give it an IP, and it returns the country, city and latitude/longitude.
- **Accuracy:** country-level is usually right. City-level is often off, sometimes by hundreds of kilometres. It can never give a street address.
- **Why it goes wrong:**
  - **VPNs and proxies:** the IP belongs to the VPN server, not the real person.
  - **Cloud servers:** an AWS IP is placed at the data centre, even if whoever's using it is elsewhere.
  - **Mobile networks:** many users in a whole region share a pool of IPs.
- **In our project:** each attacker dot shows the attacker's IP location, which is often a data centre or a hacked device, not where the human attacker actually is.
- **Check yourself:** why do many attacks appear to come from countries with lots of data centres?

### Country centroids

- **The problem:** for the target end of each arc, AbuseIPDB only gives the reporter's **country code** (like `DE` for Germany), not an exact location.
- **The fix:** use the country's **centroid**, its rough geographic centre point. A small CSV file maps each country code to a latitude/longitude.
- **Check yourself:** what's a downside of putting every German report at the same point?

## Block 4: building the pipeline

A **data pipeline** is a series of steps that take raw data in and put clean data out, like a factory line: fetch, then look up locations, then clean, then save.

### Environment variables

- **Think of it like:** keeping your house key in your pocket instead of taping it to the front door.
- **The real term:** an **environment variable** is a named value set outside your code, which the program reads while it runs. In Python: `os.environ["ABUSEIPDB_KEY"]`.
- **Why:** your code can be public on GitHub while your keys stay private. On your laptop you set them in a `.env` file. Add `.env` to `.gitignore` so Git never uploads it.
- **Check yourself:** what does `.gitignore` do, and why must `.env` be in it?

### Error handling

- **Think of it like:** a delivery driver who finds one house locked, leaves a note and moves to the next house, instead of giving up on the whole route.
- **The real term:** **error handling** means expecting things to go wrong and dealing with it. In Python, you wrap risky code in `try` / `except`. If calling the API for one IP fails, you catch the error, log it and continue with the next IP.
- **Common errors you'll handle:** rate limits (429), network timeouts, an IP missing from the GeoLite database, and missing fields in the JSON.
- **Check yourself:** what should happen if 3 out of 150 IPs fail? What if all 150 fail?

### Cleaning and shaping data

- **Think of it like:** cooking from a full grocery bag. You only use some ingredients, and you prepare them the same way each time.
- **The real term:** **data cleaning** means dropping records you can't use (no location, too old), removing duplicates, and reshaping each record into one consistent format, called a **schema**. Each record in `attacks.json` will have the same fields: attacker location, target location, category and time.
- **Why cap it at about 500:** the browser has to download and draw everything. Smaller files load faster, especially on phones.
- **Check yourself:** why does every record need exactly the same fields?

### Masking IPs

- **The real term:** **data minimisation** means only publishing what you need. Showing `45.12.88.x` instead of the full address is enough for the map, and avoids pointing at a specific device, which could be an innocent person's hacked router.
- **Check yourself:** the data is public on AbuseIPDB already. Why mask it anyway?

## Block 5: automation with GitHub Actions

This block makes your pipeline run by itself, on GitHub's computers, for free.

### CI/CD and GitHub Actions

- **Think of it like:** setting an alarm-triggered robot vacuum. You program it once, and it runs on schedule without you.
- **The real term:** **CI/CD** (Continuous Integration / Continuous Deployment) means automatically testing, building and deploying code. **GitHub Actions** is GitHub's automation tool. It runs your scripts on GitHub's machines when something happens, such as a push, a button click or a schedule.
- **Check yourself:** why is it useful that the job runs on GitHub's computer and not your laptop?

### Workflow, job, step, runner

| Term | Think of it like | What it is |
| --- | --- | --- |
| **Workflow** | The whole recipe | A YAML file in `.github/workflows/` describing what to run and when |
| **Trigger** (`on:`) | What makes you start cooking | The event that starts the workflow, like a schedule or a manual button |
| **Job** | One dish in the recipe | A group of steps that run together on one machine |
| **Step** | One instruction | A single command, like "install Python" or "run fetch.py" |
| **Runner** | The kitchen | The temporary computer GitHub lends you; it's wiped after each run |

- **YAML:** the settings-file format workflows are written in. It uses indentation to show structure, so a wrong space can break it.
- **Check yourself:** since the runner is wiped after each run, how does `attacks.json` survive? (Answer: the workflow **commits** it back to your repo.)

### Cron

- **Think of it like:** an alarm clock with five dials: minute, hour, day of month, month, day of week.
- **The real term:** **cron** syntax describes a schedule in five fields. `*` means "every".

```
0 */6 * * *
│  │  │ │ └ any day of the week
│  │  │ └── any month
│  │  └──── any day of the month
│  └─────── every 6th hour (0, 6, 12, 18)
└────────── at minute 0
```

- **Watch out:** GitHub runs cron in **UTC**, which is 5 hours 30 minutes behind IST. Scheduled runs can also start a few minutes late when GitHub is busy.
- **Check yourself:** what does `30 2 * * *` mean, and what time is that in IST?

### Secrets

- **Think of it like:** a locked drawer in GitHub that only your workflow can open.
- **The real term:** **repository secrets** are encrypted values stored in your repo's settings. The workflow reads them as `${{ secrets.ABUSEIPDB_KEY }}` and passes them to your script as environment variables. They never appear in your code or logs.
- **Check yourself:** your repo is public. Can visitors see your secrets? Why not?

## Blocks 6–9: the website

The last part is how the browser turns `attacks.json` into a spinning globe, and how the site gets online.

### HTML, CSS, JavaScript

- **Think of it like:** a house. HTML is the walls and rooms, CSS is the paint and furniture, and JavaScript is the electricity that makes things move and respond.
- **The real term:** **HTML** defines the page's structure, **CSS** styles it, and **JavaScript** runs code in the browser.
- **`fetch`:** the JavaScript command that requests a file or API over the internet, such as `fetch("data/attacks.json")`. It's the browser-side version of what your Python script does with AbuseIPDB.
- **Check yourself:** which of the three draws the globe, and which loads the data?

### GPU and WebGL

- **Think of it like:** the CPU is one brilliant professor solving problems one at a time. The GPU is a hall of thousands of students who each colour in one pixel at the same moment.
- **The real term:** the **GPU** (Graphics Processing Unit) is a chip built to do many simple calculations in parallel, which is perfect for 3D graphics. **WebGL** is the browser feature that lets JavaScript use the GPU.
- **Check yourself:** why would drawing 500 animated arcs be slow on the CPU alone?

### Three.js and globe.gl

- **Think of it like:** WebGL is raw bricks and cement. Three.js is a building kit. globe.gl is a ready-made globe you just decorate.
- **The real term:** **Three.js** is a JavaScript library that makes WebGL easier. Every 3D scene has three parts:
  - **Scene:** the world that holds all the objects.
  - **Camera:** the viewpoint you see the scene from.
  - **Renderer:** draws what the camera sees onto the screen, many times a second.
- **globe.gl** is built on Three.js and handles the globe, rotation and zooming for you. You give it data **layers**: `arcsData` for arcs, `pointsData` for dots. Each arc needs a start latitude/longitude and an end latitude/longitude, which is exactly what your JSON provides.
- **Library vs CDN:** a **library** is code someone else wrote for you to use. A **CDN** (Content Delivery Network) is a set of servers around the world that deliver files like that library quickly, so you link to it instead of downloading it.
- **Check yourself:** what four numbers does each arc need?

### Static sites and GitHub Pages

- **Think of it like:** a printed menu versus a waiter who takes custom orders. A static site is the printed menu: the same files for everyone, with nothing computed on the server.
- **The real term:** a **static site** is just files (HTML, CSS, JavaScript, JSON) that a server sends as they are. A **dynamic site** runs code on the server for each visitor. **GitHub Pages** hosts static sites from a repo for free.
- **Why it works for us:** all the "thinking" happens in GitHub Actions every 6 hours. The website only has to show the latest file.
- **Relative path:** `data/attacks.json` (relative) works wherever the site is hosted. A full address on your laptop (`C:/Users/...`) only works on your laptop.
- **Check yourself:** the AbuseIPDB key is never on the website. How does the site still get attack data?
