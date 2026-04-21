# crush sandbox

## description

This is a wrapper script around [crush](https://github.com/charmbracelet/crush) designed for modern Linux desktops.
It runs `crush` inside a highly restricted, ephemeral sandbox using `systemd-run` to protect your system from unintended modifications or access by the AI agent.

## features

- **Strict Sandboxing**: Leverages systemd's robust resource control and security features.
- **Smart Directory Mounting**: Automatically bind-mounts your current working directory (`$PWD`) so `crush` can do its job. It also mounts necessary config/state directories for `crush`, `go`, `nvim`, and your `SSH_AUTH_SOCK`.
- **API Key Checking**: Validates that at least one supported API key (e.g. `GEMINI_API_KEY`) is set in the environment before launching.
- **Environment Passthrough**: Safely passes necessary environment variables like `$PATH`, `$EDITOR`, and `$SSH_AUTH_SOCK` into the sandbox.
- **Extra Read-Only Mounts**: Allows passing additional file or directory paths as arguments, which will be bind-mounted into the sandbox as read-only.

## installation

1. Ensure you are running a Linux-based OS with `systemd` (standard on most modern distros).
2. Install the `crush` binary to `/usr/local/libexec/crush`.
3. Place this `crush` wrapper script in a directory in your `$PATH` (e.g., `/usr/local/bin/crush` or `~/.local/bin/crush`).
4. Make the script executable: `chmod +x /usr/local/bin/crush`

## usage

Navigate to the directory you want `crush` to work in, export a credential, and run the wrapper:

```bash
cd /path/to/my/project
 export GEMINI_API_KEY=AZBY...
crush
```

If you need `crush` to have read-only access to paths outside of the current directory (for example, reading a reference file or library), you can pass them as arguments:

```bash
crush /path/to/reference/code /another/read-only/path
```

## gotchas & limitations

- **Home Directory Restriction**: The script will intentionally refuse to start if you run it directly from your home directory (`$HOME`). This is a security measure to prevent accidental exposure of sensitive dotfiles or credentials. Run it inside specific project subdirectories instead.
- **Hidden Files**: Because `ProtectHome=tmpfs` is used, the sandbox will not be able to see files in your home directory unless they are explicitly bind-mounted by the script (like `~/.cache`, `~/.config/nvim`, etc.).
- **API Keys**: At least one supported API key must be set as an environment variable. The script will print a warning and exit if none are defined.
- **System Modifications**: Since `crush` runs with `ProtectSystem=strict` and limited privileges, it cannot install system-level packages, load kernel modules, or modify root file systems.
- **Path Dependency**: The script hardcodes the execution of the actual binary at `/usr/local/libexec/crush`. If your binary is located elsewhere, you will need to edit the script.
