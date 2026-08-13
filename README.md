# dotfiles

My personal dotfiles, managed with [chezmoi](https://chezmoi.io). Cross-platform:
**macOS, Fedora, Ubuntu/Debian**.

Forked from [Francis-Gurr/dotfiles](https://github.com/Francis-Gurr/dotfiles); pull upstream
changes with `git fetch upstream && git merge upstream/main`.

## Bootstrap a new machine

One command installs chezmoi, clones this repo, and applies everything:

```sh
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply LeynaM
```

What it does, in order. Run scripts are numbered `00`–`05` before dotfiles/externals are
applied to disk, and `10`–`15` after, so the gap marks the phase boundary:

**Before** (env + credentials, so GitHub auth has a ready browser and vault to work with):

1. `00` **Install packages** — per OS: `dnf` + the `scottames/ghostty` COPR on Fedora, `apt` on
   Debian/Ubuntu, Homebrew (incl. the ghostty cask) on macOS. Also installs starship.
2. `01` **System settings** — GNOME dark mode + US keyboard layout (Linux only, skipped if
   `gsettings` isn't present).
3. `02` **Bitwarden** — installs the Bitwarden desktop app (Flatpak on Linux, Homebrew cask on
   macOS) so vault-stored credentials are available before GitHub auth.
4. `03` **Firefox Sync sign-in** — opens `about:preferences#sync` and waits for you to sign in
   (skipped if already signed in), so bookmarks/history/tabs/extensions are in place before
   GitHub auth opens a browser.
5. `04` **Firefox UI prefs** — applies saved toolbar/sidebar layout and spellcheck dictionaries
   to the profile from the previous step.
6. `05` **GitHub auth** — `gh auth login` over SSH (generates/uploads a key), sets
   `git_protocol ssh`, and repoints this repo's remote to SSH.

**Apply** — dotfiles and externals (zsh plugins, FiraCode Nerd Font) are deployed to disk.

**After** (now that dotfiles/externals exist on disk):

7. `10` **Default shell** — switches your login shell to zsh.
8. `11` **Font cache** — `fc-cache` on Linux, so the just-deployed Nerd Font renders.
9. `12` **commitlint** — installs pnpm + a Node runtime and enables the commit-msg hook in
   this repo.
10. `13` **nvim providers** — installs the Python/Node providers Neovim needs.
11. `14` **ttyper** — typing-practice tool setup.
12. `15` **Homeserver enroll** — best-effort SSH key enrollment when on the home network;
    never fails the apply.

Steps 1, 3, 4 and 6 are interactive (sudo password / Bitwarden unlock / Firefox sign-in /
browser login).

`homeserver`, `homeserver-git` and `backuppi` in `~/.ssh/config` resolve over the wired home
LAN (`homeserver` → `homeserver.localdomain`), so no VPN or overlay network is needed. Off the
home network they will not resolve.

## Day-to-day

The source tree lives at `~/.local/share/chezmoi` (`chezmoi cd` to jump there).

```sh
chezmoi edit ~/.zshrc      # edit the source for a file
chezmoi apply              # apply pending changes to $HOME
chezmoi diff               # preview what apply would change
chezmoi add ~/.config/foo  # start tracking a new file
chezmoi re-add             # pull edits made directly in $HOME back into the source
chezmoi update             # git pull + apply
```

## Adding a tool

1. Track its config: `chezmoi add ~/.config/<tool>/...`
2. Add its package name(s) to `.chezmoidata.yaml` (`common`, or a per-OS list if the name differs).
   For anything needing a special install (a repo, cask, or script), edit
   `run_onchange_before_10-packages.sh.tmpl`.
3. Commit and push.

## git identity

`leyna@gurrmail.com` for everything — there is no work-machine split. If one is ever needed, add an
`includeIf` to `dot_gitconfig` pointing at a per-context include file.

## Layout notes

- **Managed**: git, zsh, ghostty, tmux, lazygit, neovim, ttyper, and the shared agent config. tmux
  plugins and nvim-lspconfig are vendored via `.chezmoiexternal.toml.tmpl`; nvim's other plugins are
  managed by Neovim's native `vim.pack`. The agent config links into `~/.claude` via chezmoi
  `symlink_` entries (`CLAUDE.md`, `skills`). ttyper installs via brew on macOS / `cargo` on Linux.
- **Obsidian**: the `obsidian-notes`, `obsidian-project` and `obsidian-plan` skills teach agents to
  write into the personal vault at `~/Documents/Notes` — its structure, frontmatter schema, and the
  rule that renames go through the Obsidian CLI so wikilinks survive. The vault itself is not
  managed here; it syncs via the Self-hosted LiveSync plugin.
- **Parked**: the Wayland desktop stack (sway/waybar/greetd/fuzzel), wezterm, and Arch (`pacman`)
  support live on upstream's [`arch`](https://github.com/Francis-Gurr/dotfiles/tree/arch) branch,
  to be ported into `main` here if a Linux desktop is adopted.

## Commits

Conventional Commits with a leading gitmoji (e.g. `✨ feat(zsh): …`, `🐛 fix(git): …`), enforced by
commitlint via the commit-msg hook. See `commitlint/`.
