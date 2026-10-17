# Package maintenance

Keep packaging here and driver changes in the driver repository. The driver fork
already contains the 1500 ms delay patch; do not apply it again in `prepare()`.
The OpenCV dependency-name adjustment is limited to the package build copy.

## Validate an update

```sh
makepkg -Cf
makepkg --printsrcinfo > .SRCINFO
namcap PKGBUILD ./libfprint-chipsailing-cs9711-git-*.pkg.tar.zst
```

`check()` runs the SIGFM fixtures and three core software test suites. Physical
reader behavior and KDE authentication require separate manual testing. Use a
clean Arch chroot for dependency validation before publishing when available.

Commit the PKGBUILD and regenerated `.SRCINFO` together. Increment `pkgrel` for
packaging changes when `pkgver` stays the same. The VCS `pkgver()` derives the
version from the driver commit count and hash.

## Publish to AUR

Use a separate checkout for the AUR repository. Configure your AUR account's SSH
public key first. Confirm the package name is available or coordinate with its
maintainer before publishing.

From this GitHub packaging checkout:

```sh
git clone ssh://aur@aur.archlinux.org/libfprint-chipsailing-cs9711-git.git ../aur-libfprint-chipsailing-cs9711-git
cp PKGBUILD .SRCINFO ../aur-libfprint-chipsailing-cs9711-git/
cd ../aur-libfprint-chipsailing-cs9711-git
```

For a new, empty AUR repository, create its `master` branch:

```sh
git switch --orphan master
```

For an existing repository, use its existing `master` branch and preserve its
history. Review and publish the update:

```sh
git add PKGBUILD .SRCINFO
git diff --cached
git commit -m 'Update CS9711 driver packaging'
git push -u origin master
```

The push publishes the package to AUR. Pushing this GitHub repository does not.
Upload only the PKGBUILD, `.SRCINFO` and any required packaging assets, never
build outputs or a driver source checkout.

After publication, users can install with `yay -S libfprint-chipsailing-cs9711-git`.
Driver-only commits do not necessarily trigger an upgrade in a normal
`yay -Syu`; rebuild explicitly or use yay's development-package checks.
