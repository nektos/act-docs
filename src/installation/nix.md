# [Nix](https://nixos.org) (Linux/macOS)

[Nix recipe](https://github.com/NixOS/nixpkgs/blob/master/pkgs/by-name/ac/act/package.nix)

Global install:

```sh
nix-env -iA nixpkgs.act
```

or through `nix-shell`:

```sh
nix-shell -p act
```

Using the latest [Nix command](https://wiki.nixos.org/wiki/Nix_command), you can run directly :

```sh
nix run nixpkgs#act
```
