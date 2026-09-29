# 🍙 TheFalafelFiles (Dotfiles)

Personal configuration files and Wayland desktop environment setup managed modularly with [GNU Stow](https://www.gnu.org/software/stow/) and Git.

This repository enables fast, reproducible deployment of a personalized Arch / Wayland environment across new systems while keeping configurations version-controlled.

---

## 🖥️ Core Stack & Components

| Component | Software |
| :--- | :--- |
| **Window Manager** | [Hyprland](https://hyprland.org/) (configured via `hyprland.lua`) |
| **Shell** | [Fish Shell](https://fishshell.com/) |
| **Terminal** | [Kitty](https://sw.kovidgoyal.net/kitty/) |
| **UI / Bar / Shell Extension** | Noctalia / QuickShell |
| **Package / Link Manager** | GNU Stow |

---

## 📂 Repository Map

GNU Stow works by treating each top-level directory as a "package." When stowed, Stow symlinks the nested tree straight into the user's `$HOME` directory (`~`), mirroring standard `XDG_CONFIG_HOME` paths.
```
TheFalafelFiles/
├── .gitignore
├── LICENSE
├── README.md
│
├── fish/
│   └── .config/
│       └── fish/
│           ├── completions/
│           ├── conf.d/
│           ├── config.fish
│           ├── fish_variables
│           ├── functions/
│           │   └── fish_prompt.fish
│           └── themes/
│
├── hypr/
│   └── .config/
│       └── hypr/
│           ├── config/
│           │   ├── animations.lua
│           │   ├── autostart.lua
│           │   ├── binds.lua
│           │   ├── colors.lua
│           │   ├── decorations.lua
│           │   ├── environment.lua
│           │   ├── inputs.lua
│           │   ├── misc.lua
│           │   ├── monitors.lua
│           │   ├── variables.lua
│           │   ├── windowrules.lua
│           │   └── workspaces.lua
│           ├── hyprmod/
│           │   ├── active_profile
│           │   └── profiles/
│           ├── hyprland-gui.lua
│           ├── hyprland.lua
│           ├── hyprtoolkit.conf
│           ├── noctalia.lua
│           └── xdph.conf
│
├── kitty/
│   └── .config/
│       └── kitty/
│           ├── kitty.conf
│           └── themes/
│               └── noctalia.conf
│
├── noctalia/
│   └── .config/
│       └── noctalia/
│           └── config.toml
│
└── wallpapers/
    └── Pictures/
        └── Wallpapers/
            ├── 2B/
            ├── Abstract/
            ├── Anime Girls/
            ├── Glitch Art/
            ├── Lain/
            ├── Macro/
            └── Minimal/
```
---

## 🚀 Restoring / Installing on a Fresh Machine

Follow these steps on a freshly installed system to clone and activate this exact setup.

### 1. Install Prerequisites
Ensure Git, GNU Stow, the core desktop environment packages, and required UI/terminal fonts are installed:
```bash
# System utilities, window manager, and shell
sudo pacman -S git stow hyprland kitty fish

# Fonts (UI & Terminal)
sudo pacman -S inter-font ttf-space-grotesk ttf-jetbrains-mono-nerd
```
Also install the system monitor plugin via super+z > plugins

### 2. Configure GitHub Authentication (SSH)
Generate an SSH key so you can pull and push cleanly:
```
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
### 3. Clone the Repository
Clone the repository directly into `~/dotfiles`:
```
git clone git@github.com:FlatFalafel/TheFalafelFiles.git ~/dotfiles
cd ~/dotfiles
```
### 4. Deploy Configurations with GNU Stow
Stow refuses to overwrite existing files. If the fresh installation already created default configuration files inside `~/.config/`, clean them up first:
```
rm -rf ~/.config/hypr ~/.config/kitty ~/.config/fish
cd ~/dotfiles
stow -t ~ fish hypr kitty noctalia
```
Verify that the symlinks are working:
```
ls -la ~/.config/hypr
```
Reload Hyprland with `hyprctl reload` or re-log in to launch the session.

---

## 🛠️ How to Build Your Own Dotfiles from Scratch

### Step 1: Initialize the Directory
```
mkdir -p ~/dotfiles
cd ~/dotfiles
git init
```
### Step 2: Adopt the Package Pattern
1. Create the package folder inside `~/dotfiles`:
```
mkdir -p ~/dotfiles/<package-name>/.config
```
3. Move your existing configuration out of `~/.config` and into the package folder:
```
mv ~/.config/<package-name> ~/dotfiles/<package-name>/.config/
```
4. Use Stow to link it back into your system:
```
cd ~/dotfiles
stow -d ~/dotfiles -t ~ <package-name>
```
### Step 3: Git Hygiene & Ignoring Caches
Create a `.gitignore` inside `~/dotfiles/`:
```
**/.git/
**/cache/
**/tmp/
**/*.log
**/*.state
**/*_history
```
### Step 4: Version Control and Push
```
cd ~/dotfiles
git add .
git commit -m "feat: initial commit of system dotfiles"
git branch -M main
git remote add origin git@github.com:<your-username>/<your-repo-name>.git
git push -u origin main
```
---

## 🌿 Managing Daily Edits & Experimental Branches

* Direct Editing: Because configuration directories are symlinked, editing `~/.config/hypr/hyprland.lua` directly edits the file in `~/dotfiles/`. Just `git commit` and `git push` when done.
* Creating a Test Branch:
```
git switch -c experimental-theme
```
* Reverting to Stable:
```
git switch main
hyprctl reload
```
