setting up a build environment
============================

Unfortunately, one of the unescapable truths of OS development is that building a kernel is a pain. Even though Rust's toolchain makes building SOS much _less_ of a pain than if the kernel were written in C, setting up a build environment to build SOS is still a fairly complex process.

In order to make this a bit easier, I've written some shell scripts to help install and configure SOS's build dependencies. You can run these scripts with the following `make` target:
```
$ make env
```
Once you've run `make env`, you should have a correctly configured build environment, and you should be ready to build SOS.

If you'd rather install and configure everything yourself, or your system isn't supported by the automatic install scripts,  you can follow these instructions to set up a suitable environment for building SOS:

installing Rust
---------------

In order to build SOS, you need an up-to-date version of the Rust compiler. SOS uses several gated features that can only be built on the nightly Rust release channel.

The suggested way to install Rust is using [Rustup](https://www.rustup.rs), a tool for managing multiple versions of Rust. If you don't have Rustup installed, you can install it by running this command in your terminal:
```
$ curl https://sh.rustup.rs -sSf | sh
```

Once Rustup is installed, run
```
$ rustup update nightly
```

to ensure the nightly release branch is up to date.

If you've set the stable or beta Rust release channels as the global default, you should run
```
$ rustup override nightly
```
in the SOS root directory, to set the nightly release channel as the default for SOS.

installing build dependencies
-----------------------------

Once you have Rust installed, you will need the following additional dependencies:
+ `ld`
+ `grub-mkrescue` & `xorriso`
+ `qemu` for running the kernel under emulation
+ `mtools`

Depending on your OS, you'll want to install these dependencies somewhat differently.

### linux

On Debian you can install them with

```
$ sudo apt-get install xorriso qemu mtools build-essential
```
On Arch Linux you can install them with
```
$ sudo pacman -S --needed binutils grub libisoburn qemu mtools
```
And on Fedora with
```
$ sudo dnf instal xorriso qemu
```
On Gentoo it's
```
$ sudo emerge app-emulation/qemu dev-libs/libisoburn sys-boot/grub
```

You will also need `x86_64-elf-objcopy` and `x86_64-elf-strip` executables.
On x86_64 Linux you can simply symlink them to your regular `objcopy` and `strip`.

If you are using Gentoo but on another architecture you can build a cross-compiled version of `binutils` (which contains `objcopy` and `strip`) with
```
$ sudo emerge sys-devel/crossdev
$ sudo crossdev -s0 --target x86_64-elf
```

### macOS

Installing dev dependencies on macOS is slightly trickier, as you will also need a cross-compiled version of GNU `binutils` to build SOS. Cross-compiling `binutils` will require some additional dependencies.

You can install a majority of dependencies using the [Homebrew](https://github.com/Homebrew/brew) package manager. I've included a `Brewfile` for automatically installing these dependencies. To use the `Brewfile`, run the following sequence of commands:

```
$ brew update
$ brew tap Homebrew/bundle
$ brew bundle
```

Once you've installed the `Brewfile`, you'll need to cross-compile `binutils`.

I've included a script to automatically download and cross-compile `binutils`, which you can run by typing:
```
$ ./scripts/install-mac.sh
```

Alternatively, if you want to do it manually, Phil Oppermann has a tutorial [here](http://os.phil-opp.com/cross-compile-binutils.html).

### windows
Seriously?
Windows isn't supported; I can't possibly advise it.

installing `xargo`
-----------------

Once you've installed Rust and the dev dependencies, you'll need to install [`xargo`](https://github.com/japaric/xargo), a tool for cross-compiling Rust programs. You can install `xargo` quite easily by running
```
$ cargo install xargo
```
