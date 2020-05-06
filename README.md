Kröhnkite
=========

[![AUR-git](https://img.shields.io/aur/version/kwin-scripts-krohnkite-git.svg?label=AUR-git)](https://aur.archlinux.org/packages/kwin-scripts-krohnkite-git/)

A dynamic tiling extension for KWin.

Kröhnkite is mainly inspired by [dwm][] from suckless folks, and aims to
provide rock solid stability while fully integrating into KWin.

The name of the script is from mineral [Kröhnkite][wikipedia]; it starts with
K and looks cool.

[dwm]: https://dwm.suckless.org/
[wikipedia]: https://en.wikipedia.org/wiki/Kr%C3%B6hnkite

![screenshot](img/screenshot.png)


Features
--------
 * DWM-like window tiling
    - Dynamically tile windows, rather than manually placing each.
    - Floating windows
 * Fully integrates into KWin features, including:
    - **Multi-screen**
    - **Activities & Virtual desktop**
    - Basic window management (minimize, fullscreen, switching, etc)
 * Multiple Layout Support
    - Tiling layout
    - Monocle layout
    - Desktop-friendly layouts (Spread, Stair)

Development Requirement
-----------------------

 * Typescript (tested w/ 3.1.x)
 * GNU Make
 * p7zip (7z)


Installation
------------

You can install Kröhnkite in multiple ways.

### Using .kwinscript package file ###

You can download `krohnkite-x.x.kwinscript` file, and install it through
*System Settings*.

 1. Download the kwinscript file
 2. Open `System Settings` > `Window Management` > `KWin Scripts`
 3. Press `Import KWin script...` on the top-right corner
 4. Select the downloaded file

Alternatively, through command-line:

    plasmapkg2 -t kwinscript -i krohnkite.kwinscript # installing new script
    plasmapkg2 -t kwinscript -u krohnkite.kwinscript # upgrading existing script

To uninstall the package:

    plasmapkg2 -t kwinscript -r krohnkite

### Installing from Git repository ###

The simplest method would be:

    make install
    make uninstall # to uninstall the script

This will automatically build and install kwinscript package.

You can also manually build package file using:

    make package

The generated package file can be imported from "KWin Script" dialog.

### Simply Trying Out ###

Krohnkite can be temporarily loaded without installing the script:

    make run
    make stop

Note that Krohnkite can destroy itself completely once it is disabled, so no
restart is required to deactivated it.

### Enabling User-Configuration ###

[It is reported][kwinconf] that a manual step is required to enable user
configuration of KWin scripts. This is a limitation of KWin scripting.

To enable configuration, you must perform the following in command-line:

    mkdir -p ~/.local/share/kservices5/
    ln -s ~/.local/share/kwin/scripts/krohnkite/metadata.desktop ~/.local/share/kservices5/krohnkite.desktop

A configuration button will appear in `KWin Scripts` in `System Settings`.

![config button shown](img/conf.png)

To make changes effective, **the script must be reactivate**:
  1) On `KWin Scripts` dialog, untick Krohnkite
  2) `Apply`
  3) tick Krohnkite
  4) `Apply`

[kwinconf]: https://github.com/faho/kwin-tiling/issues/79#issuecomment-311465357


Default Key Bindings
--------------------

| Key               | Action                         |
| ----------------- | ------------------------------ |
| Meta + J          | Focus Down/Next                |
| Meta + K          | Focus Up/Previous              |
| Meta + H          | Left                           |
| Meta + L          | Right                          |
|                   |                                |
| Meta + Shift + J  | Move Down/Next                 |
| Meta + Shift + K  | Move Up/Previous               |
| Meta + Shift + H  | Move Left                      |
| Meta + Shift + L  | Move Right                     |
|                   |                                |
| Meta + I          | Increase                       |
| Meta + D          | Decrease                       |
| Meta + F          | Toggle Floating                |
| Meta + \          | Cycle Layout                   |
|                   |                                |
| Meta + Return     | Set as Master                  |
|                   |                                |
| Meta + T          | Use Tile Layout                |
| Meta + M          | Use Monocle Layout             |
| *unbound*         | Use Spread Layout              |
| *unbound*         | Use Stair Layout               |


Tips
----

### Setting Up for Multi-Screen ###

Krohnkite supports multi-screen setup, but KWin has to be configured to unlock
the full potential of the script.

1. Enable `Separate Screen Focus` under `Window Management` >
   `Window Behavior` > `Multiscreen Behaviour`
2. Bind keys for global shortcut `Switch to Next/Previous Screen`
   (Recommend: `Meta + ,` / `Meta + .`)
3. Bind keys for global shortcut `Window to Next/Previous Screen`
   (Recommend: `Meta + <` / `Meta + >`)

Note: `Separate Screen Focus` appears only when multiple monitors are present.

### Removing Title Bars ###

Breeze window decoration can be configured to completely remove title bars from
all windows:

1. `System Setting` > `Application Style` > `Window Decorations`
2. Click `Configure Breeze` inside the decoration preview.
3. `Window-Specific Overrides` tab > `Add` button
4. Enter the followings, and press `Ok`:
   - `Regular expression to match`: `.*`
   - Tick `Hide window title bar`

### Changing Border Colors ###

Changing the border color makes it easier to identify current window. This is
convinient if title bars are removed.

1. Open `~/.config/kdeglobals` with your favorite editor
2. Scroll down and find `[WM]` section
3. Append the followings to the section:
    - `frame=61,174,233`: set the border color of active window to *RGB(61,174,233)*
    - `inactiveFrame=239,240,241`: set the border color of inactive window to *RGB(239,240,241)*

   Here's a nice 2-liner that'll do it for you:

        kwriteconfig5 --file ~/.config/kdeglobals --group WM --key frame 61,174,233
        kwriteconfig5 --file ~/.config/kdeglobals --group WM --key inactiveFrame  239,240,241
4. (Optionally) draw borders on maximized windows. Sometimes windows
   that are not in "monocle mode" as far as Krohkite is concerned, are
   maximized as far as KDE is concerned, and KDE does not draw borders
   on maximized windows by default. To change this,select `System
   Setting` \> `Window Decorations` > `Edit Breeze Decorations` (tiny
   pencil icon) > `General` > `Allow resizing maximized windows from
   window edges`
5. You must **restart** your session to see changes. (i.e. re-login, reboot)

(Note: the RGB values presented here are for the default Breeze theme)

### Script the Above and More ###

The following are some utility functions for those who prefer doing
the above steps from a shell script, and maybe want to add some tweaks
to their configuration pragmatically.

```sh
hex_rgb() {
    local css
    local printstring
    local hex="$(tr '[:lower:]' '[:upper:]' <<< ${1#\#})"
    # Convert ABC to AABBCC
    if [[ $hex =~ ^[A-F0-9]{3}$ ]]; then
        hex=$(sed -e 's/\(.\)/\1\1/g' <<< $hex)
    fi
    # If the first param is a valid hex string, convert to rgb
    if [[ $hex =~ ^[A-F0-9]{6}$ ]]; then
        # If second param exists and is a float between 0 and 1, output rgba
        if [[ -n $2 && $2 =~ ^(0?\.[0-9]+|1(\.0)?)$ ]]; then
            [[ $css ]] && printstring="rgba(%d,%d,%d,%s)" || printstring="%d,%d,%d,%s"
            printf $printstring  0x${hex:0:2} 0x${hex:2:2} 0x${hex:4:2} $2
        else
            [[ $css ]] && printstring="rgb(%d,%d,%d)" || printstring="%d,%d,%d"
            printf $printstring 0x${hex:0:2} 0x${hex:2:2} 0x${hex:4:2}
        fi
        # If it's not valid hex, return the original string
    else
        echo -n $@
    fi
}

# Function to create global kde shortcuts
bind () {
    local group cmd keys
    group=$1 && shift 1
    for cmd keys in $@; do
        kwriteconfig5 --file ~/.config/kglobalshortcutsrc \
                      --group "$group" \
                      --key "$cmd" \
                      "$keys"
    done
}

# Convenience wrapper around kwriteconfig5
configure () {
    local file type
    file=$1 && shift 1
    for group key value in $@; do
        # TODO: handle `path` type should you ever need it
        [[ $value = true || $value = false ]] && type=bool || type=string
        kwriteconfig5 --file $file --group $group --key $key --type $type $value
    done
}

# The Krohnkite tweaks mentioned above
configure ~/.config/kwinrc \
          Plugins krohnkiteEnabled true \ # turn krohnkite on
          org.kde.kdecoration2 library org.kde.breeze \ # Use breeze borders

configure ~/.config/breezerc \
          Windeco DrawBorderOnMaximizedWindows true \ # Always draw borders
          'Windeco Exception 0' BorderSize 3 \ # Normal Border size
          'Windeco Exception 0' Enabled true \
          'Windeco Exception 0' ExceptionPattern '.*' \
          'Windeco Exception 0' ExceptionType 0 \
          'Windeco Exception 0' HideTitleBar true \ # Hide title bars everywhere
          'Windeco Exception 0' Mask 16

# Pick border colors
ACTIVE_BCOLOR='#03a9f4'
INACTIVE_BCOLOR='#263238'

configure ~/.config/kdeglobals \
          WM frame $(hex_rgb $ACTIVE_BCOLOR) \
          WM inactiveFrame $(hex_rgb $INACTIVE_BCOLOR) \

# Create some handy bindings
bind kwin \
     'Window Close' 'Meta+Q,Alt+F4,' \
     'Krohnkite: Three Column Layout' 'Meta+C,none,'

# Make your favorite programs more reachable
bind emacsclient.desktop _launch 'Meta+E,none,Launch Emacsclient'
bind firefox.desktop _launch 'Meta+W,none,Launch Firefox'


# Deal with your input preferences
configure ~/.config/kxkbrc Layout Options 'caps:swapescape'
configure ~/.config/touchpadxlibinputrc \
          'DELL0808:00 06CB:7E92 Touchpad' tapToClick true
```

Useful Development Resources
----------------------------

 * [KWin Scripting Tutorial](https://techbase.kde.org/Development/Tutorials/KWin/Scripting)
 * [KWin Scripting API 4.9 Reference](https://techbase.kde.org/Development/Tutorials/KWin/Scripting/API_4.9)
 * KDE API Reference
    - [KWin::Workspace Class](https://api.kde.org/4.x-api/kde-workspace-apidocs/kwin/html/classKWin_1_1Workspace.html)
    - [KWin::Toplevel Class](https://api.kde.org/4.x-api/kde-workspace-apidocs/kwin/html/classKWin_1_1Toplevel.html)
    - [KWin::Client Class](https://api.kde.org/4.x-api/kde-workspace-apidocs/kwin/html/classKWin_1_1Client.html)
 * Adding configuration dialog
    - [Development/Tutorials/Plasma/JavaScript/ConfigDialog](https://techbase.kde.org/Development/Tutorials/Plasma/JavaScript/ConfigDialog)
    - [Development/Tutorials/Using KConfig XT](https://techbase.kde.org/Development/Tutorials/Using_KConfig_XT)
 * `*.ui` files can be edited with [Qt Designer](http://doc.qt.io/qt-5/qtdesigner-manual.html).
   It's very straight-forward if you're used to UI programming.
