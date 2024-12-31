# redbaron-boot
This repo contains a plymouth theme for Nixos, thanks to [discourse](https://discourse.nixos.org/t/genix7000-nix-project-logo-generator/15937/9) for giving me motivation.

# Install

The package is currently not in nixpkgs. 

## Flakes

You can include it in your `flakes.nix` like this:

```nix
{
  inputs.redbaron-boot.url = "github:conneroisu/nixos-boot";
  outputs = { self, nixpkgs, redbaron-boot }:
  {
    nixosConfigurations."<hostname>" = nixpkgs.lib.nixosSystem {
      modules = [ redbaron-boot.nixosModules.default ./configuration.nix ];
      system  = "x86_64-linux";
    };
  };
}

```


## Non Flakes

You can include it in your `configuration.nix` like this:

``` nix
{ config, lib, pkgs, ...}:
let
  # Fetch the repository
  redbaron-boot-src = pkgs.fetchFromGitHub {
    owner = "conneroisu";
    repo = "redbaron-boot";
    rev = "main";
    sha256 = "sha256-Dj8LhVTOrHEnqgONbCEKIEyglO7zQej+KS08faO9NJk=";
  };
in
{
  imports = [ "${redbaron-boot-src}/modules.nix" ];
}
```

## Configuration

Enable redbaron-boot in your configuration:

```nix
{ config, lib, pkgs, ...}:
{
  # ...
  redbaron-boot = {
    enable  = true;

    # Different colors
    # bgColor.red   = 100; # 0 - 255
    # bgColor.green = 100; # 0 - 255
    # bgColor.blue  = 100; # 0 - 255

    # If you want to make sure the theme is seen when your computer starts too fast
    # duration = 3; # in seconds
  };
}
```

# Themes

## redbaron

The first theme, load & unload:

![redbaron](./src/redbaron.gif)

# Adding new themes

- Create directory under src
- Copy all files as .png in there
- copy the `src/template.plymouth` to `src/THEME/THEME.plymouth`
  - replace the word THEME with the actual theme
  - adjust the description & Comment
- copy the `src/template.script` to `src/THEME/THEME.script`
  - change the line "image_quantity" to match the amount of pictures
  - Create a gif from the pngs in the folder:
  ```shell-session
  magick convert -delay 5 -loop 0 -background white $(ls -v *.png) -alpha remove THEME.gif
  ```
- Add the new entry to `modules.nix`
    ```nix
    type = lib.types.enum [ "load_unload" "evil-nixos" "THEME" ];
    ```
- Create a Readme Entry
