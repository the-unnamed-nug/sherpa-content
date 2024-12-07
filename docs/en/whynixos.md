---
title: Why use Nix/NixOS?
---

A reasonable question! Why use any of this? Why abandon everything you know
about Linux and use this niche operating system? Well, to understand, we must
first look at the advantages of Nix, the powerful package manager NixOS is built
on.

Nix advantages:

- **The Nix Store**: Nix stores all packages in a special folder called the nix
  store, which exists in the /nix directory. In the nix store, a package is
  built along with its dependencies, and stored as a hash. The store is
  read-only, meaning no modifications can be made to it without specialized
  commands, _even as root_!
- **Atomic updates**: when packages installed by Nix need to update, Nix will
  not allow updates to occur unless all of them are successful. This eradicates
  broken packages being an issue. If a package refuses to build, just wait for
  the next update when they fix it! These usually take only a couple of days,
  during which your system will be fully usable and not broken.
- **Reproducible builds**: when Nix builds a package, it will build exactly the
  same result on any system that package is built on.
- **Single package manager to rule them all**: tired of using NPM, pip, the AUR,
  and all these other package managers? We were too! That's why we are very
  happy to say that Nix has more than 100,000 packages to install, and is the
  most up-to-date package manager in existence! We like to think of it as the
  Rule 34 of package managers: if it exists, it's in nixpkgs. If it isn't in
  nixpkgs, it will be added.
- **Great for development**: development is a major reason for Nix's existence!
  With tools like direnv, making dev environments is easy and benefits from all
  that Nix has to offer.
- **Easy to try out packages without installing using `nix shell` or
  `nix run`**: have you ever wanted to try out a program from, let's say, KDE?
  On other systems, it requires fully installing all of KDE's dependencies.
  Then, if you don't like the program and uninstall it, you still have those
  dependencies on your system. But using the above commands, you can quickly and
  effectively try out any program you like. If you don't like it, no problem! It
  won't stay on your system when you remove it.
- **Uses binary caches**: Nix automatically builds packages in its repository
  remotely, meaning compiling for anything in nixpkgs is is unnecessary! Need a
  binary cache for your own program? You can set that up easily too!
- **Portable**: Nix runs on anything: Linux, MacOS, WSL, there's even a project
  called nix-on-droid that lets you use it on Android.
- **Mix and match different package versions without conflicts**: since packages
  are built independently of each other, dependencies and all, you can have
  multiple versions of a package. It isn't anything you need to set up either.
  That's just how it is.

So, now that you know why you should use Nix, why use NixOS?

NixOS advantages:

- **Declarative configuration**: the central feature of NixOS, other than the
  package manager, is the module system. Modules are pretty much a fancy term
  for options. In your NixOS configuration, you can reference one of the
  thousands of modules NixOS has for applications. This has two advantages:
  - **_Easy configuration_**: Gone are the days of setting up services manually
    or using docker containers. With NixOS, as long as the module exists, you
    can do all of that with a few lines of code.
  - **_Easier to maintain the configuration_**: since your configuration is a
    few lines of code and always in the same directory you left it in, there's
    no need to run a multitude of commands in an effort to solve problems. All
    you have to do is change some values in your NixOS configuration.
- **Reproducible system**: this is one of the crowning features of NixOS: taking
  the reproducibility of Nix to the OS level. You can back up your configuration
  to a git repository and place it on however many computers you like, and it'll
  be the exact same operating system every single time, minus your home
  directory. This makes backups a breeze.
- **Out of the box rollbacks**: another crowning feature of NixOS, and any
  immutable distro. Is there an issue with your current system? No problem! Just
  rollback to a previous system either from a terminal or at boot time and
  you'll be good to go!
- **Configuration options for many programs & services**: there are thousands of
  modules in NixOS, and thousands of options to choose from, all written in a
  single language.
- **Free of side effects - Actually uninstalls packages and their
  dependencies**: since dependencies are installed and stored with the packages
  that need them, this also means that no dependencies will be left behind when
  you uninstall a package. And any useless store directories can be cleaned up
  using `nix-collect-garbage`.
- **Easy to set up VMs**: you can set up QEMU/KVM easily using the module! No
  more fussing around with systemd stuff or a clunky GUI. Nix can do it all!
- **Test other configurations**: remember `nix shell` and `nix run`? Both of
  these commands can also build configurations from a URL! Just point to the git
  repo where the configuration is, and Nix will do the rest.

If any of these sound good to you, head on over to the next page where we'll
cover how to install NixOS on your machine.
