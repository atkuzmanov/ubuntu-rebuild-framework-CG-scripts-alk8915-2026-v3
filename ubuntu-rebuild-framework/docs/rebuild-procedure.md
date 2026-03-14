# Machine Rebuild Procedure

Follow these steps to rebuild a machine.

## 1 Install Ubuntu

Perform a normal Ubuntu installation.

Recommended options:

- Full disk encryption
- Latest LTS release
- Default GNOME desktop

## 2 Install basic tools

After first login:

sudo apt update
sudo apt install -y git curl

## 3 Clone rebuild repository

git clone <your-rebuild-repo>
cd ubuntu-rebuild-framework

## 4 Run rebuild

./rebuild.sh --profile laptop

Available profiles:

- laptop
- workstation
- vm

## 5 Wait for completion

The script will install:

- package repositories
- apt packages
- snap packages
- flatpak packages
- developer tooling
- vendor applications
- chezmoi configuration

## 6 Verify system

Run:

scripts/14-validate.sh

## 7 Export system state (optional)

scripts/15-export-state.sh

This updates manifests to match the current machine.
