# libfprint-chipsailing-cs9711-git

Chipsailing CS9711 fingerprint driver package for Arch Linux and EndeavourOS
(x86_64).

The package builds [thankarezos/libfprint-CS9711](https://github.com/thankarezos/libfprint-CS9711/tree/cs9711-rebase),
a fork of [archeYR's CS9711 driver](https://github.com/archeYR/libfprint-CS9711/tree/cs9711-rebase).
It follows the `cs9711-rebase` branch with a single driver change: a 1500 ms pause
between scans to give you time to reposition your finger.

## Installation

Install the build tools:

```sh
sudo pacman -S --needed base-devel git
```

Build and install the package:

```sh
git clone https://github.com/thankarezos/cs9711-fingerprint-aur.git
cd cs9711-fingerprint-aur
makepkg -si
sudo pacman -S fprintd
```

This package conflicts with stock `libfprint` and the older
`cs9711-fingerprint-git` package. Accept pacman's prompt to remove the conflicting
package during installation. You do not need to remove `fprintd` first.

Only the CS9711 driver is included. Other fingerprint readers that depend on
stock libfprint will not be supported by this package.

## Verify your fingerprint

To test an existing enrollment:

```sh
fprintd-verify
```

If you have not enrolled a finger yet, run `fprintd-enroll` first.
Installing or removing this package does not delete existing enrollments,
although templates may not be compatible across different drivers.

The package installs the library under `/usr` through pacman. It does not include
a GUI or configure PAM. `fprintd` provides enrollment and verification separately.

## Updates

From your packaging checkout:

```sh
git pull --ff-only
makepkg -Csi
```

The build fetches the latest driver commits from `cs9711-rebase`. Rebuild after
an OpenCV upgrade that changes its shared-library versions. Packaging adapts the
OpenCV dependency name for OpenCV 5 without changing the maintained driver fork.

## KDE Plasma

Test `fprintd-verify` before testing fingerprint unlock on the KDE lock screen.
A successful CLI verification confirms the reader and driver work; desktop
authentication also depends on the desktop's PAM integration.

KDE Plasma 6.8 lock-screen authentication has not been validated by this package.
The package does not modify your authentication configuration.

## Return to stock libfprint

```sh
sudo pacman -S libfprint
```

Accept removal of `libfprint-chipsailing-cs9711-git` when prompted. You can keep `fprintd`
installed. Stock libfprint may not support the CS9711 sensor.

## Contributing

Report packaging problems in this repository. Driver fixes belong in the
[driver repository](https://github.com/thankarezos/libfprint-CS9711), with fixes
suitable for upstream also proposed to archeYR.

See [MAINTAINING.md](MAINTAINING.md) for package checks and AUR publishing.
