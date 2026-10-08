# Natively Assistant — Build, Install & Run Guide

Comprehensive guide to running Natively locally in development, generating installers for **macOS (`.dmg`)** and **Windows (`.exe`)**, and automating cloud builds directly from **GitHub Releases**.

---

## Table of Contents
1. [System Prerequisites](#1-system-prerequisites)
2. [Running Locally in Development](#2-running-locally-in-development)
3. [Building macOS Installers Locally (`.dmg`)](#3-building-macos-installers-locally-dmg)
4. [Building Windows Installers (`.exe`)](#4-building-windows-installers-exe)
5. [Automated GitHub Releases (Mac & Windows)](#5-automated-github-releases-mac--windows)
6. [Installing & Bypassing OS Security Checks](#6-installing--bypassing-os-security-checks)
7. [App Configuration & Defaults](#7-app-configuration--defaults)

---

## 1. System Prerequisites

Ensure your system has the required runtimes installed:

* **Node.js**: `v22.13.0` or newer
* **npm**: `v10.x` or newer
* **Rust & Cargo**: Required for compiling the native audio/stealth module (`native-module/`)
  * **macOS**: `brew install rust` or `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
  * **Windows**: Download Rust installer from [rustup.rs](https://rustup.rs) + install Visual Studio Build Tools (C++ workload).

Verify your toolchain:
```bash
node -v    # Must be >= 22.13.0
npm -v
cargo -v
```

---

## 2. Running Locally in Development

To start the app in development mode with hot-reloading:

1. **Install dependencies**:
   ```bash
   npm install
   ```

2. **Start the development server and Electron app**:
   ```bash
   npm run app:dev
   ```
   * This command launches the Vite frontend server on `http://127.0.0.1:5180` and starts the Electron main process.
   * If you close the window, you can restart it anytime with `npm run app:dev`.

---

## 3. Building macOS Installers Locally (`.dmg`)

You can generate production-ready `.dmg` installers directly on your Mac.

### Option A: Build for your Mac's architecture (Fastest)
Builds specifically for your current Mac (Apple Silicon `arm64` or Intel `x64`):
```bash
npm run app:build:local
```

### Option B: Build Universal Packages (Both arm64 and x64)
Builds DMG installers for both Apple Silicon and Intel machines:
```bash
npm run app:build:mac
```

### Output Location:
All generated packages will be located in the `release/` directory:
* `release/corespeechd-2.9.2-arm64.dmg` *(Apple Silicon)*
* `release/corespeechd-2.9.2-x64.dmg` *(Intel)*
* `release/*.zip` *(Portable archives)*

> **Note on Process Disguise:**
> For stealth, the on-disk application identity is named `corespeechd` (a native macOS audio daemon name). The UI display title remains `Natively`.

---

## 4. Building Windows Installers (`.exe`)

Electron native modules (`better-sqlite3`, `keytar`, and native Rust keyboard hooks) require Windows MSVC headers and Windows DLLs to compile. 

### Method 1: Automated via GitHub Actions (Recommended)
You do **not** need a Windows machine. Pushing to GitHub will build the Windows installer on a cloud Windows runner automatically (see [Section 5](#5-automated-github-releases-mac--windows)).

### Method 2: Locally on a Windows PC
If you are on a Windows machine:
1. Open PowerShell or Command Prompt as Administrator.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run the Windows packaging script:
   ```bash
   npm run app:build:win
   ```
4. Output files in `release/`:
   * `release/audiodg-Setup-2.9.2.exe` *(Full NSIS Windows Setup Installer)*
   * `release/audiodg 2.9.2.exe` *(Portable standalone executable)*

---

## 5. Automated GitHub Releases (Mac & Windows)

The repository includes a GitHub Actions release pipeline configured at:
[`.github/workflows/release.yml`](.github/workflows/release.yml)

Every time you push or tag, GitHub will run a parallel matrix build on:
* **macOS Runner (`macos-latest`)** ➔ Builds `.dmg` and `.zip`
* **Windows Runner (`windows-latest`)** ➔ Builds `.exe` installers

### How to trigger automated releases:

#### Option 1: Official Versioned Release (Tag)
To publish a formal release:
```bash
git add .
git commit -m "Release version 2.9.2"
git push origin main

# Tag and push:
git tag v2.9.2
git push origin v2.9.2
```
GitHub Actions will automatically build both operating systems and create a Release under your repository's **Releases** tab with all installer files attached.

#### Option 2: Continuous Release on Every Commit (`main`)
Every push to the `main` branch automatically builds and updates a **Latest Automated Build** pre-release so you always have direct download links to the newest binaries.

#### Option 3: Manual 1-Click Trigger
1. Go to your GitHub repository in your browser.
2. Click on the **Actions** tab.
3. Select **Build & Release (macOS & Windows)** from the left sidebar.
4. Click **Run workflow** ➔ Click the green button.

---

## 6. Installing & Bypassing OS Security Checks

Because self-built installers are ad-hoc signed (and not signed with a paid $99/yr Apple Developer or Microsoft EV code-signing certificate), operating system security filters require a one-time approval.

### macOS (Gatekeeper / Quarantine)
If macOS displays *"corespeechd is damaged and cannot be opened"* or *"unidentified developer"*:

1. Drag `corespeechd.app` from the DMG into `/Applications`.
2. Open Terminal and run:
   ```bash
   xattr -cr /Applications/corespeechd.app
   ```
3. *Alternative GUI method:* Right-click `corespeechd.app` in Finder, hold `Option`, click **Open**, and then click **Open**.

### Windows (Microsoft Defender SmartScreen)
If Windows SmartScreen displays *"Windows protected your PC"*:
1. Click **More info**.
2. Click **Run anyway**.

---

## 7. App Configuration & Defaults

The application is pre-configured with the following unmetered defaults:

* **Subscription Plan**: **Ultra** (highest tier enabled by default across all screens and IPC handlers).
* **Usage Quota**: Unmetered / unlimited for:
  * AI Queries & Smart Vision
  * Voice Audio Transcription
  * Realtime Web & Company Research
  * Knowledge Base Sync
* **Trial Expiration**: Completely disabled (no countdown timers, trial modals, or lockouts).
* **Stealth / Undetectable Mode**: Enabled by default (hidden from task switches and proctoring capture tools).
* **API Keys (Optional)**:
  * You can enter your OpenAI, Groq, Anthropic Claude, or Gemini API keys in the app's **Settings** (`Cmd/Ctrl + ,`), or by creating a `.env` file in the project root:
    ```env
    GROQ_API_KEY=your_groq_key
    OPENAI_API_KEY=your_openai_key
    GEMINI_API_KEY=your_gemini_key
    CLAUDE_API_KEY=your_claude_key
    ```
