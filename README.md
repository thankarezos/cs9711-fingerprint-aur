# CS9711 Arch Linux packaging

Packaging for Arch Linux and EndeavourOS, kept separate from the driver.
Source: [thankarezos/libfprint-CS9711, cs9711-rebase](https://github.com/thankarezos/libfprint-CS9711/tree/cs9711-rebase),
which follows [archeYR](https://github.com/archeYR/libfprint-CS9711/tree/cs9711-rebase).
The driver fork contains the 1500 ms scan-delay patch. Packaging does not reapply it.

## Build and install locally

```sh
git clone https://github.com/thankarezos/cs9711-fingerprint-aur.git
cd cs9711-fingerprint-aur
makepkg -si
sudo pacman -S fprintd
fprintd-verify
```

This is a GitHub packaging repository, not proof of an AUR publication. Once an
AUR entry exists, `yay -S cs9711-fingerprint-git` can install that entry. Check its
PKGBUILD source before installing; the name may already be owned by another
maintainer. Do not assume an existing AUR entry uses this fork.

The package replaces stock `libfprint` via `provides` and `conflicts`; pacman
manages the shared paths under `/usr`. There is no `replaces` migration claim,
`/usr/local` installation, PAM modification, service restart, or enrollment step.
`fprintd` is installed separately. This build enables only the CS9711 driver, so
it is unsuitable for systems that also need another fingerprint reader.

On OpenCV 5 hosts, `prepare()` changes the old `opencv4` dependency name in the
build copy only. OpenCV 4 continues to use upstream's name. Rebuild after OpenCV
soname changes; this package does not create ABI symlinks or install rebuild hooks.
Doctest remains a build dependency, matching the driver source.

## Updates and validation

```sh
git pull --ff-only
makepkg -Csi
```

A VCS package does not necessarily rebuild on a normal `yay -Syu` when only the
driver branch changes. Rebuild explicitly or use yay's development-package checks.
After driver updates, run the build and `check()`, inspect runtime dependencies
with `ldd`, and test `fprintd-verify` before checking KDE lock-screen integration.
The software checks use fake devices and SIGFM fixtures. They cannot establish
physical-device or KDE authentication compatibility. A previous local build of
commit `ec3ad6e` passed software checks and the user's physical `fprintd-verify`.

For maintainers, regenerate metadata after building:

```sh
makepkg --printsrcinfo > .SRCINFO
namcap PKGBUILD ./*.pkg.tar.zst
```

Only x86_64 is declared because that is the tested architecture. No KDE Plasma
6.8 compatibility guarantee is made: CLI verification and lock-screen PAM
integration are separate checks. This package does not configure the latter.

## Publish to AUR separately

Confirm the AUR package name is available or coordinate with its current
maintainer. Configure your AUR SSH key and clone its independent Git repository:

```sh
git clone ssh://aur@aur.archlinux.org/cs9711-fingerprint-git.git ../cs9711-aur-publish
cp PKGBUILD .SRCINFO ../cs9711-aur-publish/
cd ../cs9711-aur-publish
git add PKGBUILD .SRCINFO
git commit -m 'Update CS9711 driver packaging'
# Review the commit, then publish explicitly:
git push origin master
```

Keep the GitHub repository and AUR repository separate. Do not copy build outputs
or source checkouts into AUR. GitHub publication and AUR publication are manual.

## Return to stock libfprint

```sh
sudo pacman -S libfprint
```

Accept removal of the conflicting CS9711 package. Keep `fprintd` if desired.
Stock libfprint may not support this sensor. Enrollment is not deleted by this
package, although templates are not guaranteed portable between drivers.
