# Intech Grid Controller Profiles

Profiles for two Intech Grid modules (EF44 + PBF4) to control:
- **Zoom LiveTrak L6 Max** — 4-channel mixer control with tilt EQ, mute, pan, mid EQ
- **Apple Music + YouTube** — Media playback, volume, seek, speed, love, fullscreen

## Hardware

- **Module 1 (EF44):** 4 encoders + 4 faders (no buttons)
- **Module 2 (PBF4):** 4 potentiometers + 4 faders + 4 buttons
- **Zoom LiveTrak L6 Max**
- A computer running Grid Editor + MIDI routing software

## Layout

### Module 1 — EF44 (Tilt EQ + Volume)

| Element      | Function              |
|--------------|-----------------------|
| Encoders 1-4 | Channel 1-4 Tilt EQ  |
| Faders 1-4   | Channel 1-4 Volume   |

Tilt EQ: one encoder controls Low EQ and High EQ in opposite directions. Center position (64) = flat. Turn right = brighter (more highs, less lows). Turn left = warmer (more lows, less highs). Encoders start at center on power-up.

### Module 2 — PBF4 (Pan + Mid EQ + Mute)

| Element     | Function                                          |
|-------------|---------------------------------------------------|
| Pots 1-4    | Channel 1-4 Mid EQ                               |
| Faders 1-4  | Channel 1-4 Pan                                  |
| Buttons 1-4 | Short press: Mute toggle / Long press (500ms): Reset tilt EQ |

### LED Colors

| Element   | Color       | Meaning            |
|-----------|-------------|---------------------|
| Encoders  | Cyan        | Tilt EQ             |
| Faders (EF44) | Orange  | Volume              |
| Pots      | Purple      | Mid EQ              |
| Faders (PBF4) | Teal   | Pan                 |
| Buttons   | Green       | Unmuted             |
| Buttons   | Red         | Muted               |
| Buttons   | Blue        | Tilt EQ just reset  |

## Importable Profile Files

The easiest way to set up your Grid modules:

- **`ef44-tilt-eq-volume.json`** — Import into Grid Editor for your EF44 module
- **`pbf4-pan-mideq-mute.json`** — Import into Grid Editor for your PBF4 module

In Grid Editor: connect your module, go to Profile Cloud / Import, and load the JSON file.

## CC Number Assignments

Set these in the Zoom L6 Editor to match.

| Parameter | Ch 1 | Ch 2 | Ch 3 | Ch 4 |
|-----------|------|------|------|------|
| Volume    | 1    | 7    | 13   | 19   |
| Low EQ    | 2    | 8    | 14   | 20   |
| High EQ   | 3    | 9    | 15   | 21   |
| Mute      | 4    | 10   | 16   | 22   |
| Pan       | 5    | 11   | 17   | 23   |
| Mid EQ    | 6    | 12   | 18   | 24   |

All on MIDI channel 1 (channel 0 in Grid Lua code).

## Setup Instructions

### 1. Configure the Zoom L6 Max

1. Connect the L6 Max to your computer via USB-C
2. Open the Zoom L6 Editor software
3. Assign the CC numbers from the table above to each parameter for channels 1-4

### 2. Import Grid Profiles

1. Connect your Grid modules to your computer
2. Open Grid Editor
3. Import `ef44-tilt-eq-volume.json` to your EF44 module
4. Import `pbf4-pan-mideq-mute.json` to your PBF4 module

Alternatively, you can manually paste the Lua code from the `module1-ef44/` and `module2-pbf4/` directories into each element's action blocks.

### 3. Route MIDI

Both the Grid and L6 Max are USB MIDI devices (neither is a host), so you need a computer to route MIDI between them.

**macOS options:**
- [MIDI Patchbay](https://github.com/notahat/midi_patchbay) (free)
- Audio MIDI Setup (built-in) with IAC Driver
- Any DAW with MIDI routing

Route the Grid's MIDI output to the L6 Max's MIDI input.

## Element Mapping Reference

### EF44 (8 elements)

| Element # | Physical Control |
|-----------|-----------------|
| 0-3       | Encoders (top)  |
| 4-7       | Faders (bottom) |

### PBF4 (12 elements)

| Element # | Physical Control   |
|-----------|--------------------|
| 0-3       | Potentiometers (top) |
| 4-7       | Faders (middle)    |
| 8-11      | Buttons (bottom)   |

## Media Control Profiles (Apple Music + YouTube)

Requires the Grid Editor packages:
- [package-apple-music](https://github.com/brentvatne-agent/package-apple-music)
- [package-chrome-youtube](https://github.com/brentvatne-agent/package-chrome-youtube)

Import these profiles as a second page on your Grid modules (use alongside the L6 Max profiles on page 1).

- **`ef44-media-control.json`** — EF44 media control profile
- **`pbf4-media-control.json`** — PBF4 media control profile

### Media Control Layout — EF44

| Element    | Function               | LED Color |
|------------|------------------------|-----------|
| Encoder 1  | YouTube Volume up/down | Orange    |
| Encoder 2  | YouTube Seek ±10s      | Cyan      |
| Encoder 3  | YouTube Playback speed | Purple    |
| Encoder 4  | YouTube Mute (press)   | Gray      |
| Faders 1-4 | Unused                 | Dim       |

Encoders are in relative mode — turning sends discrete commands, no absolute position.

### Media Control Layout — PBF4

| Element    | Function                                      | LED Color |
|------------|-----------------------------------------------|-----------|
| Pots 1-4   | Unused                                        | Dim       |
| Faders 1-4 | Unused                                        | Dim       |
| Button 1   | Play/Pause (Apple Music + YouTube)             | Green     |
| Button 2   | Previous (track or video)                      | Blue      |
| Button 3   | Next (track or video)                          | Blue      |
| Button 4   | Short press: Love (Apple Music) / Long press: Fullscreen (YouTube) | Red/Blue |

Buttons send commands to both Apple Music and YouTube packages — whichever app is active will respond.

## Notes

- The long press tilt EQ reset sends CC values to the L6 Max but cannot physically reposition the encoder on Module 1. The encoder's internal value will be out of sync until you turn it, at which point it will "jump" to the new position.
- L6 Max profiles use MIDI channel 0 (channel 1 in standard MIDI terminology). Edit the first parameter of `gms()` calls if you need a different channel.
- Media control profiles use `gps()` to send commands to the Grid Editor packages. The packages must be installed in Grid Editor for these to work.
- Code uses Grid shortname aliases (e.g., `gms` = `midi_send`, `glc` = `led_color`) to fit within the 400-character action chain limit.

## Individual Lua Files

For reference or manual setup, individual Lua code blocks for the L6 Max profiles are in:
- `module1-ef44/` — encoder and fader scripts
- `module2-pbf4/` — pot, fader, and button scripts

Files are named `{element}{number}-{event}.lua` (e.g., `knob1-encoder.lua`, `button1-button.lua`).
