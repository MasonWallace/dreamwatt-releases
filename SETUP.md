# Dreamwatt — Setup Guide

*Your computer earns while it dreams.* Dreamwatt lets a dashboard use a laptop's spare power **while the owner is away from it** — or, if the owner chooses, lends the whole machine out. Two roles:

- **Owner / host** — a laptop or desktop running the Dreamwatt agent (Mac, Windows, Linux). Phones and iPads can't host.
- **Dashboard** — the web page where machines show up, jobs get sent, and coins are tracked. Any browser, phone included.

Nothing runs on a host until its owner turns sharing on. In Idle Sharing, anything running stops the instant they touch the keyboard or mouse. In Remote Mode they've chosen to hand over the screen, and a banner says so the whole time.

**Coins:** every rented minute pays the owner in Electricity Coins. They are **test credit** — there are no renter accounts yet, so nobody is charged; the ledger just shows what each machine would have earned.

---

## Part A — Mason: run the coordinator (one machine, once)

1. Terminal:
   ```
   cd ~/Projects/idlecloud
   ./run.sh
   ```
2. It prints `dashboard http://localhost:8399/?key=XXXX`. Open it; the key is remembered by your browser.
3. Leave that window open — it's what every machine connects to.
4. **Machines on your Wi-Fi** pair with the printed `LAN agents pair with: 192.168.x.x:8399`.
5. **Machines anywhere else** need the tunnel. Second Terminal:
   ```
   cd ~/Projects/idlecloud
   ./tunnel.sh
   ```
   It prints `https://something.trycloudflare.com` — that's the address they pair with. Close it after the demo; it makes the dashboard reachable from the internet (behind the key).

## Part B — Owner: install the agent

### Windows
1. Download `dreamwatt-agent-windows-amd64.exe` (from the repo's Releases or `dist/`).
2. Put it somewhere permanent, e.g. `C:\Dreamwatt\`, and double-click it.
3. SmartScreen will say "Windows protected your PC" (the file isn't signed yet): **More info → Run anyway**.
4. A small icon appears in the system tray (bottom-right; click `^` if hidden). Click it → **Open Dreamwatt** → `http://127.0.0.1:47831`. Only your own computer can open this page.

### Mac
1. Download `Dreamwatt.app` (or `dreamwatt-agent-macos-arm64`). Move the app to Applications and open it.
2. If macOS blocks it: System Settings → Privacy & Security → **Open Anyway**.
3. The Dreamwatt D appears in the menu bar (top-right). Click it → **Open Dreamwatt**.

### Linux
`chmod +x dreamwatt-agent-linux-amd64 && ./dreamwatt-agent-linux-amd64`. Idle detection needs `xprintidle` (X11); Wayland isn't supported yet.

## Part C — Owner: pair with the dashboard

1. On the dashboard, go to **My machines** and click **+ Add a machine**. A 6-digit code appears (5 minutes, single use).
2. In Dreamwatt on your machine, go to **Connect → Add to your dashboard**: the address from Part A (`192.168.x.x:8399` on the same Wi-Fi, or the `https://…trycloudflare.com` URL) + the code → **Pair**. The status box turns to *Connected*.
3. Your machine appears on the dashboard with its hardware, location (city-level, if you allow it), latency, and your price.

Command line (handy over Remote Desktop/SSH): `dreamwatt-agent --coordinator <address> --pair 123456`

## Part D — Owner: your rules

Everything below is enforced by the agent on your machine. The dashboard can't override it; the one exception is your price, which you can also set from the dashboard while the machine is online.

| Rule | What it does |
|---|---|
| **Share this machine** | Master switch. Off = nothing runs, ever. |
| **While I'm away / Lend the whole computer** | While I'm away = jobs only while you're away. Lend = you lend the whole machine (screen + input). See Part F. |
| **Only after I've been away for** | No keyboard/mouse for this long before sharing starts. Any input stops it instantly. |
| **Only when plugged in** | Never drains the battery. |
| **When it can work** | A weekly grid: which hours sharing may happen at all. |
| **Stay awake** | "Not now": no jobs for 1 hour, or until 8 am tomorrow (menu bar). |
| **Sleep** (automatic, no setting) | While a job or remote session is running, the computer won't go to sleep by itself. It can sleep again as soon as that ends. The screen can still turn off, and closing a laptop lid still puts it to sleep. |
| **Stop if it gets hotter than** | Stops work above this temperature. |
| **What it can do: AI · Compute · Encrypted storage and memory** | Turn individual job kinds off. |
| **Share approximate location** | Lets the dashboard show your city and sort by distance. Off = country only. The coordinator uses your connection's IP; nothing on your machine reads GPS. |
| **Shared folders** | Folders the dashboard may read/write. Default: none. Hidden files are never served. |
| **Virtual machine** | Allow a sandboxed Ubuntu VM, with caps on RAM, CPUs and disk. Suspended the moment you come back in While I'm away mode. |
| **Price** | Your price in coins per rented hour, or follow the suggestion (base rate for your hardware × current demand: Quiet 0.75× · Steady 1× · Busy 1.17× · Peak 1.3×). |

Dreamwatt always says what's happening in plain words: *Sharing off*, *Awake* (with the reason: "input 3s ago", "on battery", "outside your schedule", "cpu 95% > 80%"), *Staying awake*, *Dreaming*, *Dreaming and earning*, *Lent out*. Work also won't start above 80% CPU load; that limit isn't a setting yet.

## Part E — Dashboard: send jobs

| Tab / button | What it does | Note |
|---|---|---|
| **AI** | Prompt → the host's local Ollama model, streamed back | The host *can* read the prompt |
| **CPU** | Hashes N MB on the host; the coordinator verifies the answer | Proves honest work |
| **Storage** | Text is encrypted on the coordinator and stored as ciphertext on a host; fetch shows ROUND-TRIP MATCH | The host never has the key |
| **RAM** | Hold a value in a host's memory, read it back | Gone when the agent quits |
| **Files** (card button) | Browse, download, upload, delete inside the owner's shared folders | Only what the owner shared |
| **VM** (card button) | Launch an Ubuntu VM within the owner's caps, run commands, open a web terminal, suspend/resume/delete | Your code lives only in the VM |
| **Remote** (card button) | Part F | |

Sort machines by **Latency**, **Distance from me** (asks your browser for location only when you click it), or **Name**. Leave the device picker on **auto** and the scheduler picks the idle machine with the lowest load; if its owner comes back mid-job, the log shows *interrupted: host busy (input 0s ago)* and the job moves elsewhere. Every completed minute credits the owner's coins; the **Coins** panel shows per-machine earnings and today's demand.

## Part F — Remote Mode: screen, control, gaming

Remote Mode = the owner lends their whole computer. You see their screen and use their keyboard and mouse through **Moonlight** (free: Windows, Mac, iPad, iPhone, Android, TV). With a gaming PC on the host side, this is also how you play their games remotely.

**Owner, once:**
1. In Dreamwatt, under **Lending**, choose **Lend the whole computer**. On the card, click **Remote → Install**. The agent installs **Sunshine** (the streaming host) and **Tailscale** (so the connection works from anywhere without router setup). Windows will show admin prompts; macOS a password prompt — that's you approving it.
2. macOS only: System Settings → Privacy & Security → **Screen Recording** → allow Sunshine (and **Accessibility** for input), then restart Sunshine.
3. Sign in to Tailscale when it opens. (On the same Wi-Fi it works without Tailscale.)

**Every session:**
1. Dashboard → the card's **Remote** → **Start session** (type your name). The card shows the address(es) to use.
2. Open Moonlight on your device → **Add host** → enter that address. Moonlight shows a 4-digit PIN.
3. Type the PIN into the dashboard's **PIN** box → **Pair**. Then in Moonlight open *Desktop* (or a game).
4. On the owner's machine the menu bar/tray shows **● REMOTE: your name** the whole time and they get a notification. They can end the session from the tray; you can end it from the dashboard. The session is logged on their machine and pays coins from first sight to end.

Latency: same city ≈ 20–40 ms (fine for most games), cross-country noticeably more. Pick the nearest machine using the **Latency** sort.

## What leaves a host machine
Hardware summary, CPU/RAM/temperature numbers, job status and results, device name, price, and (if allowed) a city-level location estimate made by the coordinator from the connection. Idle detection reads one number — seconds since last input — never what was typed. The agent runs only its built-in job kinds; it never executes downloaded code on the host; customer code runs only inside the VM.

## Troubleshooting
- **Device never shows up** — check the coordinator address (`host:8399` or the tunnel URL, no `http://` needed). Codes expire in 5 minutes.
- **"Awake: cpu 90% > 80%"** — something else is using that machine's CPU; it goes back to dreaming when the load drops. The dashboard shows the same reason as *Host busy*.
- **"Awake: outside your schedule"** — the owner's weekly schedule doesn't allow sharing right now.
- **AI job stays queued** — the host needs Ollama with the model pulled (`ollama pull qwen2.5:3b-instruct`). Machines without Ollama never get AI jobs.
- **Files or VM job stays queued** — those kinds are dispatched only while the machine is Available or in a remote session; a busy owner's laptop holds them until it's idle.
- **Moonlight can't find the host** — same Wi-Fi: use the LAN address; elsewhere: both sides signed in to Tailscale, use the `100.x.y.z` address. Pair the PIN within a minute of Moonlight showing it.
- **Sunshine runs but shows a black screen (Mac)** — Screen Recording permission wasn't granted; grant it and restart Sunshine.
- **Windows: no tray icon** — run `dreamwatt-agent-windows-amd64-console.exe` to see the log; it's also in `%AppData%\Dreamwatt\agent.log` (Mac: `~/Library/Application Support/Dreamwatt/agent.log`).
- **Reset a device** — dashboard card → *forget*; delete the agent's `config.json` in that folder; re-pair.
