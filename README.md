# Crush Sandbox

An ephemeral [systemd-run](https://www.freedesktop.org/software/systemd/man/latest/systemd-run.html) sandbox for [crush](https://github.com/charmbracelet/crush) on Linux workstations. It restricts `crush` to protect your system from unintended agentic modifications. 🙈

## Features

- Run `crush` in a systemd-run sandbox that restricts access to your system and most paths in `$HOME` outside the local working directory.
- Configurable filtering of dbus comms to e.g. support desktop notifications while restricting access to the user keyring.
- Automatically handle common config/state dirs, environment variables like `SSH_AUTH_SOCK`, and other necessary config files.
- Support API keys from various providers either predefined in an environment variable or dynamically loaded from a password manager.
- Mount additional paths as arguments to provide read-only access to other directories.

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

1. Run `crush`.
2. Configure the launch environment using the interactive prompt.
3. Launch crush.

## Dynamic loading of credentials

Crush supports OAuth2, so you can get a token dynamically from within crush for many providers.

Two that don't support OAuth2:

1. Gemini API: needs an API key.
1. GitHub MCP: needs a PAT.

Those are supported by defining a function `crush_api_key_command_xxxx` or a function `github_pat_command_xxxx` that returns the token as a string.
The `xxxx` can be any string: the script dynamically loads any function matching this pattern and sets either `XXXX_API_KEY` or `GITHUB_PAT` respectively.

These can then be added via `crushrc`:

```bash
provider add gemini --api-key "$GEMINI_API_KEY"

mcp add github \
	--type http \
	--header Authorization "Bearer $GITHUB_PAT" \
	--url "https://api.githubcopilot.com/mcp/"
```

## Gotchas & Limitations

- Execution in `$HOME` is blocked to prevent sensitive data exposure. The crush sandbox is designed to run in project directories only.
