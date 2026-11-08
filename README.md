[README.md](https://github.com/user-attachments/files/34556837/README.md)
# NetDrift 2.0

**Advanced host-network integrity monitor** — baseline what “normal” looks like on your PC, detect silent drift, score posture, export blame reports, and demo attacks for interviews.


---

## Why recruiters care

NetDrift is not a toy chat/port-scanner. It demonstrates:

- Multi-signal host networking (DNS, ARP/gateway, routes, proxy, Wi‑Fi BSSID, TLS, captive portal)
- Extra enterprise signals (hosts file, VPN adapters, firewall profiles, DHCP server, network category, latency)
- Diff engine + severity + plain-English remediation
- Debounced watch loop, SQLite history, webhook alerts
- Local dashboard + REST API
- HTML / Markdown / JSON incident artifacts
- Scenario simulator for live demos

---

## Features (v2)

| Area | What you get |
|---|---|
| Collectors | Interfaces, gateway IP/MAC, DNS, routes, proxy, Wi‑Fi, public IP, TLS probes, captive portal, latency, hosts file, VPN, firewall, DHCP, Windows network profile |
| Detection | 20+ finding codes with cause + fix |
| Scoring | Snapshot posture score + drift health score |
| Ignore rules | Suppress noisy codes/categories via config |
| Watch | Debounce, auto-rebase option, JSONL + SQLite |
| Notify | Optional webhook (Slack/Discord/custom) |
| Reports | JSON + Markdown + HTML |
| Dashboard | `netdrift serve` on `http://127.0.0.1:8765` |
| Demos | 14 simulate scenarios for CV/interview |

---

## Install

```powershell
cd $env:USERPROFILE\Desktop\NetDrift
py -m pip install -e ".[dev]"
py -m netdrift version
```

Or run `.\scripts\install.ps1`.

---

## Quick start

```powershell
py -m netdrift doctor
py -m netdrift baseline create --note "trusted network"
py -m netdrift score
py -m netdrift scan
py -m netdrift watch --interval 5

# Dashboard
py -m netdrift serve

# Interview demos
py -m netdrift scenarios
py -m netdrift simulate arp-spoof
py -m netdrift simulate dns-hijack
py -m netdrift simulate hosts-tamper
py -m netdrift simulate tls-intercept
py -m netdrift explain GATEWAY_MAC_CHANGED
```

Data directory: `%USERPROFILE%\.netdrift\`  
(`baselines/`, `reports/`, `netdrift.db`, `events.jsonl`, `config.yaml`)

---

## Config highlights

```yaml
watch:
  interval_seconds: 5
  debounce_hits: 2
  include_latency: true
  include_hosts: true
  include_vpn: true
  include_firewall: true
webhook:
  enabled: false
  url: "https://hooks.example/netdrift"
  min_severity: high
ignore_rules:
  - code: VPN_CONNECTED
trusted_dns: ["1.1.1.1", "8.8.8.8"]
trusted_ssids: ["HomeWiFi"]
```

Write defaults with: `py -m netdrift init-config`

---

## Architecture

```text
capture_snapshot() ──► NetworkSnapshot (+ health score)
        │
 baseline store ──► compare_snapshots() ──► DiffReport
        │                │
   ignore rules     severity / score
        │
 watch debounce ──► SQLite + reports + webhook
        │
 netdrift serve ──► Dashboard / REST API
```

---

## CV bullet

> Built **NetDrift 2.0**, an advanced host-network integrity platform that baselines DNS/gateway/ARP/proxy/Wi‑Fi/TLS/hosts/VPN/firewall/DHCP state, detects silent drift with health scoring and blame reports, stores incidents in SQLite, exposes a local dashboard/API, and ships interview-ready attack simulations.

---

## Development

```powershell
py -m pip install -e ".[dev]"
py -m pytest -q
```

---

## Security note

Defensive monitoring only. Reads local network configuration and performs outbound probes. Does not spoof ARP, intercept traffic, or attack networks.

---

## License

MIT
