# Ubuntu Rebuild Framework

A production-ready, idempotent rebuild framework for Ubuntu machines.

This framework is designed around a simple rule:

- **Rebuild scripts own installation and orchestration**
- **chezmoi owns dotfiles and user configuration**

Chezmoi is a stage in the process, not the foundation of the process.

## Directory tree

```text
ubuntu-rebuild-framework/
├── README.md
├── rebuild.sh
├── lib/
│   ├── common.sh
│   ├── logging.sh
│   └── packages.sh
├── manifests/
│   ├── apt-packages.txt
│   ├── snap-packages.txt
│   ├── flatpak-packages.txt
│   ├── pipx-packages.txt
│   ├── cargo-packages.txt
│   ├── uv-tools.txt
│   ├── npm-global-packages.txt
│   ├── apt-repositories.sh
│   └── manual-downloads.txt
├── profiles/
│   ├── laptop.env
│   ├── vm.env
│   └── workstation.env
├── scripts/
│   ├── 00-preflight.sh
│   ├── 01-system-prep.sh
│   ├── 02-install-repositories.sh
│   ├── 03-install-apt.sh
│   ├── 04-install-snap.sh
│   ├── 05-install-flatpak.sh
│   ├── 06-install-pipx.sh
│   ├── 07-install-cargo.sh
│   ├── 08-install-uv-tools.sh
│   ├── 09-install-npm-global.sh
│   ├── 10-install-manual-apps.sh
│   ├── 11-install-chezmoi.sh
│   ├── 12-apply-chezmoi.sh
│   ├── 13-post-chezmoi.sh
│   ├── 14-validate.sh
│   ├── 15-export-state.sh
│   └── 98-manual-checklist.sh
├── state/
│   └── exports/
└── logs/
```

## Intended flow

1. Base Ubuntu install
2. Clone this repo
3. Run `./rebuild.sh --profile laptop`
4. Let the framework install packages, tools, and repos
5. Let the framework install and apply chezmoi
6. Review the manual checklist at the end

## Usage

```bash
chmod +x rebuild.sh
./rebuild.sh --profile laptop
```

Dry run:

```bash
./rebuild.sh --profile laptop --dry-run
```

Skip chezmoi temporarily:

```bash
./rebuild.sh --profile laptop --skip-step 11-install-chezmoi.sh --skip-step 12-apply-chezmoi.sh --skip-step 13-post-chezmoi.sh
```

Run only validation:

```bash
./rebuild.sh --profile laptop --only-step 14-validate.sh
```

## Important notes

- The manifests included here are a **solid starting baseline**, not a magical full inventory of every package that has ever existed on your machine.
- The framework already includes state export scripts so you can refresh manifests from the live machine.
- Manual and proprietary apps are tracked separately in `manifests/manual-downloads.txt`.
- Destructive actions are intentionally avoided.

## What belongs where

### This rebuild framework owns

- apt repositories and signing keys
- apt, snap, flatpak, pipx, cargo, uv, npm-global installation
- vendor `.deb` / `.AppImage` style installers
- orchestration and validation
- machine-profile logic
- system package installation

### Chezmoi owns

- shell config
- git config
- ssh config templates
- editor and terminal configuration
- app settings and user-level config files
- small post-config user scripts

### Manual steps still remain manual

- disk partitioning
- full-disk encryption choices
- BIOS / UEFI settings
- Secure Boot choices
- login to web services
- browser sync
- secrets requiring interactive auth

## Profile strategy

Profiles live in `profiles/*.env`.

Use them to decide whether a machine is a laptop, VM, or workstation, and toggle categories like:

- GUI apps
- virtualization tools
- printer/scanner support
- heavy desktop apps
- backup tools
- work-specific utilities

## Refreshing manifests from a live machine

After you tune a machine exactly the way you want it:

```bash
./scripts/15-export-state.sh
```

That will export package state into `state/exports/` and refresh the text manifests.
