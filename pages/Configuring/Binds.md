
## Table of contents
{{< toc >}}

## Basic

```ini
bind=MODS,key,dispatcher,params
```

for example,

```ini
bind=SUPER_SHIFT,Q,exec,firefox
```

will bind opening firefox to <key>SUPER</key> + <key>SHIFT</key> + <key>Q</key>

{{< hint type=tip >}}
For binding keys without a modkey, leave it empty:
```ini
bind=,Print,exec,grim
```
{{< /hint >}}

*For a complete mod list, see [Variables](../Variables/#variable-types).*

*The dispatcher list can be found in [Dispatchers](../Dispatchers).*

## Uncommon syms / binding with a keycode

See the
[xkbcommon-keysyms.h header](https://github.com/xkbcommon/libxkbcommon/blob/master/include/xkbcommon/xkbcommon-keysyms.h)
for all the keysyms. The name you should use is the one after `XKB_KEY_`,
written in all lowercase.

If you are unsure of what your key's name is, or what it shifts into, you can
use `xev` or `wev` to find that information.

If you want to bind by a keycode, you can just input it in the KEY position,
e.g.:

```ini
bind=SUPER,28,exec,amongus
```

Will bind <key>SUPER</key> + <key>T</key>. (<key>T</key> is keycode 28.) - You
can also use `xev` or `wev` to find keycodes.

## Misc

### Unbind
You can also unbind with `unbind`, e.g.:

```ini
unbind=SUPER,O
```

May be useful for dynamic keybindings with `hyprctl`.

### Mouse buttons
You can also bind mouse buttons, by prefacing the mouse keycode with `mouse:`,
for example:

```ini
bind=SUPER,mouse:272,exec,amongus
```

will bind it to <key>SUPER</key> + <key>LMB</key>.

### Only modkeys
For binding only modkeys, you need to use the TARGET modmask (with the
activating mod) and the `r` flag, e.g.:

```ini
bindr=SUPERALT,Alt_L,exec,amongus
```

### Mouse wheel
You can also bind the mouse wheel with `mouse_up` and `mouse_down`:
```ini
bind=SUPER,mouse_down,workspace,e-1
```
(control the reset time with `binds:scroll_event_delay`)

### Switches
Useful for binding e.g. the lid close/open event:
```
bindl=,switch:[switch name],exec,swaylock
```
check out your switches in `hyprctl devices`.

## Bind flags

bind supports flags in this format:

```ini
bind[flags]=...
```

e.g.:

```ini
bindrl=MOD,KEY,exec,amongus
```

flags:

```ini
l -> locked, aka. works also when an input inhibitor is active
r -> release, will trigger on release of a key
e -> repeat, will repeat when held.
m -> mouse, see below
```

## Mouse Binds
Mouse binds are binds that heavily rely on a mouse, usually its movement.
They will have one less arg, and look for example like this:

```
bindm=ALT,mouse:272,movewindow
```

this will create a bind with <key>ALT</key> + <key>LMB</key> to move the window
with your mouse.

*Available mouse binds*:

| Name | Description |
| -----|------------ |
| movewindow | moves the active window |
| resizewindow | resizes the active window |

*Common mouse buttons' codes:*
```
LMB -> 272
RMB -> 273
```

*for more, you can of course use `wev` to check.*

{{< hint type=tip >}}
Mouse binds, despite their name, behave like normal binds. You are free to use
whatever keys / mods you please. When held, the mouse function will be activated.
{{< /hint >}}

## Binding mods

You can bind a mod alone like this:

```ini
bindr=ALT,Alt_L,exec,amongus
```

## Global Keybinds
Yes, you heard this right, Hyprland does support global keybinds for ALL apps,
including OBS, Discord, Firefox, etc.

See the `pass` dispatcher for keybinds.

e.g.:

I've set the "Start/Stop Recording" keybind in OBS to <key>SUPER</key> +
<key>F10</key>, and I want it to be global.

Simple, add
```ini
bind = SUPER,F10,pass,^(com\.obsproject\.Studio)$
```
to your config and you're done.

`pass` will pass the PRESS and RELEASE events by itself, no need for a `bindr`.
This also means that push-to-talk will work flawlessly with one pass, e.g.:

```ini
bind=,mouse:276,pass,^(TeamSpeak 3)$
```

Will pass MOUSE5 to TeamSpeak3.

{{< hint type=important >}}
XWayland is a bit wonky. Make sure that what you're passing is a "global Xorg
keybind", otherwise passing from a different XWayland app may not work.

It works flawlessly with all native Wayland applications though.

*Side note*: **OBS** on Wayland really dislikes keybinds with modifiers. If
they don't work, try removing mods and binding them to e.g. <key>F1</key>.
Combining this with a submap should yield neat and usable results.
{{< /hint >}}

## Submaps

If you want keybind submaps, for example if you press <key>ALT</key> +
<key>R</key>, you can enter a "resize" mode, resize with arrow keys, and leave
with escape, do it like this:

```ini
# will switch to a submap called resize
bind=ALT,R,submap,resize

# will start a submap called "resize"
submap=resize

# sets repeatable binds for resizing the active window
binde=,right,resizeactive,10 0
binde=,left,resizeactive,-10 0
binde=,up,resizeactive,0 -10
binde=,down,resizeactive,0 10

# use reset to go back to the global submap
bind=,escape,submap,reset 

# will reset the submap, meaning end the current one and return to the global one
submap=reset

# keybinds further down will be global again...
```

**IMPORTANT:** do not forget a keybind to reset the keymap while inside it! (In
this case, `escape`)

If you get stuck inside a keymap, you can use `hyprctl dispatch submap reset` to
go back. If you do not have a terminal open, tough luck buddy. I warned you.

You can also set the same keybind to perform multiple actions, such as resize
and close the submap, like so:

```ini
bind=ALT,R,submap,resize

submap=resize

bind=,right,resizeactive,10 0
bind=,right,submap,reset
# ...

submap=reset
```

This works because the binds are executed in the order they appear, and
assigning multiple actions per bind is possible.
