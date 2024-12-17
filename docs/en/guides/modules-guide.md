A nix module is a file that contains an expression with a given structure. A module declares a bunch of options for other modules to define, think of it as an "easier" way to handle configurations in the long run, this is one of the main advantages of the nix ecosystem.

The biggest example of a module is in `etc/nix/configuration/nix`, others can be found (here)[https://github.com/NixOS/nixpkgs/tree/master/nixos/modules/programs].

## Structure

a module should have the following structure:

```{
    imports = [
        # Path from which you import other modules.
        # Helps splitting modules in even smaller ones?
    ];

    options = {
        # where you should define the module options.
        # Declares what options the module has.
        # it usually has an enable option, which defines if the module will be used.
    };

    config = {
        # where the actually magic happens, and the modules options declared before will be used to define the options of the module? .
        # if you import form another module that already has an options set you can just define the options here.
    };
}
```

Modules can also be used as functions accepting and attribute set:
```
{ config, pkgs, ...}:
    let

    in {
        imports = [];
        options = {};
        config = {};
    }
```
the following module has:
    
    `config`
    this means the configuration of the entire system.
    
    `pkgs`
    attribute set extracted from nix packages collection and enhanced with `nixpkgs.config` option.

    `...`
    means that any extra parameter passed shall be either ignored or assigned to a keyword which you can use later on.

    `imports`
    as the name states it imports from other files .
    
    `options`
    options declarations with all the definitions and their references.

    `config`
    options definition for the already declared module previously.



here is an example of nix module:

```
{
  pkgs,
  config,
  lib,
  ...
}: let
  cfg = config.gui.obs;
  inherit (lib) mkEnableOption mkIf;
in {
  options.gui.obs.enable = mkEnableOption "obs";

  config = mkIf cfg.enable {
      programs.obs-studio = {
        enable = true;
        plugins = with pkgs; [
          obs-studio-plugins.wlrobs
          obs-studio-plugins.obs-vkcapture
          obs-studio-plugins.obs-vaapi
        ];
      };
  };
}
```
as we can see we are not using 'imports' as is not needed in this case cus everything is in the file.

    
    
