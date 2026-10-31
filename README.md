# terraprism-git for Arch Linux

Builds the [`terraprism-git`](https://aur.archlinux.org/packages/terraprism-git) AUR package, which
compiles [terraprism](https://github.com/CaptShanks/terraprism) (a terminal UI for Terraform and
OpenTofu plans) from the latest upstream `main` commit. If you want the prebuilt release binary
instead, use [`terraprism-bin`](https://github.com/hosua/aur-terraprism-bin). The two conflict, so
pick one.

## Install

```bash
yay -S terraprism-git
```

Without a helper:

```bash
git clone https://aur.archlinux.org/terraprism-git.git
cd terraprism-git
makepkg -si
```

The build needs `go` and `git` (makepkg installs them with `-s`) and runs upstream's tests. It works
on `x86_64` and `aarch64`. For `plan`, `apply` and `state` modes, also install `terraform` or
`opentofu`.

## Update

The version is computed from git at build time, so you get the newest commit whenever your helper
rebuilds VCS packages:

```bash
yay -Syu --devel
```

The AUR page itself is only republished when upstream tags a release, so the version shown there can
lag behind `main`. That's expected.

Do not use `terraprism upgrade`. It tries to overwrite `/usr/bin/terraprism`, which pacman owns. Set
`TERRAPRISM_SKIP_UPDATE_CHECK=1` to hide its "Update available" notice.

## Uninstall

```bash
sudo pacman -Rns terraprism-git
```

Plan and apply history stays in `~/.terraprism/`.

## Usage

```bash
terraprism apply                       # run plan, review it, press a to apply
terraform plan -no-color | terraprism  # view piped plan output
TERRAPRISM_TOFU=1 terraprism plan      # use OpenTofu
```

`terraprism --help` lists every command and key. Full docs:
<https://github.com/CaptShanks/terraprism#readme>

## Reporting problems

Bugs in terraprism itself go to <https://github.com/CaptShanks/terraprism/issues>. Build or install
failures of this package go to an issue here or a comment on the AUR page.

## Maintainer notes

`.github/workflows/update-aur.yml` runs daily at 06:30 UTC in an `archlinux` container. It builds
the package from upstream `main` with `check()`, so a broken upstream build fails the run and GitHub
emails the repo owner. It commits the new `pkgver` here and pushes to the AUR only when the release
part of `pkgver` changes (0.13.0.r4.gabc to 0.14.0.r0.gdef). Each push to a package notifies everyone
watching it, so we skip per-commit pushes.

Secrets: `AUR_SSH_PRIVATE_KEY` (a deploy key used only for this package), `AUR_USERNAME`,
`AUR_EMAIL`. To republish without a new tag:

```bash
gh workflow run update-aur.yml -f force=true
```
