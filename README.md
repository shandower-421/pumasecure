# PumaSecure

A single-file HTML5 infosec roguelike where you play as a security team dropped into a poorly managed network. Scan assets, discover vulnerabilities, and fix them before the red team punches through to your critical infrastructure.

Open `pumasecure.html` in any modern browser. No build step, no dependencies.

## How it works

### The network

The map is an onion-model network with six zones:

| Ring | Zone | Example assets |
|------|------|----------------|
| Outer | Perimeter | Firewalls, WAFs, load balancers |
| | DMZ | Web servers, mail gateways, DNS, VPN |
| | Corporate | Workstations, file servers, printers, Wi-Fi APs |
| | Internal Services | App servers, CI/CD, internal APIs, dev environments |
| | Data / Critical | Databases, domain controllers, backup systems, key vaults |
| Center | Cloud / Shadow IT | Cloud VMs, containers, SaaS apps, IoT, unmanaged devices |

Critical assets sit at the center. The perimeter is on the outside. This is the topology you're defending.

### The red team

The red team AI runs through realistic attack phases: reconnaissance, initial access, lateral movement, privilege escalation, and exfiltration.

They **typically** come from the outside in — probing perimeter assets, finding a foothold, then pivoting deeper toward your critical data. But not always. A compromised Wi-Fi access point or an unpatched workstation could give them an unexpected entry point. Shadow IT and unmanaged devices are especially dangerous because they sit outside your normal visibility.

The red team operates on a 15-minute clock, escalating through MITRE ATT&CK-inspired phases:

| Phase | Time | What's happening |
|-------|------|------------------|
| **Reconnaissance** | 0:00–3:00 | Red team passively probes your perimeter, gathering intel on vulnerability categories. Attacks are infrequent (every 10-15s). This is your safest window to scan and fix. |
| **Initial Access** | 3:00–6:00 | Active exploitation begins. The red team targets perimeter nodes using the intel they gathered, attacking every 5-8s. Fix perimeter vulns before this hits. |
| **Lateral Movement** | 6:00–10:00 | From any footholds, the red team pivots inward through network connections. Every compromised node becomes a new launch point. Attacks every 4-6s. |
| **Priv Escalation** | 10:00–13:00 | The red team pushes toward deeper zones with a 1.3x exploitation bonus. Attacks every 3-5s. They're hunting for domain admin. |
| **Exfiltration** | 13:00–15:00 | Final push at 1.5x exploitation bonus, attacking every 2-4s. They're going for your critical assets. |

The red team gathers intel during recon. They learn what vulnerability categories exist on your assets before attempting exploitation. They pick targets strategically — a domain controller is far more valuable as a pivot than a printer. Compromising certain node types gives them lasting advantages: owning a firewall lets them bypass perimeter defenses, owning a domain controller gives them credential access across the network, owning a VPN gateway lets them tunnel into deeper zones.

### Node types matter

Not all assets are created equal. A domain controller (👑) is a crown jewel — if the red team compromises it, they gain domain admin privileges and a massive exploitation bonus across your entire network. A printer (🖨) has low strategic value, but an unpatched printer is still a foothold the red team can pivot from. A VPN gateway (🔒) in the wrong hands gives attackers a tunnel straight past your internal defenses.

When deciding what to scan and fix first, think about what each asset *enables* if compromised, not just what the vulnerability scanner tells you about severity scores.

### Vulnerability types matter

Each vulnerability has an attack vector — Network, Adjacent, Local, or Physical — and these determine how the red team can exploit them:

- **Network** vulns can be attacked from anywhere the red team has reach
- **Adjacent** vulns require the attacker to be in the same network segment
- **Local** vulns require an existing foothold on the machine (or domain admin)
- **Physical** vulns require the attacker to be in the same zone or adjacent zone

A critical-severity Physical vulnerability on a well-isolated server may be less urgent than a medium-severity Network vulnerability on a perimeter firewall. Context matters more than CVSS scores.

### Fixing things (and failing)

When you find a vulnerability, you have up to four ways to address it depending on the category:

- **Patch** — Full fix if it works. Expensive, causes downtime, and takes the longest. Success rate: 70-95%. When it fails, it fails for real reasons: the ops team declined the change window, a dependency conflict broke three other services, the server rolled back on restart, or nobody actually rebooted the box.
- **Configure** — Cheaper and faster, no downtime. Reduces severity rather than eliminating the vuln. But the new config might conflict with a legacy app and get reverted by ops, or you might deploy it to the wrong host entirely.
- **Replace** — Rip and replace the affected component. Near-guaranteed to work, but very expensive and slow. Sometimes the migration script crashes halfway or the license key rejects the new version.
- **Mitigate** — Always succeeds, but doesn't fix the underlying issue. Reduces exploitability by 50-75%. The vuln is still there — you've just made it harder to exploit. Think WAF rules, network segmentation, or disabling unnecessary features.

Fixes are not guaranteed. A patch that requires a reboot might get pushed back because the change window was denied. A config change might get silently reverted by a cron job restoring yesterday's backup. A certificate deployment might leave an incomplete chain. You'll spend team time and budget on a fix only to see `FIX FAILED` in the log — and now you have to decide whether to try again or move on to something else.

Some vulnerability categories can only be mitigated, not fully fixed. Zero-days and physical security issues don't have patches — you can only reduce the risk and hope it's enough.

### Scanning and discovery

You start each run with partial knowledge. Some assets are "pre-known" — documented infrastructure that was there when the network was first deployed. But networks grow organically, and there's plenty you don't know about yet.

**Passive scanning** happens automatically. Your sensors will gradually reveal *some* vulnerabilities on discovered assets without any team effort. But passive scanning is slow and incomplete — it won't find everything.

**Active scanning** (clicking "Scan Asset") assigns a team member to thoroughly scan a node. This takes 15-25 seconds and occupies a team slot. A full scan reveals all vulnerabilities, discovers neighboring assets connected via network edges, and gives you the complete picture. It's worth doing even on nodes where passive scanning has already found a few vulns — there may be more hiding.

**Deep scanning** goes further. After a normal scan, you can deep scan an asset to reduce its overall exploitability by 15-20% and discover hidden shadow IT connections. Deep scans take 20-30 seconds and also consume a team slot.

### Team slots: the core constraint

Everything your team does — scanning, deep scanning, fixing vulnerabilities, investigating incidents — costs a team slot. You start with 2 slots. That means you can run two activities in parallel: maybe a scan on the DNS server while patching a critical vuln on the firewall.

The strategic tension is real: do you scan the domain controller to find out what's wrong with it, or fix the critical RCE you already found on the web server? Every slot matters.

You can hire contractors (+1 slot for $10k) or unlock upgrades in the skill tree to work faster. The "Rapid Scanning" upgrade cuts scan times by ~30%. "Deep Scan Protocol" does the same for deep scans.

### Scoring

If any critical asset (zone 4) is breached, you get an automatic **F — CRITICAL BREACH**. That's the one rule that matters most: protect the core.

For non-breach runs, you're graded on five axes:

| Axis | Weight | What it measures |
|------|--------|------------------|
| **Containment** | 30% | How much of the network stayed clean. Nodes you reclaimed count as contained — losing a firewall temporarily and taking it back is better than leaving it compromised. |
| **Posture** | 25% | Severity-weighted percentage of known vulns you fixed or mitigated. This is your end-state security posture — "how clean is the environment when the auditor walks in?" |
| **Assessment** | 15% | What percentage of total vulnerabilities across the network did you identify? Can't fix what you can't see. |
| **Response** | 15% | How fast you responded to compromises. Perfect score if nothing was compromised. Rewards fast incident response. |
| **Efficiency** | 15% | Risk reduced per dollar spent. Rewards smart triage over throwing money at everything. |

**Grades:** S (95+, threat 5+ only) / A (85+) / B (70+) / C (55+) / D (40+) / F

You earn Intel based on your grade and threat level. Spend it in the Skill Tree for permanent upgrades. Grade F earns zero Intel.

## Controls

- **Click** a node on the map or asset list to select it
- **Scroll** to zoom the network map
- **Drag** to pan the map
- **Space** to pause/unpause
- Use the detail panel (right side) to scan, deep scan, fix, and investigate

## Tech

Single HTML file. Vanilla JavaScript. HTML5 Canvas for the network map. No frameworks, no build tools, no external assets. CRT terminal aesthetic because that's how security tools should look.
