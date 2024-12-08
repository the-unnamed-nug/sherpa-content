---
title: Gaming on NixOS
---

Gaming may naturally be a major field of concern regarding NixOS's compatibility. Thankfully, like any modern Linux distribution, NixOS can play the vast majority of games just as well as Windows.

## Setting Up Drivers

You should be sure to enable graphics driver support for OpenGL, Vulkan, etc. You can do that with the following:

```nix
# configuration.nix
hardware.graphics = {
    enable = true;
    enable32Bit = true; # Also enables 32-bit drivers for things like wine
};
```

These settings are usually set when you enable something like Steam, however it would be wise to include it for other cases.

### Hybrid Graphics

For many of you seeking to game on a laptop with NixOS, you may be left wondering how to configure your system to either maximize performance all of the time using both GPUs, or even only use your dedicated GPU when playing a game.

#### Nvidia

Nvidia has support for this through Nvidia Prime. Their "sync" mode enables the use of both GPUs simulateneously:

```nix
# configuration.nix
hardware.nvidia.prime = {
    sync.enable = true;
    
    # Internal graphics from a CPU
    intelBusId = "PCI:0:2:0";

    # Or, if you have an AMD CPU:
    # amdgpuBusId = "PCI:4:0:0";

    # Dedicated Nvidia graphics card
    nvidiaBusId = "PCI:1:0:0";
};
```

Or consider their "offload" mode to optimize battery life, using the dedicated GPU only when needed:

```nix
# configuration.nix
hardware.nvidia.prime = {
    offload.enable = true;

    # See note below
    offload.enableOffloadCmd = true;

    # Internal graphics from a CPU
    intelBusId = "PCI:0:2:0";

    # Or, if you have an AMD CPU:
    # amdgpuBusId = "PCI:4:0:0";

    # Dedicated Nvidia graphics card
    nvidiaBusId = "PCI:1:0:0";
};
```

You can also enable `hardware.nvidia.prime.offload.enableOffloadCmd` to enable an `nvidia-offload` command that automatically offloads a program to the GPU. You can use it by prefixing the running command with `nvidia-offload`, for example with Steam, setting the launch options to:

```
nvidia-offload %command%
```

Returning to the Nix options, to find your respective bus IDs, simply run `lspci` from a terminal. On the left, a bus ID such as "01:00.0" will be shown, and to the right, a description of the hardware the ID corresponds to will be shown. You should be able to identify the bus ID for both your CPU and GPU.

In the case of "01:00.0," the bus ID should be written as `PCI:1:0:0`. For "00:02.0," you would use `PCI:0:2:0`.

Nvidia users should also be sure to reference the specific Nvidia page for custom driver configuration.

#### AMD

On AMD, the process should be much simpler. By default, AMD does not use the dedicated GPU synchronously with the machine. You can replicate similar behavior as on Nvidia for each of the desired outcomes described above. For sync, you can declare the environmental variable `DRI_PRIME=1` like so:

```nix
# configuration.nix
environment.sessionVariables = {
    DRI_PRIME = "1";
};
```

If you wish for the dedicated GPU to only be used in games for performance, you can tell Steam to prefix the game launch command:

```
DRI_PRIME=1 %command%
```

## Launchers

There are various launchers used in modern gaming, here are a few that you will likely find useful on Linux.

### Steam

Steam is a major distributor of games with first-class Linux support, and excellent support on NixOS. It can be installed as simply as:

```nix
# configuration.nix
programs.steam = {
    enable = true; # Enable Steam
};
```

Some options you may wish to be aware of:

| Option                                   | Definition                                                                         |
| ---------------------------------------- | ---------------------------------------------------------------------------------- |
| `programs.steam.package`                 | Allows you to define a specific Steam package, for example for use with overrides  |
| `programs.steam.protontricks.enable`     | Enables protontricks (like winetricks for Proton)                                  |
| `programs.steam.remotePlay.openFirewall` | Opens the firewall for Steam Remote Play                                           |
| `hardware.steam-hardware.enable`         | Enables certain udev rules for Steam hardware (controllers, HTC Vive, Index, etc.) |

Please note that this table is not exhaustive and the official NixOS options search should be referenced.


### Heroic Games Launcher

Heroic Games Launcher is a more recent FOSS launcher with support for games from GOG, Epic, and Amazon Prime Games, native to Linux.

It does not have extensive option declaration however can certainly be added as a package:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    heroic
];
```

### Lutris

Lutris is another FOSS launcher with support for many different games along with custom installers to improve compatibility for a wide array of games, such as Battle.net, EA Games, and even standalone games like Osu! or Genshin Impact.

It may also be installed as a package:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    lutris
];
```

### Bottles

Finally, Bottles is a relatively new, GTK4 and FOSS launcher that advertises compatibility with Windows games and software through preconfigured environments. They support many different launchers such as Epic Games, EA Launcher, and Battle.net.

While they recommend their Flatpak package, we would recommend the app packaged in nixpkgs due to its closer support with Nix.

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    bottles
];
```

## Useful Utilities

Beyond launching a game, NixOS comes with many additional programs and features that are highly useful for gaming.

### GameMode

GameMode performs optimizations on the host system and/or game process to improve performance.

It can be enabled and configured declaratively like so:

```nix
# configuration.nix
programs.gamemode = {
    enable = true;
    settings = {
        # Example config from nixpkgs
        general = {
            renice = 10;
        };

        # Warning: GPU optimisations have the potential to damage hardware
        gpu = {
            apply_gpu_optimisations = "accept-responsibility";
            gpu_device = 0;
            amd_performance_level = "high";
        };

        custom = {
            start = "${pkgs.libnotify}/bin/notify-send 'GameMode started'";
            end = "${pkgs.libnotify}/bin/notify-send 'GameMode ended'";
        };
    };
};
```

Please reference their man pages for exhaustive list of settings.

You can launch a game with GameMode through Steam by setting `gamemoderun %command%` as a launch option, enabling GameMode as the game is launched.

### Gamescope

Gamescope is another major utility developed by Valve that acts as a microcompositor helping improve consistency in games on Linux.

Likewise, it has excellent support in NixOS. It can be enabled and configured declaratively:

```nix
# configuration.nix
programs.gamescope = {
    enable = true;
    args = [ # Define arguments to launch Gamescope with.
        # Following example from nixpkgs.
        "--rt"
        "--prefer-vk-device 8086:9bc4"   
    ];
};
```

You can also use ChimeraOS's fork, Gamescope-Session, which creates a special Gamescope session to launch from the Display Manager.

```nix
# configuration.nix
programs.steam.gamescopeSession = {
    enable = true;
    args = []; # Works the same as programs.gamescope.args
    env = {}; # Define environmental variables for the Gamescope session.
};
```

### Protonup

You may wish to install different versions of Proton, such as Glorious Eggroll's fork. You can do so by installing the protonup CLI:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    protonup
];
```

And then running it to install the latest Proton-GE:

```
protonup
```

You will then need to fully restart Steam and then select it in Settings->Compatibility.

## VR

This section is still a work-in-progress. If you would like to contribute, please submit a PR in the [repo](https://github.com/the-unnamed-nug/sherpa).

For wired play, see [Monado](https://github.com/SimulaVR/monado), an open source implimentation of the OpenXR API.

For wireless devices, see [WiVRn](https://github.com/WiVRn/WiVRn), a VR streamer built off of Monado for devices such as the Quest.

For now, you can reference the [NixOS Wiki](https://wiki.nixos.org/wiki/VR) and the [Linux VR Adventures Wiki](https://wiki.nixos.org/wiki/VR).

## See Also

- [Vimjoyer's Video](https://www.youtube.com/watch?v=qlfm3MEbqYA)
- [Arch Wiki](https://wiki.archlinux.org/title/Gaming)
- [Linux VR Adventures Wiki](https://lvra.gitlab.io/)
- [r/linux_gaming FAQ](https://www.reddit.com/r/linux_gaming/wiki/faq/)
