# Live Cyber Attack Map — Weekend Learning Plan

Nov 20, 2026 · @Harshini

## Overview

By Sunday night you'll have a public 3D globe showing real attacks reported in the last few hours, refreshed automatically, for ₹0 and with no credit card. About 14 hours total: 1 on Friday night, about 7 on Saturday, about 6 on Sunday.

**How it works:** a scheduled GitHub Action pulls reported malicious IPs from AbuseIPDB, looks up where each attacker is and which country reported it, and saves the result as `attacks.json`. A static page on GitHub Pages reads that file and draws each attack as an arc on a globe.

&#91;embedded content: project architecture · 4 parts, 2 days\]

Everything runs on free tiers: GitHub runs the job and hosts the page, and the data comes from free APIs.

**Stack:** Python (data pipeline) · GitHub Actions (free scheduler) · AbuseIPDB free API · MaxMind GeoLite2 (free IP-to-location database) · globe.gl (3D globe library built on Three.js) · GitHub Pages (free hosting).

**One honest note:** most arcs will be port scans and brute-force logins, not DDoS. That's what the internet's background noise really looks like. Colour arcs by attack type and call it a "cyber attack map". Knowing why is part of what you're learning.

**How to use this plan.** Every block follows the same loop:

1. **Learn:** watch the videos and read the link, before touching code.
2. **Explain:** answer the concept questions out loud or in a notes file, in your own words. If you can't, rewatch.
3. **Build:** now vibe code that block with AI. Read every line it gives you and ask it about anything you don't understand.
4. **Done when:** check the block's finish line before moving on.

Video links open a YouTube search for the exact channel and title, so they keep working even if a video is re-uploaded. Pick the matching result.

**Read along with the videos:** simple notes for every block are in Study Notes.

## How Claude should teach you

You're new to networking and security, so every concept gets explained simply first, then named with its proper technical term. The goal is that by Sunday you use the real terms naturally.

**The teaching pattern, for every new idea:**

1. **Analogy first.** An everyday comparison ("an IP address is like a postal address for a computer").
2. **Then the real term.** The proper word, defined in one plain sentence.
3. **Then where it shows up in our project.** The exact file or line where it matters.
4. **Then a quick check.** One question for you to answer before moving on.

**Copy this into a file called `CLAUDE.md` in your project's main folder.** Claude Code reads it at the start of every session, so it always knows how to teach you:

```markdown
# About me
I'm a student and a complete beginner in networking and security.
I'm building this project to learn the concepts, not just to finish it.
The full plan is in docs/PLAN.md.

# How to teach me
- Explain every new concept simply first, with an everyday analogy.
- Then give the proper technical term and a one-sentence definition.
- Then show where it appears in this project's code.
- Never use a technical term without explaining it the first time.
- After explaining something, ask me one short question to check I understood.
  Wait for my answer before continuing.

# How to build with me
- Before writing code for a block, tell me the plan in plain words.
- Build one small piece at a time. Never generate whole files at once.
- After writing code, explain it section by section, like you're teaching.
- Let me write small pieces myself when they're good practice.

# Keep these files updated
- docs/ARCHITECTURE.md: every file, what it does, and how data flows between them.
- docs/GLOSSARY.md: every technical term I've learned, with a simple meaning
  and where it appears in this project. Add new terms as they come up.
```

Also switch Claude Code's output style to **Learning**, so it asks you to write small parts of the code yourself.

## Friday night: set up and foundations (\~1 hr)

Get your accounts and tools ready tonight, and watch two short videos so Saturday starts with code.

**Accounts (all free, no card):**

- [x] GitHub account, plus a new public repo called `attack-map`
- [x] AbuseIPDB free account, then create an API key ([API docs](https://docs.abuseipdb.com/))
- [x] MaxMind account for GeoLite2, then generate a license key ([GeoLite2 signup](https://www.maxmind.com/en/geolite2/signup))
- [x] Save both keys in a password manager or private note. They never go in your code or your repo.

**Install:** Python 3.11+, Git, VS Code. Nothing else. globe.gl loads from a CDN, so you don't need Node.

**Watch (\~15 min):**

- [Code.org: The Internet: IP Addresses & DNS](https://www.youtube.com/results?search_query=code.org+The+Internet+IP+Addresses+DNS)
- [Code.org: The Internet: Packets, Routing & Reliability](https://www.youtube.com/results?search_query=code.org+The+Internet+Packets+Routing+Reliability)

**Explain in your own words:**

1. What is an IP address, and who hands them out?
2. What is a packet, and why does data get split into them?
3. What is a port? Why does SSH use 22 and HTTPS use 443?

**Done when:** the repo exists, both keys are saved somewhere safe, and you've written answers to the three questions in a `notes.md` file in the repo.

## Saturday: understand attacks, build the data pipeline (\~7 hrs)

By Saturday night a GitHub Action should be producing a fresh `attacks.json` on its own every few hours. Take breaks between blocks.

### Block 1: DDoS and attack types (1.5 hrs, no code)

**Watch:**

- [IBM Technology: DDoS attacks explained](https://www.youtube.com/results?search_query=IBM+Technology+DDoS+attacks+explained) (botnets and congestion, whiteboard style)
- [PowerCert Animated Videos: DDoS Attack Explained](https://www.youtube.com/results?search_query=PowerCert+DDoS+attack+explained)
- [Computerphile: The Attack That Could Disrupt The Whole Internet](https://www.youtube.com/results?search_query=Computerphile+The+Attack+That+Could+Disrupt+The+Whole+Internet) (DoS to DDoS to amplification)
- [Geoffrey Challen: Distributed Denial of Service](https://www.youtube.com/watch?v=Ftc0TPp9-3Q) (short university lecture)

**Read:** [Cloudflare: SYN flood attack](https://www.cloudflare.com/learning/ddos/syn-flood-ddos-attack/), plus the other pages in Cloudflare's DDoS learning section if you have time.

**Explain:**

1. DoS vs DDoS: why is the distributed version so much harder to block?
2. What is a botnet, and how do ordinary IoT devices end up in one?
3. Name the three layers of DDoS (volumetric, protocol, application) with one example each.
4. How does amplification let a small request become a huge attack? Why does spoofing the source IP matter?
5. Why do port scans and brute-force attempts usually come before bigger attacks?

### Block 2: Threat intel and the AbuseIPDB API (1 hr)

**Read:** the [AbuseIPDB API docs](https://docs.abuseipdb.com/) (the `blacklist` and `check` endpoints) and the [report categories](https://www.abuseipdb.com/categories) page. Note which category numbers mean DDoS, port scan, brute-force and SSH.

**Explain:** What does "crowdsourced threat intelligence" mean? What is the abuse confidence score? Why might a reported IP be innocent (shared IPs, compromised machines)?

**Build:**

- [ ] A Python script that downloads the blacklist once and saves the raw JSON to look at
- [ ] Call `check` with `verbose` for 5 IPs and inspect the `reports` list. Each report has categories and the reporter's country. That reporter country is your arc's target.
- [ ] Read the API key from an environment variable, never hard-coded

**Budget:** the free tier allows 1,000 checks a day. Sample about 150 IPs per run and run 4 times a day, so you stay at roughly 600.

### Block 3: How IP geolocation works (1 hr)

**Watch:** [How IP geolocation works](https://www.youtube.com/results?search_query=how+does+IP+geolocation+work+explained). Pick a short one, under 10 minutes.

**Explain:** How do regional internet registries assign IP blocks? Why is geolocation a database guess, not GPS? Why are VPN and cloud IPs often placed in the wrong city?

**Build:**

- [ ] Download `GeoLite2-City.mmdb` using your MaxMind key
- [ ] Use the `geoip2` Python package to turn attacker IPs into latitude/longitude
- [ ] For the reporter (target) side you only have a country code, so map it to a country centre point with a small countries CSV

### Block 4: The pipeline script (2 hrs)

Combine Blocks 2 and 3 into one `fetch.py` that writes `data/attacks.json`.

- [ ] Each record: attacker lat/lng and country, target lat/lng and country, attack category, report time
- [ ] Mask the last part of each IP (`45.12.88.x`). It's good practice even for public data.
- [ ] Keep only the last 24 hours and cap the file at about 500 arcs, so the page loads fast
- [ ] Handle errors: a failed API call or an HTTP 429 (rate limit) should skip that IP, not crash the run

Ask your AI to explain anything it writes that you couldn't have written yourself, especially the error handling.

### Block 5: Automate it with GitHub Actions (1.5 hrs)

**Watch:**

- [Sarah Lean: Running a CRON Job with GitHub Actions](https://www.youtube.com/results?search_query=Sarah+Lean+Running+a+CRON+Job+with+GitHub+Actions) (about 5 min)
- [Patrick Loeber: Python script with GitHub Actions on a schedule](https://www.youtube.com/results?search_query=Patrick+Loeber+python+script+github+actions+cron)

**Read:** [crontab.guru](https://crontab.guru/). Type in a few cron expressions until the five fields make sense.

**Explain:** What is a workflow, a job and a runner? Why do secrets live in repo settings instead of code? Why are GitHub cron times in UTC, not IST?

**Build:**

- [ ] Add `ABUSEIPDB_KEY` and `MAXMIND_KEY` as repo secrets
- [ ] `.github/workflows/fetch.yml` that runs every 6 hours, plus `workflow_dispatch` so you can trigger it by hand
- [ ] The workflow installs dependencies, runs `fetch.py`, and commits `data/attacks.json` back to the repo (give it `contents: write` permission)

**Done when:** the Actions tab shows a green run, and `attacks.json` has a commit made by the bot rather than by you.

## Sunday: globe, stats, deploy (\~6 hrs)

By Sunday night you'll have a public URL that shows the globe and updates itself whenever the Action runs.

### Block 6: How 3D in the browser works (1 hr)

**Watch:**

- [Fireship: Three.js in 100 Seconds](https://www.youtube.com/results?search_query=Fireship+Three.js+in+100+seconds)
- [What is WebGL, explained](https://www.youtube.com/results?search_query=what+is+WebGL+explained+GPU)

**Explore:** the [globe.gl repo](https://github.com/vasturiano/globe.gl) and its [random arcs example](https://vasturiano.github.io/globe.gl/example/random-arcs/). Open the example's source and read it line by line. It's under 30 lines.

**Explain:** What are a scene, a camera and a renderer? Why does the GPU draw this instead of the CPU? What does globe.gl do for you that raw Three.js wouldn't?

### Block 7: Draw the attacks (2.5 hrs)

- [ ] `index.html` loads globe.gl from a CDN and fetches `data/attacks.json`
- [ ] Pass the records to `arcsData`, with each arc running from attacker to target
- [ ] One colour per attack category, plus a small legend explaining the colours
- [ ] Animate the arcs (`arcDashAnimateTime`) so they "fire" like the famous maps
- [ ] Hover tooltip with countries, attack type and time
- [ ] Keep it smooth on a phone: if it stutters, show fewer arcs

### Block 8: Stats panel (1 hr)

A side panel that turns the same JSON into insight:

- [ ] Top 5 attacking countries and top 5 reporting (target) countries
- [ ] Count by attack type
- [ ] "Last updated" time, shown in IST

**Explain:** how you grouped and counted with `reduce` (or a loop). Why do some countries show up as attackers so often? (Think hosting providers and cloud data centres, not "hackers live there".)

### Block 9: Deploy and write it up (1.5 hrs)

- [ ] Turn on GitHub Pages in repo settings (main branch, root folder)
- [ ] Use a relative path to fetch the data, so it works on Pages
- [ ] Open it on your phone and send it to a friend
- [ ] README with a screenshot, a simple architecture diagram, what you learned, and the data limitations (sampled data, refreshed every 6 hours, mostly scans and not DDoS)

**Done when:** the public link works, and after the next scheduled run the globe shows new arcs without you touching anything.

## Bonus lab: watch a SYN flood happen (2–3 hrs, optional)

This lab makes DDoS concrete. Your map shows attacks; here you watch one at the packet level between two virtual machines that both belong to you. Do it on a spare evening or next weekend.

**Safety rule (non-negotiable):** only run this between your own VMs on a **host-only network**, which can't reach the internet. Never aim it at any IP you don't own: not your college network, a friend's server or a game server. Doing that is a criminal offence in India under the IT Act, even "just as a test".

**Watch:**

- [Practical Networking: TCP three-way handshake](https://www.youtube.com/results?search_query=Practical+Networking+TCP+three+way+handshake)
- [Chris Greer: TCP handshake in Wireshark](https://www.youtube.com/results?search_query=Chris+Greer+TCP+three+way+handshake+Wireshark)
- [David Bombal: Wireshark for beginners](https://www.youtube.com/results?search_query=David+Bombal+Wireshark+tutorial+beginners)

**Do:**

1. Install VirtualBox (free) and create two small Linux VMs on a host-only network.
2. Run a web server on VM B. From VM A, `curl` it while capturing in Wireshark. Find the SYN, SYN-ACK and ACK.
3. Disable SYN cookies on VM B (`net.ipv4.tcp_syncookies=0`), then use `hping3` on VM A to send a SYN flood at VM B.
4. On VM B, run `ss -s` and look at the half-open connections piling up. Try loading the web page from VM A.
5. Turn SYN cookies back on and repeat. Write down what changed and why.

**Explain:** why half-open connections fill the server's queue, and how SYN cookies avoid storing anything until the handshake finishes.

## Glossary: terms you should know by Sunday

These are the technical terms this project teaches. By the end you should be able to explain each one without looking. The plain meaning here is your starting point; Claude Code will add more detail in your `docs/GLOSSARY.md` as you build.

| Term | In plain words | Learned in |
| --- | --- | --- |
| IP address | A computer's postal address on the internet | Friday |
| Packet | Data is sent in small envelopes, not one big parcel | Friday |
| Port | A numbered door on a computer; each service listens at its own door | Friday |
| Protocol | The agreed rules two computers follow to talk | Friday |
| TCP | A protocol that confirms delivery, like registered post | Block 1 |
| UDP | A protocol that just sends and doesn't check, like dropping flyers | Block 1 |
| Three-way handshake | TCP's "hello / hello back / great, let's talk" before a connection | Block 1 |
| DoS / DDoS | Overwhelming a service with junk traffic, from one machine or thousands | Block 1 |
| Botnet | An army of infected devices controlled by one attacker | Block 1 |
| Volumetric / protocol / application-layer attack | Flooding the pipe / tying up connection slots / overloading the app itself | Block 1 |
| Amplification and spoofing | Faking the victim's address so servers send big replies to them | Block 1 |
| SYN flood | Starting thousands of handshakes and never finishing them | Block 1 |
| Port scan | Knocking on every door to see which ones open | Block 1 |
| Brute-force attack | Guessing passwords over and over, very fast | Block 1 |
| Threat intelligence | Shared information about who's attacking and how | Block 2 |
| API and API key | A way for programs to request data / a password for that access | Block 2 |
| Rate limit (HTTP 429) | A cap on how many requests you can make in a period | Block 2 |
| JSON | A simple text format for structured data | Block 2 |
| GeoIP / IP geolocation | Guessing where an IP is using a lookup database | Block 3 |
| Regional internet registry (RIR) | The organisations that hand out blocks of IP addresses | Block 3 |
| CI/CD and GitHub Actions | Automatic jobs GitHub runs for you on its own computers | Block 5 |
| Cron | A way to write "run this every 6 hours" | Block 5 |
| Secrets | Passwords and keys stored safely outside your code | Block 5 |
| WebGL and GPU | Drawing 3D in the browser using the graphics chip | Block 6 |
| Static site | A website made of fixed files, with no server running code | Block 9 |
| Honeypot | A fake target that lures attackers so you can watch them | v2 |
| SYN cookies | A defence that saves no memory until a handshake finishes | Bonus lab |

**Test yourself:** cover the middle column and explain each term out loud. Anything you stumble on, ask Claude to explain again with a different analogy.

## Self-check and what's next

If you can answer these without AI or notes, you learned the concepts and didn't just ship code. Try them Sunday night, then again a week later.

1. Explain your whole system end to end in 60 seconds, as if in an interview.
2. Why can't anyone build a map of *all* DDoS attacks on the internet?
3. What's the difference between a volumetric, a protocol and an application-layer attack? Which one is a SYN flood?
4. Why is amplification so dangerous, and why is spoofing a source IP easy with UDP but hard with TCP?
5. Why might an IP on your map be innocent?
6. How accurate is IP geolocation, and why?
7. Why does your pipeline run on a schedule instead of a server that's always on? What would you lose and gain by switching?
8. How did you keep your API keys out of the public repo?
9. If the AbuseIPDB API went down for a day, what would your site show? Is that acceptable?

**Ideas for a v2 weekend:**

- **Truly live data:** run a honeypot (for example Cowrie) on Oracle Cloud's Always Free VM and stream attacks on *your own* server over WebSockets.
- **Time slider:** replay the last 24 hours and spot when attack waves happen.
- **Compare with the pros:** put your map next to Cloudflare Radar and explain why their picture looks different.
