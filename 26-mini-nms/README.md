# 26 — Mini-NMS · Network Operations Center

A working network management system that reads real Cisco IOS configurations, monitors live device state, and troubleshoots connectivity faults by reasoning through the network layer by layer. Unlike the blueprint-level projects in this portfolio, the full source code is public and the tool is live.

**Status:** Live — deployed and publicly reachable
**Live demo:** https://mini-nms.onrender.com
**Code:** https://github.com/Lazz24/mini-nms
**Stack:** Python (stdlib only) · Cisco IOS parsing · HTTP API · Vanilla JS · Render
**Tags:** #networking #cisco-ios #config-audit #troubleshooting #python #nms

## What It Does

Mini-NMS reads the configuration files of a small network — two routers, two switches, and a firewall — and turns them into a live operations dashboard that does the three things a network engineer does daily: audit the configuration, monitor live state, and troubleshoot faults.

It was built to answer a challenge: with current AI, can someone reproduce a real slice of a specialist's job — a job the builder does not hold? The tool is the proof. It works on any Cisco IOS config in the standard `running-config` format, not only the sample network.

## System Components

| Component | Description |
|-----------|-------------|
| Parser | Turns raw Cisco IOS text into structured data; surfaces any unrecognized line rather than dropping it silently |
| Model | Assembles parsed configs into one topology; infers links by subnet and description; flags subnet conflicts instead of drawing false connections |
| Audit engine | Ruleset over security, correctness, and configuration drift; each finding carries severity, plain-English reasoning, and the IOS fix |
| Troubleshooter | Bottom-up L1→L2→L3→ACL diagnostic walk that stops at the first fault; reads live state so down links are caught even when config looks correct |
| Monitor | Simulated live interface state, utilization, alerts, and fault injection |
| API | Python stdlib HTTP server exposing the engine as JSON and serving the dashboard |
| Dashboard | Single-page operations UI — topology, live status, alerts, audit findings, troubleshooting |

## Architecture

IOS configs -> parser -> network model -> engine -> API -> dashboard
|
audit . monitor . troubleshoot

symptom in -> resolve endpoints
-> L1 interface state (config + live)
-> L2 VLAN / trunk
-> L3 addressing / routing / overlap
-> L4 ACL (first-match-wins)
-> stop at first fault -> root cause + IOS fix


## Key Design Decisions

- **LLM narrates, code computes** — the diagnostic and audit logic is deterministic Python; reasoning is rendered in plain language on top. Scores and verdicts are auditable, not model-guessed.
- **Fail loud, not silent** — the parser records any config line it doesn't recognize rather than discarding it, so meaningful configuration is never dropped unnoticed.
- **A conflict is not a link** — two devices claiming the same subnet are flagged as a conflict, not drawn as a connection; the topology tells the truth even when the config lies.
- **Bottom-up, stop at first fault** — the troubleshooter follows the OSI layers and stops at the lowest broken layer, because that is the root cause and everything above it is a symptom.
- **Live state overrides config at L1** — an operationally-down link is caught even when its configuration is correct, so the monitor and the troubleshooter never contradict each other.
- **General rules, not hardcoded** — every audit rule inspects parsed fields, so the tool works on unseen configs, not only the sample network.
- **Separate demo from ops** — the dashboard is a separate surface from the engine, which is independently testable and driven the same way from the CLI and the web server.

## Validation

- 41 automated tests (`python -m unittest discover tests`) — all passing
- Coverage: parsing (including malformed, empty, and non-IOS input), topology inference, every audit rule, the troubleshooting walk at each layer, live-state correlation, and graceful handling of bad input
- No-false-positive checks: strong SNMP communities and present enable secrets do not trigger findings
- The suite found and fixed a latent parser fault (a valid config line silently dropped) before deployment

## Rebuild-From-Prompt Protocol

Stack required:

 - Python 3.11+ (standard library only — no external dependencies)
 - A browser for the dashboard
 - Render (or any host that runs a Python process) for deployment

Build sequence:

1. Write sample Cisco IOS configs with deliberate, documented flaws (one router, switch, and firewall set)
2. Build the parser (IOS text → structured dict; unrecognized lines surfaced, not dropped)
3. Build the model (assemble topology; infer links by subnet and description; flag subnet-overlap conflicts)
4. Build the audit ruleset (security, correctness, drift; general rules against parsed fields)
5. Build the troubleshooter (bottom-up L1→L2→L3→ACL walk, stop at first fault, return root cause + IOS fix)
6. Build the monitor (simulated live state + fault injection)
7. Expose the engine over a stdlib HTTP API and serve the dashboard from it
8. Build the single-page dashboard; call the API with relative URLs so it works locally and deployed
9. Write a full test suite and gate on it before shipping
10. Deploy as one web service; serve API and dashboard from one origin

## Known Constraints

- The network is simulated — monitoring state is derived from configuration plus injected faults, not polled from real hardware over SNMP/SSH/ICMP
- Routing is computed by prefix length across connected, static, and OSPF-derived routes; OSPF cost is approximated by hop count
- ACL matching honors first-match-wins on source/destination subnets (and protocol/port when supplied); VLAN ACLs and non-contiguous wildcard masks are not modeled
- The public demo uses shared in-memory state — injected faults are visible to all viewers and reset on restart
- Free-tier hosting sleeps after inactivity; the first load after idle takes ~30–60 seconds to wake

## Iteration Notes

- Built end to end in a single focused session: configs → parser → model → audit → troubleshooter → monitor → API → dashboard
- The troubleshooter's live-state correlation was added after end-to-end testing exposed a contradiction — the monitor showing a link down while the troubleshooter called the path healthy
- A packaging bug (inconsistent module imports between CLI and API execution) was isolated by reproducing the engine call directly, rather than repeatedly editing code that wasn't the code actually running
- The configuration analysis is real; only the live-monitoring layer is simulated
