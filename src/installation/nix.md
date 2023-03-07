# [Nix](https://nixos.org) (Linux/macOS)

[Nix recipe](https://github.com/NixOS/nixpkgs/blob/master/pkgs/development/tools/misc/act/default.nix)

Global install:

```sh
nix-env -iA nixpkgs.act
```

or through `nix-shell`:

```sh
nix-shell -p act
```

Using the latest [Nix command](https://nixos.wiki/wiki/Nix_command), you can run directly :

```sh
nix run nixpkgs#act
```
