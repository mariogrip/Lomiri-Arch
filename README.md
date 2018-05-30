# Unity8 for Arch GNU/Linux project

## About
This project aims to bring [Unity8](https://github.com/ubports/unity8-build) DE to Arch GNU/Linux.

Currently it is built for `x86_64` only, but support for `i686` and `arm` (`aarch64`) is on the roadmap.

### Progress
_Clickable icons:_

- 🌍 - Available in the repository
- ✅ - Available in the AUR
- 😜 - Git version is available in the AUR
- ⛔️ - Unresolved issues
- 🆗 - Resolved issues

- [x] `mir` [🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/mir) [😜](https://aur.archlinux.org/packages/mir-git) | [🆗](https://github.com/MirServer/mir/commit/e6ba0de363320feab9359821c96d69ff61a7f121) 
- [ ] `ubuntu-download-manager` [⛔️](https://github.com/ubports/ubuntu-download-manager/issues/2) [⛔️](https://github.com/ubports/ubuntu-download-manager/issues/3) [⛔️](https://github.com/ubports/ubuntu-download-manager/issues/4) [⛔️](https://github.com/ubports/ubuntu-download-manager/issues/6)
- [ ] `url-dispatcher`
- [ ] `thumbnailer`
- [ ] `settings-components`
- [ ] `history-service`
- [ ] `ubuntu-ui-toolkit`
- [ ] `?whoopsie` [repo](https://bazaar.launchpad.net/~daisy-pluckers/whoopsie/trunk/files)
- [x] `cmake-extras` [🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/cmake-extras-git) | [⛔️](https://github.com/ubports/cmake-extras/issues/2)
- [x] `libqtdbustest` [🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/libqtdbustest-git) | [⛔️](https://github.com/ubports/libqtdbustest/issues/1)
- [x] `dbus-test-runner` [🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/dbus-test-runner)
- [x] `unity-api`[🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/unity-api-git) | [⛔️](https://github.com/ubports/unity-api/issues/2)
- [x] `libertine` [🌍](https://github.com/vanyasem/Unity8-Arch/tree/master/libertine-git) | [⛔️](https://github.com/ubports/libertine/issues/3)
- [ ] `ubuntu-app-launch`
- [ ] `content-hub`
- [ ] `qtmir`
- [ ] `trust-store`
- [ ] `location-service`
- [ ] `platform-api`
- [ ] `qtubuntu`
- [ ] `libusermetrics`
- [ ] `keyboard-component`
- [ ] `system-settings`
- [ ] `unity8`
- [ ] Probably more

## Install Unity8 (not implemented, boilerplate text)
### Option 1:
You can install precompiled packages from my personal repository.

Add the repository to /etc/pacman.conf:
```
[unity8]
SigLevel = Required
Server = https://unity8.mynameisivan.ru/$repo/os/$arch
```

Trust my GPG key:
```
sudo pacman-key --recv-keys --keyserver hkps://hkps.pool.sks-keyservers.net F3A621DFD4328CC4
sudo pacman-key --lsign-key F3A621DFD4328CC4
```

Refresh the local pacman database:
```
sudo pacman -Syyu
```

Install Unity8 packages:
```
sudo pacman -S !!!TBD!!!
```

### Option 2:
You can compile the packages yourself from the AUR.

You can either do it manually for each dependency:

_There is a helper script (`build-packages.sh`) in the root of this repository that will build all packages for you._
```
PACKAGE = !!!TBD!!!
git clone https://aur.archlinux.org/$PACKAGE.git
cd $PACKAGE
makepkg -sic
```

Or use an AUR helper of your choice:
```
pacaur -S !!!TBD!!!
```

## (Advanced) Configure a local repository / build the packages
Add the package repository to `/etc/pacman.conf`:

_You can add my repository, or you could specify your own local repo that you will create in the next step. Read more [on the wiki](https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks#Custom_local_repository)._
```
[unity8]
SigLevel = Never
Server = https://unity8.mynameisivan.ru/$repo/os/$arch
```
_or_
```
[unity8]
SigLevel = Never
Server = file:///your/path/$repo/os/$arch
```

Assemble the build enviroment:

_Don't forget to configure PACKAGER in /etc/makepkg.conf_

_If you want to cross-compile packages for `i686`, then follow the guide [on the wiki](https://wiki.archlinux.org/index.php/Building_32-bit_packages_on_a_64-bit_system). But as Arch doesn't officially support i686 now, use the [mirrorlist](https://raw.githubusercontent.com/archlinux32/packages/master/core/pacman-mirrorlist/mirrorlist) of the ArchLinux32 project. You will also need to trust 2 GPG keys: `255A76DB9A12601A` and `C8E8F5A0AF9BA7E7` same way as desribed above with my key._
```
sudo pacman -S devtools
mkdir chroot-x86_64
sudo mkarchroot -C /etc/pacman.conf -M /etc/makepkg.conf ./chroot-x86_64/root base base-devel git
mkdir unity8 sources logs PKGBUILDs
```

_Your Arch repository will settle in the `unity8` folder._

Clone this repo's PKGBUILDs:
```
cd PKGBUILDs
git clone https://github.com/vanyasem/Unity8-Arch.git ./
git submodule init
git submodule update
cd ..
```

Sync the databases:
```
sudo arch-chroot chroot-x86_64/root
pacman -Syyu
exit
```

Install Arch repository manager ([guzuta](https://github.com/eagletmt/guzuta)):
```
sudo pacman -S cargo
cargo install guzuta
```

Configure guzuta:
```
cat > .guzuta.yml
name: unity8
package_key: YOUR_GPG_KEY
repo_key: YOUR_GPG_KEY
srcdest: sources
logdest: logs
pkgbuild: PKGBUILDs
builds:
  x86_64:
    chroot: ./chroot-x86_64
```

Build the packages:

_If your decided to use your local repo, you have to build packages in this specific order, as some packages depend on each other_

Run `rebuild-repo.sh` from the PKGBUILDs directory. Make sure to configure sudo timeout for your build user, as it defaults to 5 minutes.
