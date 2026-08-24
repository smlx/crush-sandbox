# Crush Sandbox

An ephemeral [systemd-run](https://www.freedesktop.org/software/systemd/man/latest/systemd-run.html) sandbox for [crush](https://github.com/charmbracelet/crush) on Linux workstations. It restricts `crush` to protect your system from unintended agentic modifications. 🙈

## Features

- 🛡️ **Sandboxing**: Isolated via systemd's robust resource controls (`ProtectSystem=strict`, `ProtectHome=tmpfs`).
- 📂 **Environment Variables and Bind Mounts**: Auto-binds e.g. `$PWD`, config/state dirs, and `SSH_AUTH_SOCK`.
- 🔑 **API Key Check**: Requires a valid provider key (e.g., `GEMINI_API_KEY`) before launching.
- 🔗 **Extra Read-Only Paths**: Pass additional paths as arguments for safe, read-only access.

## Prerequisites

Ensure `systemd` is in use, and `xdg-dbus-proxy` is installed.

## Installation

1. Install the upstream `crush` binary to `/usr/local/libexec/crush`.
1. Install the user service template:

   ```bash
   mkdir -p ~/.config/systemd/user
   cp crush-proxy@.service ~/.config/systemd/user/
   systemctl --user daemon-reload
   ```

1. Place the wrapper script in your `$PATH` and make it executable:

   ```bash
   cp crush ~/.local/bin/
   chmod +x /usr/local/bin/crush
   ```

## Usage

Export a credential and run inside your project directory:

```bash
cd /path/to/project
 export GEMINI_API_KEY=AZBY... # optional export variable
crush [optional/read-only/paths...]
```

## Gotchas & Limitations

The sandbox results in some limitations:

- 🚫 **No Home Directory Executions**: Execution in `$HOME` is blocked to prevent sensitive data exposure. Run inside project subdirectories only!
- 🙈 **Hidden Home Files**: `$HOME` is generally inaccessible unless explicitly mounted by the script.
- 📦 **No System Mods**: Cannot install system packages or modify most directories.
- 📍 **Hardcoded Path**: Expects the `crush` binary at `/usr/local/libexec/crush`.
