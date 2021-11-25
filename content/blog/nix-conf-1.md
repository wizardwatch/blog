---
title: "My NixOS Configuration: Part one, flake.nix"
tags: ["nixos"]
series: ["My NixOS Configuration"]
date: 2021-11-25
---
# My NixOS Configuration
My NixOS configuration has grown organically (a nice way to say it is a mess). I like how it works, though I am slowly refactoring it to something approximating sanity. As a part of that effort in this blog series I endeavour to document it to the point where anyone interested in switching to NixOS could understand how something works and why I included it. 

# flake.nix
flake.nix is the core of any configuration that uses flakes to declaratively manage the repositories it draws packages from. Flakes are not the default yet, and are considered unstable. I believe the extra features are worth the small amount of instability.

## Inputs and Outputs
Flakes start off by declaring inputs, which are collections of packages similar to repositories. My main repository is unstable nixpkgs. I also import home-manager, used for declaratively managing dotfiles; the master branch from the github, used for getting software not yet on unstable; neovim's unstable branch, used for treesitter and other feature support; and eww; used for eww.
```nix
{
description = "My system conf";
inputs = rec {
        # set the channel
        nixpkgs.url = "nixpkgs/nixos-unstable";
        # enable home-manager
        home-manager.url = "github:nix-community/home-manager/master";
        # tell home manager to use the nixpkgs channel set above.
        home-manager.inputs.nixpkgs.follows = "nixpkgs";
        # master channel
        nixmaster.url = "github:NixOS/nixpkgs";
        neovim-nightly.url = "github:nix-community/neovim-nightly-overlay";
        eww.url = "github:elkowar/eww";
};
```
From these inputs I create outputs which are useable throughout the rest of the configuration. Mine include a little bit of special sauce in the form of the @inputs which creates a variable that lets us pass the whole value of outputs.
```nix
outputs = { self, 
            nixpkgs, 
            home-manager,
            neovim-nightly,
            nixmaster,
            eww,
...}@inputs:
```
## Variables
I then move on to setting variables in this section, starting with the keyword let. The first variable set is system which will be used to to set the desired package architecture. I don't run NixOS on any arm computers yet so simply declare that I want 64 bit x86 Linux binaries as follows,
```nix
let
        system = "x86_64-linux";
```
Then I declare the username and hostname.  The username is also used to choose which user profile (work in progress) to install. The hostname is used to set the network hostname and choose which machine profile to use.
```nix
        hostname = "pc1";                                                   
        username = "wyatt";                                                 
        networking.hostname = "${hostname}";
```
Next I set the pkg variable, which controls which packages are available for install. After telling it use the packages chosen in the system variable, I also allow unfree packages. While I aspire to exclusively use free software, especially for games I still have not found suitable free replacements.
```nix
        pkgs = import nixpkgs {                                             
                # imports the system variable                               
                inherit system;                                             
                # enable non free packages                                  
                config = {                                                  
                        allowUnfree = true;                                 
                };                                                          
        };
```
At this point the first overlay is declared. This overlay lets us pull packages from master rather than unstable. Unstable is always a little behind master so being able to pull from nixmaster lets allows use of the latest fixes while Unstable provides binaries which helps minimize compile time.
```nix
        overlays = {                                                        
                nixmaster = final: prev: {                                  
                        nixmaster = (import nixmaster {                     
                                inherit system;                             
                                config = {                                  
                                        allowUnfree = true;                 
                                };                                          
                        });                                                 
                };                                                          
        };
```
## Configuration
Home-manager can now be configured. The user home-manager controls is always called wyatt, though it does set the home directory to the configured username. It also uses the username to choose which files to import. At this point I don't know a clean way make the user controlled by home-manager dynamic with the username. If you do know feel free to make a pr or issue. The neovim overlay and overlays contained within the overlay directory are also applied.
```nix
 lib = nixpkgs.lib;                                                                          
        in {                                                                                        
        homeManagerConfigurations = {                                                               
                wyatt = home-manager.lib.homeManagerConfiguration {                                 
                        inherit system pkgs username;                                               
                        homeDirectory = ("/home/" + username + "/.config");                         
                        configuration = {                                                           
                                nixpkgs.overlays = [ neovim-nightly.overlay (import ./overlays)];   
                                imports = [                                                         
                                        (./users + ("/" + username) + /dotfiles/main.nix)           
                                ];                                                                  
                        };                                                                          
                };                                                                                  
        };
```
Finally the system configuration is declared. This is fairly boring(in a good way), it uses the hostname to choose files to import. It imports the neovim overlay and overlays contained within the overlay directory. The first line in the modules section allows the use of eww (or any other input) without it being an overlay, a bit easier for eww since it only provides one package. Common nix files that are used on all my machines are also imported.
```nix
        nixosConfigurations = {                                                                     
                nixos = lib.makeOverridable lib.nixosSystem{                                        
                        # imports the system variable                                               
                        inherit system;                                                             
                        # import the config file                                                    
                        modules = [                                                                 
                           { _module.args = inputs; }                                               
                                { nixpkgs.overlays = [ overlays.nixmaster  (import ./overlays)]; }  
                                (./common/common.nix)                                               
                                (./machines + ("/" + hostname) + /main.nix)                         
                        ];                                                                          
                };                                                                                  
        };                                                                                          
};                                                                                                  
}
```
## Future plans
I intend to make the home-manager user change with the username, and move the control of which user and machine configuration is enabled to an external program. Then I will make the configuration general enough to make it easy to port between machines. The ultimate goal will be to achieve something similar to [hlissner's flake](https://github.com/hlissner/dotfiles/blob/master/flake.nix) which is a bit cleaner and much more modular.
