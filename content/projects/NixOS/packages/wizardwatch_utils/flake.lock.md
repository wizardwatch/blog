---
title: flake.lock
---
```
{
  "nodes": {
    "flake-utils": {
      "locked": {
        "lastModified": 1638122382,
        "narHash": "sha256-sQzZzAbvKEqN9s0bzWuYmRaA03v40gaJ4+iL1LXjaeI=",
        "owner": "numtide",
        "repo": "flake-utils",
        "rev": "74f7e4319258e287b0f9cb95426c9853b282730b",
        "type": "github"
      },
      "original": {
        "owner": "numtide",
        "repo": "flake-utils",
        "type": "github"
      }
    },
    "nixpkgs": {
      "locked": {
        "lastModified": 1637841632,
        "narHash": "sha256-QYqiKHdda0EOnLGQCHE+GluD/Lq2EJj4hVTooPM55Ic=",
        "owner": "NixOS",
        "repo": "nixpkgs",
        "rev": "73369f8d0864854d1acfa7f1e6217f7d6b6e3fa1",
        "type": "github"
      },
      "original": {
        "id": "nixpkgs",
        "ref": "nixos-unstable",
        "type": "indirect"
      }
    },
    "root": {
      "inputs": {
        "flake-utils": "flake-utils",
        "nixpkgs": "nixpkgs"
      }
    }
  },
  "root": "root",
  "version": 7
}
```
