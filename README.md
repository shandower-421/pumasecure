# PumaSecure

An infosec roguelike in a single HTML file. You're a security team dropped into a poorly managed network — scan assets, discover vulnerabilities, and fix them before an AI red team punches through to your critical infrastructure.

**No install. No dependencies.** Open `pumasecure.html` in any modern browser and go.

## Gameplay

You have **15 minutes** to defend a procedurally generated network while an AI red team escalates through MITRE ATT&CK-inspired phases — from passive recon to active exploitation to exfiltration.

### The network

Six concentric zones, from outer perimeter to inner core:

| Zone | Examples |
|------|----------|
| Perimeter | Firewalls, WAFs, load balancers |
| DMZ | Web servers, mail gateways, DNS, VPN |
| Corporate | Workstations, file servers, printers, Wi-Fi APs |
| Internal Services | App servers, CI/CD, internal APIs, dev environments |
| Data / Critical | Databases, domain controllers, backup systems, key vaults |
| Cloud / Shadow IT | Cloud VMs, containers, SaaS apps, IoT, unmanaged devices |

The red team typically attacks from the outside in, but compromised Wi-Fi APs, unpatched workstations, or shadow IT can give them unexpected entry points.

### Red team phases

| Phase | Window | Tempo |
|-------|--------|-------|
| Reconnaissance | 0:00 – 3:00 | Passive probing every 10–15s |
| Initial Access | 3:00 – 6:00 | Active exploitation every 5–8s |
| Lateral Movement | 6:00 – 10:00 | Pivoting inward every 4–6s |
| Privilege Escalation | 10:00 – 13:00 | 1.3x bonus, every 3–5s |
| Exfiltration | 13:00 – 15:00 | 1.5x bonus, every 2–4s |

The red team gathers intel during recon and picks targets strategically. Compromising key assets gives them lasting advantages — owning a firewall bypasses perimeter defenses, a domain controller grants credential access network-wide, a VPN gateway tunnels past internal defenses.

### Scanning

- **Passive scanning** runs automatically but is slow and incomplete
- **Active scanning** (click "Scan Asset") reveals all vulns and discovers neighboring assets — takes 15–25s and one team slot
- **Deep scanning** reduces exploitability by 15–20% and uncovers shadow IT connections — takes 20–30s and one team slot

### Fixing vulnerabilities

| Action | Speed | Effect | Risk |
|--------|-------|--------|------|
| **Patch** | Slow | Full fix | 70–95% success; change windows get denied, dependencies break |
| **Configure** | Fast | Reduces severity | Configs get reverted, deployed to wrong host |
| **Replace** | Very slow | Near-guaranteed fix | Migrations crash, license keys reject |
| **Mitigate** | Instant | Reduces exploitability 50–75% | Doesn't fix the underlying issue |

Fixes fail for real reasons. Some vuln categories (zero-days, physical security) can only be mitigated.

### Team slots

Everything costs a team slot — scanning, fixing, investigating incidents. You start with 2 slots. Hire a contractor mid-run (+1 slot, $10K) or unlock permanent slots in the Skill Tree.

### Scoring

Any critical asset breached = automatic **F**. Otherwise you're graded on:

| Axis | Weight |
|------|--------|
| Containment | 30% |
| Security Posture | 25% |
| Assessment Coverage | 15% |
| Incident Response | 15% |
| Efficiency | 15% |

Grades: **S** (95+, threat 5+ only) / **A** (85+) / **B** (70+) / **C** (55+) / **D** (40+) / **F**

### Meta-progression

Earn **Intel** based on your grade and threat level. Spend it in the **Skill Tree** for permanent upgrades — faster scanning, extra team slots, budget boosts, threat intelligence. Unlock new network templates (Healthcare, Finance, Government) by hitting milestones. Crank up the **Threat Level** (1–10) for harder runs and bigger Intel rewards.

## Controls

| Input | Action |
|-------|--------|
| Click | Select a node |
| Scroll | Zoom the map |
| Drag | Pan the map |
| Space | Pause / unpause |

Use the detail panel on the right to scan, fix, and investigate.

## Tech

Single HTML file. Vanilla JS. HTML5 Canvas. No frameworks, no build tools, no external assets. CRT terminal aesthetic.
