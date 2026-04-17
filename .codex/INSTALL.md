# Installing ai-plugins for Codex

Enable skills in Codex via native skill discovery. Just clone and symlink.

## Prerequisites

- Git

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/nguyenthanhson/ai-plugins.git ~/.codex/ai-plugins
   ```

2. **Create the skills symlinks:**
   ```bash
   mkdir -p ~/.agents/skills
   ln -s ~/.codex/ai-plugins/plugins/atlassian-tools/skills ~/.agents/skills/atlassian-tools
   ln -s ~/.codex/ai-plugins/plugins/personal-software-engineer/skills ~/.agents/skills/personal-software-engineer
   ```

   **Windows (PowerShell):**
   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
   cmd /c mklink /J "$env:USERPROFILE\.agents\skills\atlassian-tools" "$env:USERPROFILE\.codex\ai-plugins\plugins\atlassian-tools\skills"
   cmd /c mklink /J "$env:USERPROFILE\.agents\skills\personal-software-engineer" "$env:USERPROFILE\.codex\ai-plugins\plugins\personal-software-engineer\skills"
   ```

3. **Restart Codex** (quit and relaunch the CLI) to discover the skills.

## Verify

```bash
ls -la ~/.agents/skills/atlassian-tools
ls -la ~/.agents/skills/personal-software-engineer
```

You should see symlinks pointing to the respective skills directories.

## Updating

```bash
cd ~/.codex/ai-plugins && git pull
```

Skills update instantly through the symlinks.

## Uninstalling

```bash
rm ~/.agents/skills/atlassian-tools
rm ~/.agents/skills/personal-software-engineer
```

Optionally delete the clone: `rm -rf ~/.codex/ai-plugins`
