# Build Plan & Specification: panelette (macOS GUI for `scrcpy` & `ADB`)

## 1. Project Identity & Target Environment
- **App Name:** `panelette`
- **Target Runtime for Current Build:** Intel (`x86_64`) Mac running **macOS 12.6.7 (Monterey)** (Mid-2015 15" Retina MacBook Pro, model `MJLT2`).
- **Future Portability:** Structure code cleanly so the UI and core logic can later target additional desktop platforms (Windows/Linux), keeping macOS-specific windowing, hotkey (`Cmd + ,`), and binary bundling isolated in dedicated modules.
- **Recommended Stack:** **Tauri** (Rust backend + lightweight TypeScript/HTML/CSS frontend) so the app stays compact, supports rich custom CSS animations/transitions, and compiles to a standalone macOS `.app` bundle.

## 2. Bundling & Offline-Ready Rule
- Bundle all required UI assets, fonts/icons, and platform binaries (`adb`, `scrcpy`, and `scrcpy-server` built for `x86_64` macOS) inside the application bundle so nothing has to be downloaded on first run.
- Avoid external CDN links in the UI; load styles, scripts, and icons locally from bundled resources.
- Target a ready-to-run, self-contained `.app` bundle for Intel macOS 12.6.7.

## 3. Visual Direction & Design System
- **Inspiration / Base System:** Use **Tabler** (`https://github.com/tabler/tabler`) as a structural and visual reference/foundation—clean cards, crisp borders, structured tables/lists, badges, and clear hierarchy—customized with a distinct, refined, lightweight aesthetic and subtle, purposeful animations (respecting `prefers-reduced-motion`).
- **Icons:** Use **Tabler Icons** (`https://github.com/tabler/tabler-icons`)—minimal, consistent stroke icons bundled locally (no heavy icon fonts if inline/sprite SVGs are cleaner and smaller).
- **Color Palette:** Neutral surfaces and text as the primary palette (light/dark adaptable), with **one restrained accent color** reserved strictly for active states, primary actions, connected indicators, or key focal elements.

## 4. Core Views, Controls & Features

### A. Main Mirror / Display Control & Navigation Bar
- Use a **standard macOS window** with native **traffic light buttons** (close, minimize, zoom).
- Provide easy-to-use controls for core `scrcpy` parameters (such as **Resolution / Max Size**, **Bitrate**, **FPS**, orientation/rotation, stay-awake, turn-screen-off, borderless/always-on-top options):
  - Each numeric/stepped parameter must pair a **slider** (or quick preset ticks) with a **direct numeric text input** so users can either drag or type exact values and keep both synchronized and validated.
- Include an **Android navigation bar** strip at the bottom of the window with the 3 classic actions: **Recent (Overview)**, **Home**, and **Back**, clearly styled and separated from desktop controls.

### B. Preferences Window (`Cmd + ,`)
- Accessible via the standard macOS shortcut **`Command + ,`** (and via the macOS app menu).
- Include sections for:
  1. **Appearance:** Theme/palette options, accent/density or animation preferences.
  2. **File Transfer Destination:** Default local folder picker for pulled files/screenshots/recordings.
  3. **Presets / Profiles:** Create, edit, duplicate, delete, and apply named parameter profiles (e.g., *Low Latency / High FPS*, *Crisp Mirror*, *Low Bandwidth Wireless*) with custom parameter values.
  4. **General / Tool Settings:** Default ADB/scrcpy behaviors, connection timeouts, logging/diagnostics options.

### C. ADB Tools Section / Tab
- Include a dedicated section/tab/page for ADB utilities:
  - **Connected Devices:** List connected USB/TCP devices, status, and quick connect/disconnect actions.
  - **File Manager / Transfer:** Browse Android directories, push files from Mac to device, and pull files from device to the configured destination folder.
  - **App Management:** List installed packages (user/system filter), install APKs, launch/force-stop apps, clear data/cache where applicable, or uninstall apps safely with confirmation.

---

## 5. Phased Execution Rules (Strict Workflow)

### Phase 1: GUI & Design Prototype Only (No Backend Execution Yet)
- Build the **entire visual interface first**: all windows, panels, tabs, parameter sliders + typed inputs, Android 3-button nav bar, ADB tools views, and `Cmd + ,` Preferences modal/window—visually matching the intended final app.
- Use mock/placeholder state only; **do not wire live `adb` or `scrcpy` commands yet**.
- Package/build a runnable prototype for **macOS 12.6.7 (`x86_64`)** so the UI can be inspected and approved before Phase 2 starts.
- **Stop after delivering the Phase 1 prototype and wait for GUI approval before beginning Phase 2.**

### Phase 2: Backend & System Integration (After Phase 1 Approval)
Implement the backend in **4 structured stages** based on proximity to the device and added capabilities:
1. **Stage 1 — Layer 1 (Phone / Device Side):** Device detection, authorization/state checks, on-device `scrcpy-server` lifecycle, Android keyevents/navigation (`Back`, `Home`, `Recent`), remote filesystem queries, and package/app state commands.
2. **Stage 2 — Layer 2 (Transport & Bridge Middle):** ADB server/transport handling (USB and TCP/IP), binary/resource resolution inside the `.app` bundle, process spawning, stdout/stderr stream capture, error parsing, and file transfer streams.
3. **Stage 3 — Layer 3 (Computer / macOS Host Side):** macOS window management, `Cmd + ,` native menu/shortcut binding, local filesystem/folder permissions and transfer destination handling, preset/profile persistence, and `scrcpy` session supervision.
4. **Stage 4 — Added Features & Polish:** Quality-of-life extras such as quick wireless ADB pairing/connect helpers, command/log viewer, transfer progress indicators, profile import/export, and edge-case recovery.

### Chunking & Self-Review Protocol
- Work in **small, logical chunks** (one component, view, or backend module at a time).
- **After every single step:** perform **1 review pass** (check for bugs, broken types, missing edge cases, macOS 12 / Intel `x86_64` compatibility issues, and UI/UX rough edges; fix issues before moving on).
- **After every 3rd step:** perform **2 thorough review passes** (audit cross-file integration, state consistency, resource cleanup/process termination, and bundle/build health).