# Contributing to Sherpa Content

If you would like to contribute to Sherpa, there are two main paths through which you may do so:

1. Improving/expanding the documentation
2. Site development

You will need a GitHub account and knowledge of how to fork a repo, commit changes, and submit a PR. GitHub has a page explaining how to do this [here](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project).

If you wish to contribute to site development, you should see [sherpa](https://github.com/the-unnamed-nug/sherpa). Otherwise, continue reading ahead.

## Procedure

A PR should not always be the first step to contributing to Sherpa. In some cases, especially where you seek to add a new page, you should make sure there is not a preexisting page that covers it, and ideally, submit a discussion post in the forums (Discord) for it.

However, there are some cases where it would be advisable to simply write up your contribution and submit a PR. Here are some examples:

1. A page includes any errors: typos, gramatical mistakes, syntax errors, etc.
2. A page includes outdated information.
3. A page discusses an area that you have extensive knowledge and experience with, and you think it would benefit the documentation to expand on the information. 

Do note that this list is not necessarily exhaustive.

## Editing Guidelines

To maintain consistency in reading and writing the documentation, there are some general guidelines for writing style and formatting:

1. Use second-person language (you) and avoid first-person language (I, we).
2. Try to mimic the tone of other pages, but in general, use a formal tone.
3. Use code blocks wherever possible.
4. Explain any referenced program, and, ideally, include a link to their homepage.

A maintainer may also add editing suggestions when reviewing your PR to maintain consistency and improve readability.

## Codeblock Examples

Nix modules should include `# configuration.nix` to differentiate it from HM modules.

Codeblock for a Nix Module:

~~~nix
```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    git
];
```
~~~

In contrast, HM modules should include `# home.nix` to highlight it as a HM module.

Codeblock for a HM Module:
~~~nix
```nix
# home.nix
home.packages = with pkgs; [
    git
];
```
~~~

Finally, any codeblocks for a flake should include `# flake.nix`.

Codeblock for a Flake:
~~~nix
```nix
# flake.nix
inputs = {
    nixpkgs.url = "github:nixos/nixpkgs/nixos-unstable";
};
```
~~~

As should be obvious, all nix codeblocks should be specified as nix for formatting.

There is no standard for terminal commands; you may either use codeblocks or in-line code formatting depending on your judgement.

## Option Explanation

When looking at a specific program or service in HM or nixpkgs, you may be faced with a large list of options too lengthy to fully include in a codeblock. In these cases, you should use a table to highlight any significantly important options the user may wish to know about.

As an example, we will look at Steam, with several useful options defined in nixpkgs. The following is an excerpt from the guide to gaming on NixOS:

> Steam is a major distributor of games with first-class Linux support, and excellent support on NixOS. It can be installed as simply as:
> 
> ```nix
> # configuration.nix
> programs.steam = {
>     enable = true; # Enable Steam
> };
> ```
>
> Some options you may wish to be aware of:
> 
> | Option                                   | Definition                                                                         |
> | ---------------------------------------- | ---------------------------------------------------------------------------------- |
> | `programs.steam.package`                 | Allows you to define a specific Steam package, for example for use with overrides  |
> | `programs.steam.protontricks.enable`     | Enables protontricks (like winetricks for Proton)                                  |
> | `programs.steam.remotePlay.openFirewall` | Opens the firewall for Steam Remote Play                                           |
> | `hardware.steam-hardware.enable`         | Enables certain udev rules for Steam hardware (controllers, HTC Vive, Index, etc.) |
> 
> Please note that this table is not exhaustive and the official NixOS options search should be referenced.

Also note the commented explanation of option definition in the codeblock.

