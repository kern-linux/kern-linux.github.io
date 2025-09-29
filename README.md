# Kern

**Fast, Focused, Foundational.**

---

## Vision Statement

To define a modern, minimalist computing environment that prioritizes speed, focus, and user control by leveraging the Linux console as a first-class citizen. Kern is a philosophy of computing that rejects the complexity and resource overhead of traditional graphical desktop environments. It combines the power of the Linux kernel, the directness of the framebuffer, and the flexibility of modern TUI (Text-based User Interface) applications to create a cohesive, powerful, and entirely keyboard-driven workspace.

---

## Core Principles

1. **Console First:** The primary interface is the Linux Virtual Console, not a graphical display server like X11 or Wayland. All core interactions happen in this environment.

2. **Text as the Universal Interface:** TUI and CLI applications are not afterthoughts; they are the primary application model. The environment is optimized for displaying and interacting with text.

3. **Minimalism by Default:** The system starts with a minimal base (e.g., Arch Linux or Debian Netinstall). Every component is chosen deliberately. There is no bloat.

4. **Keyboard Driven:** Workflows are optimized for keyboard-centric operation, minimizing reliance on the mouse to achieve maximum speed and efficiency.

5. **Composition over Integration:** Following the Unix philosophy, the environment is built from simple, independent tools that work together. The user composes their ideal workflow rather than accepting a monolithic, pre-integrated desktop.

---

## Why Kern? Why Not Alternatives?

### Why not just use a lightweight tiling window manager (i3, dwm, Sway)?

Tiling window managers are a layer of optimization on top of a graphical stack. **Kern eliminates the stack itself.**

- **No Display Server:** Kern communicates directly with the kernel's framebuffer. We bypass X11 and Wayland entirely, freeing up hundreds of megabytes of RAM
- **No Dependencies:** This removes dependencies on graphics drivers, compositors, and display servers, resulting in a smaller footprint, faster boot times, and compatibility with older hardware

### Why not just run tmux/zellij in a regular terminal emulator?

- You absolutely can, and many users do—this is a valid stepping stone
- But you're still dependent on a GUI desktop environment running underneath
- Kern makes the console the *primary* interface, not an application running inside a desktop
- You gain true persistence: your workspace survives across system reboots without needing a full desktop session

### Why not use an existing minimal distro like Alpine or Void?

- Kern isn't a distribution—it's a methodology and configuration layer
- You *can* build Kern on Alpine, Void, Arch, or Debian
- Think of it like "Oh My Zsh" but for your entire computing environment
- The installation script handles the framebuffer setup, tool installation, and configuration regardless of base distro

### What about accessibility and graphics work?

- Kern isn't for everyone—it's optimized for text-heavy workflows (coding, writing, system administration, research)
- For CAD, video editing, or graphical design work, a traditional desktop remains the better choice
- However, many tasks work surprisingly well in this environment: documentation, presentations via Markdown, data visualization, and web development

---

## System Architecture

The Kern environment is composed of five distinct layers, building from the base hardware up to the application ecosystem.

### Layer 0: The Base System

- **Kernel:** Standard Linux Kernel
- **Distribution:** A minimal installation of a flexible distribution
  - **Recommended:** Arch Linux (for access to the latest packages and AUR) or Debian Netinstall (for rock-solid stability)
  - **Also Compatible:** Alpine Linux, Void Linux
- **Core Services:** Only essential system services are run. No graphical display manager, desktop services, or notification daemons are installed

### Layer 1: The Console Environment

- **Primary Interface:** A Framebuffer Terminal Emulator that runs directly on the Linux console, providing full Unicode font and color support
  - **MVP Component:** `fbterm` (for stability and ease of setup)
  - **Advanced Alternative:** `kmscon` (for more modern kernel integration)

### Layer 2: Session Management

- **"Window Manager":** A modern terminal multiplexer that provides persistent sessions, panes, tabs, and floating windows. This is the core of the user's workspace
  - **MVP Component:** `zellij` (for its powerful features, floating panes, and excellent out-of-the-box experience)
  - **Alternative:** `tmux` (for its ubiquity and extreme customizability)

### Layer 3: Application Ecosystem

- **Application Launcher:** A custom script combining a system-wide file index with `fzf` (fuzzy finder), bound to a key within `zellij` to provide a "Spotlight/Alfred-like" pop-up launcher

- **Core Applications:** A curated set of TUI/CLI tools to replace traditional GUI applications
  - **File Management:** `ranger` or `nnn`
  - **Text Editing:** `neovim` or `helix`
  - **Web Browsing:** `browsh` (for modern sites), `lynx` (for text-only)
  - **Productivity:** `taskwarrior` (tasks), `khal` (calendar), `neomutt` (email)
  - **System Monitoring:** `btop` or `bottom`
  - **Media Management:**
    - **Music:** `cmus` or `ncmpcpp` with `mpd`
    - **Podcast Client:** `castero` or `podboat`
    - **eBook Reader:** `epy` or `termpub`

### Layer 4: The Graphics & Multimedia Bridge

**Methodology:** Bypassing the terminal to render graphical content directly to the framebuffer via dedicated applications. The session is seamlessly restored upon exit.

**Integrated Document & Media Workflows:** Kern handles media through direct framebuffer applications and manages complex documents via a powerful command-line conversion pipeline. PDFs can be read as pure text (`pdftotext`) or viewed with full fidelity by converting pages to images (`pdftoppm` + `fbi`). Office documents and presentations are handled through `pandoc` and headless LibreOffice (`unoconv`), allowing you to stay in the console for over 90% of your workflow.

- **Image Viewing:** `fbi` or `fim`
- **Video Playback:** `mpv` (using the `--vo=drm` output driver)
- **Legacy/Emulation:** `dosbox` (using the `SDL_VIDEODRIVER=fbcon` environment variable)
- **Presentations:** Markdown → HTML slides via `mdp` or export to PDF

---

## Implementation & Distribution

### Delivery Model

Kern is distributed as an installation script (similar to Omakub) that transforms a minimal Linux installation into a complete Kern environment.

### Installation Process

1. User installs a minimal base system (Arch, Debian netinstall, Alpine, etc.)
2. Runs the Kern installer script: `curl -fsSL getkern.sh | bash`
3. Script handles:
   - Framebuffer terminal installation and configuration (fbterm/kmscon)
   - Zellij installation and default layouts
   - Core TUI application installation (ranger, neovim, etc.)
   - fzf launcher setup with keybindings
   - Automatic session restoration configuration
   - Custom `/etc/issue` branding
   - Shell profile integration

### Configuration Philosophy

- **Sensible defaults** that work immediately
- All configs stored in `~/.config/kern/` for easy customization
- **Modular:** users can opt-out of specific components
- Dotfiles managed via a simple CLI tool (`kern config`)

---

## User Experience (UX) Flow

1. **Boot:** The system boots in seconds to a customized, text-based TTY login screen
2. **Login:** The user logs in, which automatically executes their shell profile
3. **Session Start:** The profile script transparently launches `fbterm`, which in turn starts or attaches to the user's persistent `zellij` session
4. **Workspace:** The user is instantly dropped into their fully configured, multi-pane workspace exactly as they left it
5. **Interaction:** The user launches applications via the `fzf` pop-up, manages windows with `zellij` keybindings, and views media with framebuffer applications, all without leaving the console environment

---

## Remote Access & SSH

### How It Works

- **Local Usage:** Full framebuffer capabilities including image viewing (`fbi`) and video playback (`mpv`)
- **Remote Usage (SSH):** All text-based TUI/CLI applications work seamlessly
  - Attach to persistent `zellij` sessions: `zellij attach`
  - See your exact workspace state remotely
  - Framebuffer applications won't work over SSH (limitation of remote access)
  - Audio playback requires additional PulseAudio configuration (optional advanced setup)

### Mirrored Environments

Because all core applications are text-based, you can create identical Kern environments on multiple machines and switch between them seamlessly via SSH.

---

## Resource Footprint

### System Requirements

- **RAM at Idle:** 50-100 MB (compared to 1-2 GB for traditional desktop environments)
- **Boot Time:** Seconds (no display server initialization)
- **CPU Usage:** Minimal (no compositor, window manager, or desktop services)

### Ideal Use Cases

- **Older Hardware:** Breathe new life into machines 10-15 years old
- **Low-Power Systems:** Maximize battery life on laptops
- **Servers:** Efficient local administration interface
- **Resource Maximization:** Dedicate nearly 100% of system resources to actual work

---

## Future Development

### Roadmap

**Phase 1 (MVP):** Stable framebuffer environment with core TUI tools

**Phase 2:** Enhanced launcher with app categories and recent items

**Phase 3:** Sixel graphics integration for in-terminal image previews

**Phase 4:** Community application repository and configuration sharing

**Phase 5:** Remote session management tools (kern-ssh helper scripts)

### Community Goals

- If adoption grows, encourage TUI developers to optimize for framebuffer environments
- Build a curated application directory (`kern-apps`)
- Create pre-configured "profiles" for different workflows (developer, writer, sysadmin)
- Develop GUI-to-TUI migration guides for common workflows

---

## Technical Notes

### Virtual Consoles (TTYs)

Linux provides multiple independent terminal sessions accessible via `Ctrl + Alt + F1` through `F6`. Each virtual console (`/dev/tty1`, `/dev/tty2`, etc.) can run separate login sessions, providing kernel-level multitasking without any window manager.

### Graphics Standards

- **Framebuffer (`/dev/fb0`):** Direct pixel access to display hardware
- **Sixel:** Terminal graphics protocol for embedding images in text streams (future enhancement)
- **DRM (Direct Rendering Manager):** Modern kernel interface used by `mpv` for video playback

### Login Screen Customization

The TTY login screen is controlled by `/etc/issue`. You can customize it with:
- ASCII art (via `figlet`)
- ANSI color codes
- System information variables (`\n` for hostname, `\d` for date)
- Centered layouts using blank lines

---

## Getting Started

1. Install a minimal Linux distribution (recommended: Arch Linux or Debian netinstall)
2. Boot to console and log in
3. Run: `curl -fsSL https://getkern.sh | bash`
4. Follow the interactive setup prompts
5. Log out and back in to start your Kern session

---

## Philosophy

Kern represents a return to computing fundamentals: direct hardware access, composable tools, and user control. It's not about rejecting graphical interfaces entirely, but about questioning whether they're necessary for every task. For text-heavy workflows—coding, writing, system administration, research—the console provides a faster, lighter, and more focused environment than any desktop.

By building on proven Unix principles and modern TUI applications, Kern creates a computing experience that is simultaneously retro and cutting-edge: the simplicity of 1980s terminals meets the power of 2020s tooling.

**Fast. Focused. Foundational.**
