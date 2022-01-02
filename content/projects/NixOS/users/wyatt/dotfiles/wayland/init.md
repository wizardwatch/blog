---
title: init
---
```
#!/bin/sh

# Modifier Options
mod="Mod1"
primary=$mod
secondary="$primary+Shift"
tertiary="$secondary+Control"

# ScratchPads
MusicTag=$((1 << 10 )) 
TopTag=11


# Launch on startup
riverctl spawn 'foot ncmpcpp'
riverctl spawn foot
riverctl spawn firefox
#riverctl spawn dbus-daemon --session --address=unix:path=$XDG_RUNTIME_DIR/bus
riverctl spawn mako
riverctl spawn 'eww daemon'
riverctl spawn 'eww open time'

# Primary bindings
riverctl map normal $primary Return spawn foot
riverctl map normal $primary Q close
riverctl map normal $primary N spawn fuzzel
riverctl map normal Mod4 E exit
riverctl map normal $primary Tab Return zoom
riverctl map normal $primary M toggle-focused-tags $MusicTag
riverctl map normal $primary Tab zoom

# TODO: Bind to scroll wheel
#riverctl map normal $mod  focus-view next
#riverctl map normal $mod E focus-view previous

# Secondary Bindings
riverctl map normal $secondary M set-view-tags $MusicTag

# move/resize
riverctl map-pointer normal $mod BTN_LEFT move-view
riverctl map-pointer normal $mod BTN_RIGHT resize-view

# toggle float/fullscreen
riverctl map normal $mod Space toggle-float
riverctl map normal $mod F toggle-fullscreen

# multi-output TODO: Bind to jkl; 
riverctl map normal $mod Period focus-output next
riverctl map normal $mod Comma focus-output previous
riverctl map normal $mod+Shift Period send-to-output next
riverctl map normal $mod+Shift Comma send-to-output previous

# screenshots
riverctl map normal $mod C spawn 'grim -g "$(slurp)" - | wl-copy'
riverctl map normal $secondary C spawn '/etc/nixos/common/scripts/wlrootsScreen.sh'
riverctl map 
# tags
for i in $(seq 1 9); do
    tags=$((1 << ($i - 1)))
    riverctl map normal $mod $i set-focused-tags $tags
    riverctl map normal $mod+Shift $i set-view-tags $tags
    riverctl map normal $mod+Control $i toggle-focused-tags $tags
    riverctl map normal $mod+Shift+Control $i toggle-view-tags $tags
done
tags1to9=$(((1 << 9) - 1))
riverctl map normal $mod 0 set-focused-tags $tags1to9
riverctl map normal $mod+Shift 0 set-view-tags $tags1to9

# passthrough mode for nested sessions
riverctl declare-mode passthrough
riverctl map normal $mod Semicolon enter-mode passthrough
riverctl map passthrough $mod Semicolon enter-mode normal
#cp -rLpT ~/.local/sv $svdir
#cp -rLpT ~/.local/sv $svdir

#riverctl set-repeat 80 300

#riverctl xcursor-theme Adwaita

# float stuff with app_id "float"
riverctl float-filter-add "float"
riverctl float-filter-add "popup"

riverctl default-layout kile

# run an svdir to supervise stuff like rivertile, foot --server, etc

# TODO: /run is mounted noexec so can't use XDG_RUNTIME_DIR
#svdir="/tmp/$(id -u)-sv-${WAYLAND_DISPLAY}"

#pgrep -f "^runsvdir ${svdir}" &&
#    exec echo "runsvdir already running for wayland session"

# recursive, deref links, preserve mode, if target is a dir, overwrite
# instead of copying into it
wlr-randr --output HDMI-A-1 --pos 700,1440 --scale 1
wlr-randr --output DP-1 --pos 0,0

riverctl focus-follows-cursor normal
riverctl spawn kile -l "(3 < v ? ((v: h d) 2 .5 .2))"
riverctl border-width 4
riverctl border-color-focused 0xbd93f9
riverctl border-color-unfocused 0x44475a
riverctl background-color 0x282a36
```
