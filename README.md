# OP1 8k v2 Web Config (unofficial)

A browser-based configurator for the **Endgame Gear OP1 8k v2** mouse, so it can be set up on Linux (or anywhere) without the Windows-only official tool.

**Open it:** https://xxsinkingxxv2-glitch.github.io/op1-8k-v2-config/

Not affiliated with or endorsed by Endgame Gear.

## Features

Everything from the official configuration tool v1.07 except firmware updates:

- **Basic:** polling rate (125–8000 Hz), lift-off distance, angle snapping, LED off on lift-off, 1–4 CPI stages (10–30000, optional separate X/Y)
- **Advanced Sensor:** Motion Sync, Motion Jitter Filter, force max sensor fps, CPI downshift tuning, ripple control, sensor angle tuning
- **Buttons:** per-button multiclick filter, SPDT modes (GX Speed / GX Safe), slamclick filter
- **Button Mapping:** mouse buttons, scroll, CPI loop, fixed CPI, media keys, browser/explorer, keyboard keys with modifiers, disable, left-handed mode
- **Device:** firmware version, save/load settings to a file, factory reset

Nothing is written to the mouse until you press **Apply**.

## Requirements

- A Chromium-based browser (Chrome, Chromium, Edge, Brave, Helium). Firefox and Safari don't support WebHID.
- **Linux only:** give your user access to the mouse once with the included udev rule:

  ```sh
  sudo cp 70-endgamegear.rules /etc/udev/rules.d/
  sudo udevadm control --reload && sudo udevadm trigger
  ```

## Running locally

WebHID needs HTTPS or `localhost`, so serve the folder instead of opening the file directly:

```sh
python3 -m http.server 8765 --bind 127.0.0.1
# then open http://127.0.0.1:8765/
```

## Protocol notes

The mouse is configured through two feature reports on its vendor HID collection (usage page `0xFF01`, interface 1):

| Report | Size | Use |
|---|---|---|
| `0xA1` | 63 bytes | command / status (`byte0`: command out, status in: `1` = OK, `3` = busy) |
| `0xA0` | 1040 bytes | bulk config transfer, 1024-byte config block at offset 15 |

Commands: `0x02` info, `0x11` write config, `0x12` read config, `0x13` factory reset. The config block layout is documented in the `O` table and `stageOfs`/`btnOfs` helpers in `index.html`.
