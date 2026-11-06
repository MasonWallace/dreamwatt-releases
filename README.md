# Dreamwatt — downloads

**Your computer earns while it dreams.** Dreamwatt is a small tray/menu-bar app that lets a dashboard use your laptop's spare power only while you're away from it — or lends the whole machine out when you choose Remote Mode. You set the rules and the price. Nothing runs until you turn sharing on; any keypress stops it.

Proof-of-concept builds. **Unsigned** — your OS will warn once.

| Platform | File | First run |
|---|---|---|
| Windows 10/11 (64-bit) | `dreamwatt-agent-windows-amd64.exe` | SmartScreen: *More info → Run anyway*. A tray icon appears; click it → *Open device panel*. |
| macOS (Apple Silicon) | `Dreamwatt-macOS-arm64.zip` | Unzip, move to Applications, open. If blocked: System Settings → Privacy & Security → *Open Anyway*. Cloud icon in the menu bar. |
| Linux (x86-64) | `dreamwatt-agent-linux-amd64` | `chmod +x` and run; needs `xprintidle` for idle detection. |
| Windows, with a log window | `dreamwatt-agent-windows-amd64-console.exe` | Same app, shows what it's doing. |

Your panel opens at `http://127.0.0.1:47831` — only your own computer can reach it. To join a dashboard you need a 6-digit pairing code from whoever runs it.

**What leaves your machine:** a hardware summary, CPU/RAM/temperature numbers, job status, your device name and price, and — only if you allow it — a city-level location estimate. Idle detection reads one number (seconds since your last input), never what you typed. The app runs only its built-in job kinds; it never executes downloaded code on your computer.

Full guide: `SETUP.md` in this repo. Source is in a private repository for now.
