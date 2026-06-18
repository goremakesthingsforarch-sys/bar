# bar (Bash AUR Retriever)

`bar` is a lightweight, zero-dependency, standalone AUR helper and system maintenance tool written entirely in native Bash. It provides a quick, interactive terminal experience to manage official Arch Linux repository packages and AUR packages seamlessly.

## Features

* **Dual Engine Search & Install**: Queries official repository databases via `pacman` first before gracefully falling back to look up packages on the AUR.
* **Interactive Package Selection**: Running a search displays a numbered menu allowing you to choose one or multiple packages to install instantly on the spot.
* **Multi Package Queueing**: Supports installing multiple packages sequentially in a single command.
* **System & AUR Upgrades**: Synchronizes repository databases, runs full system upgrades via `pacman`, and targets your foreign packages to upgrade AUR entries safely using `vercmp`.
* **Build Cache Management**: Includes a built-in cache-clearing utility to prevent your temporary build directories from cluttering system memory.
* **Security Conscious**: Never traps or hides administrative actions. It executes without root privileges and safely delegates password prompts directly to `pacman` and `makepkg`. It forces a `PKGBUILD` review before executing any source scripts.

## Installation

### Manual Installation

To install `bar` system-wide on your machine:

```bash
# Clone this repository or download the script directly
git clone https://github.com/goremakesthingsforarch-sys/bar.git
cd bar

# Make the script executable
chmod +x bar

# Move it into your system PATH
sudo mv bar /usr/local/bin/
```

# Dependencies

bar relies on core system tools native to Arch Linux, with only one lightweight external parser:

  bash

  pacman

  makepkg

  curl

  jq (Command-line JSON processor)

If jq is not installed on your system, you can fetch it using bar itself or pacman:
```bash
# For bar
bar -S jq

# pacman
sudo pacman -S jq
```

# Usage
Usage:
  bar -S <pkg1> [pkg2 ...] : Install packages (Official Repo or AUR)
  bar -Ss <query>          : Search and install interactively via numbered menu
  bar -Syu                 : Update core system and all AUR packages
  bar -Sc                  : Clean up local AUR build cache directory

# Examples

Interactive Search & Installation:
```bash
bar -Ss visual-studio-code
```
This will fetch matches from both official repositories and the AUR, presenting an interactive prompt:
:: Searching official repos...
:: Searching AUR...
1 aur/visual-studio-code-bin 152.0.1-1
    Visual Studio Code Open Source Edition
2 aur/visual-studio-code-insiders-bin 153.0.0-1
    The Insider build of Visual Studio Code

=> Enter numbers to install (separated by spaces, e.g., 1 3 5), or press Enter to exit:

Direct Installation (Single or Multiple):
```bash
bar -S neovim discord-canary-bin
```

Full System & AUR Sync Update:
```bash
bar -Syu
```

Clear Temporary Build Cache:
```bash
bar -Sc
```

# How It Works Under The Hood

  `bar -S`: Evaluates arguments. If pacman -Si detects the package exists in the core repositories, it executes sudo pacman -S. If missing, it uses curl to query the AUR RPC Web API, runs a clean git clone into /tmp/bar-builds/, prompts for an explicit PKGBUILD user audit using your environment's $PAGER, and passes execution off to makepkg -sic.

   `bar -Syu`: Triggers standard repository compilation upgrades, reads local foreign versions with pacman -Qm, batches an info payload back to the remote AUR repository using standard arrays, and evaluates version divergence locally using the native vercmp binary tool before triggering sequential builds.

# License

This project is licensed under the GPL License. Feel free to fork, modify, and rice it to fit your workflow.
