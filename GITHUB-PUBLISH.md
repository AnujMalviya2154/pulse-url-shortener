# Publish the Pulse M0 foundation to GitHub

The local project has been initialized on branch `main` and contains a two-commit documentation history. The Git bundle preserves that history. No GitHub remote is configured yet.

## Recommended: publish the Git bundle

1. Create a new **empty** repository under your GitHub account. Suggested repository name: `pulse-url-shortener`. Choose public or private as you prefer. Do not initialize it with a README, license or `.gitignore` because the bundle already contains the first commit.
2. Download `Pulse-M0-foundation.bundle` from the handoff.
3. In a terminal, replace the bundle path with its actual downloaded location:

```bash
git clone /path/to/Pulse-M0-foundation.bundle pulse
cd pulse
git remote add origin https://github.com/AnujMalviya2154/pulse-url-shortener.git
git push -u origin main
```

4. Open the new repository and confirm that `README.md`, `AGENTS.md`, `docs/` and `.agents/rules/` are present.
5. Open that cloned folder as the workspace root in Antigravity.

If you choose a different repository name, update the remote URL accordingly. The GitHub account/repository URL above is a command template based on the owner's previously supplied GitHub username; the new repository URL is not yet verified because the repository has not been created.

## Alternative: publish from the ZIP

The ZIP contains project files but intentionally excludes the `.git` directory. After extracting it:

```bash
cd pulse-foundation
git init -b main
git add .
git commit -m "docs: establish Pulse M0 foundation"
git remote add origin https://github.com/AnujMalviya2154/pulse-url-shortener.git
git push -u origin main
```

Use the bundle route if you want to preserve the prepared commit history.
