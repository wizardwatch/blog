---
title: wizardwatch-xmonad.cabal
---
```
cabal-version:      2.4
name:               wizardwatch-xmonad
version:            0.1.0.0

common common
  default-language:      Haskell2010
  default-extensions:
    LambdaCase
  ghc-options:
    -Wall

executable wizardwatch-xmonad
    import:           common
    main-is:          xmonad.hs
    build-depends:
        base,
        xmonad,
        xmonad-contrib,
        X11,
        containers,
        utf8-string,
```
