# zmk-behavior-russian-capsword

A [ZMK](https://zmk.dev) behavior module providing a **caps word** tuned for the
Russian ЙЦУКЕН layout.

It behaves like the built-in [`&caps_word`](https://zmk.dev/docs/keymaps/behaviors/caps-word),
but also treats the Russian *letter* keys that sit on punctuation positions as alphabetic.
This is exactly what the 
[standard `&caps_word` fails to do for Russian](https://github.com/zmkfirmware/zmk/issues/1706):

| Key      | `&caps_word` (upstream) | `&rus_caps_word` (this module) |
|----------|:----------------------:|:-------------------------------:|
| `[` (Х)  | breaks / not caps'd   | continues, caps'd               |
| `]` (Ъ)  | breaks / not caps'd   | continues, caps'd               |
| `;` (Ж)  | breaks / not caps'd   | continues, caps'd               |
| `'` (Э)  | breaks / not caps'd   | continues, caps'd               |
| `,` (Б)  | breaks / not caps'd   | continues, caps'd               |
| `.` (Ю)  | breaks / not caps'd   | continues, caps'd               |
| `` ` `` (Ё) | breaks / not caps'd | continues, caps'd               |

The Shift modifier is applied to these keys exactly like it is for A–Z, so the host
OS produces the uppercase Cyrillic letters (ЙЦУКЕН maps Shift on those keys to Х Ъ Ж Э Б Ю Ё).

## Usage

### 1. Add the module

In your keymap repo's `west.yml`:

```yaml
manifest:
  remotes:
    - name: zmkfirmware
      url-base: https://github.com/zmkfirmware
  projects:
    - name: zmk
      remote: zmkfirmware
      revision: v0.3
      import: app/west.yml
    - name: zmk-behavior-russian-capsword
      url: https://github.com/graveric/zmk-behavior-russian-capsword
      revision: main
  self:
    path: config
```

Then run `west update`.

### 2. Enable the behavior

Add the predefined behavior instance to your keymap. Either include the shipped file:

```c
#include <behaviors/rus_caps_word.dtsi>
```

or define the node yourself:

```
/ {
    behaviors {
        rus_caps_word: rus_caps_word {
            compatible = "zmk,behavior-russian-capsword";
            #binding-cells = <0>;
            continue-list = <UNDERSCORE BACKSPACE DELETE>;
        };
    };
};
```

### 3. Bind it

In your keymap:

```
&rus_caps_word
```

### Configuration

- `continue-list` — an array of keycodes that keep caps word active (shared with the
  alphanumeric keys above). The default example ships `UNDERSCORE BACKSPACE DELETE`,
  the same as upstream `&caps_word`.
- `mods` — modifiers applied to the alphabetic keys, default `MOD_LSFT`.

Example override:

```
&rus_caps_word {
    mods = <MOD_LSFT>;
    continue-list = <UNDERSCORE MINUS BACKSPACE>;
};
```

## Compatibility

Targets ZMK `v0.3` (release tag `v0.3`, branch `v0.3-branch`). Requires the modern
`zmk_keycode_state_changed` event API (`implicit_modifiers`), available in all releases
from `v0.1`.

Like the built-in `&caps_word`, on split keyboards this behavior is compiled and runs
on the central (left) half only; the peripheral half is unaffected. This mirrors how
ZMK itself gates its `caps_word` behavior.

## Implementation

This module is a modified copy of ZMK's built-in
[`caps_word` behavior](https://github.com/zmkfirmware/zmk/blob/main/app/src/behaviors/behavior_caps_word.c);
the only functional change is that the alphabetic key set is extended with the Russian
layout's extra letter keys.

## License

MIT
