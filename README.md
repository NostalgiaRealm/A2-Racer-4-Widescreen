# A2 Racer 4 Widescreen Fix

This package contains a native 16:9 widescreen fix for the PC version of **A2 Racer 4**.

The fix has two parts:

1. a patched `spel.dat` executable module that makes the 3D game render correctly at a 16:9 resolution without horizontal stretching;
2. patched `overlay*.ini` files that reposition and unstretch the in-game HUD for a 16:9 layout.

The game uses `spel.dat` as the executable module for the DirectDraw/Direct3D racing portion of the game, even though the file extension is `.dat`.

<img width="2560" height="1440" alt="Screenshot_20260511_140825" src="https://github.com/user-attachments/assets/92287c97-9710-4b4e-a718-f0f4be975bc0" />
<img width="2560" height="1440" alt="Screenshot_20260511_141545" src="https://github.com/user-attachments/assets/aaebefbc-542e-44e1-bb16-28fb7f4f5704" />
<img width="2560" height="1440" alt="Screenshot_20260511_141559" src="https://github.com/user-attachments/assets/282e2b2b-f775-46b7-84f6-cd1c4b895779" />

## Files

### `A2Racer4_spel_dat_16x9_common_resolutions`

These are the fixed `spel.dat` files for use with a 16 by 9 resolution.

To use it, back up the original game file and replace the original `spel.dat` with this patched file. Rename the patched file to `spel.dat` if needed.

### `A2Racer4_DashboardFix`

This contains the patched overlay files:

```text
overlay0.ini
overlay1.ini
overlay2.ini
overlay3.ini
overlay4.ini
overlay5.ini
overlay6.ini
```

Extract these files into the same folder where the original overlay `.ini` files are located. Back up the originals first.

## What the `spel.dat` patch changes

The original game is built around 4:3 display modes such as 640×480 and 800×600. Simply forcing a 16:9 resolution makes the 3D image either stretched or vertically zoomed.

The patched `spel.dat` fixes this in two steps:

### 1. Native 16:9 display mode

The internal display-mode table was changed so the game can open a **16 by 9** mode instead of falling back to old 4:3 modes.

For the 2560×1440 build, for example, the relevant mode-table entries are redirected to:

```text
Width  = 2560
Height = 1440
```

### 2. Hor+ 3D projection correction

The 3D viewport/projection math was adjusted so the game keeps the correct proportions at 16:9.

The important part is that the patch does **not** just stretch the old 4:3 image. Instead, it preserves the vertical framing and expands the visible horizontal view. This is the desired **Hor+** widescreen behavior:

```text
Original 4:3:   normal vertical view, limited horizontal view
Patched 16:9:   same vertical view, more horizontal view
```

For the 2560×1440 patch, the aspect/projection calculation was adjusted around a 4:3 reference width of **1920** for a 1440-pixel-tall frame:

```text
1440 × 4 / 3 = 1920
```

This keeps the original 4:3 vertical framing while allowing the wider 2560-pixel output to show extra image on the sides.

An additional horizontal tuning factor was applied after testing. Resulting in a proper 16:9 image, no visible 3D stretching, and acceptable HUD behavior when combined with the patched overlay files.

## What the overlay `.ini` files do

The overlay `.ini` files control the HUD layout and many 2D on-screen elements. These include:

- speedometer
- turbo dial
- damage meter
- euro counter
- communicator panel
- character portrait popups
- race timer
- lap / round text
- position text
- direction arrows
- finish text styling

The original overlay files are laid out for a **640×480 4:3 virtual canvas**. When the game is rendered in 16:9, those HUD elements can become stretched, misplaced, or too close to the old 4:3 center area.

The patched overlay files keep the HUD usable and visually correct at 16:9.

## Main overlay changes compared with the original game files

### HUD horizontal unstretch

Most HUD image elements received an X-scale correction such as:

```ini
Scale = 0.75,1,1
```

This compensates for the 4:3-to-16:9 horizontal stretch while preserving the vertical size.

Text elements that had their own scale values were adjusted similarly, for example:

```ini
Scale = 0.525,0.7,0.7
Scale = 0.6,0.8,0.8
Scale = 0.45,0.6,0.6
```

The goal was to make text and sprites look proportionally correct rather than wide or flattened.

### Speedometer moved and corrected

The speedometer was moved toward the lower-right 16:9 screen edge and its internal parts were realigned.

Changed speedometer-related elements include:

```text
OVL_SPEEDOMETER_BODY
OVL_SPEEDOMETER_GLAS
OVL_SPEEDOMETER_STRIPES
OVL_SPEEDOMETER_HLTH
OVL_SPEEDOMETER_HLTH_BK
OVL_SPEEDOMETER_DIAL
OVL_SPEEDOMETER_DOP
OVL_SPEEDOMETER_RED_LED
OVL_SPEEDOMETER_RED_LUD
OVL_SPEEDOMETER_RED_GLOW
OVL_SPEEDOMETER_GRN_LED
OVL_SPEEDOMETER_GRN_LUD
OVL_SPEEDOMETER_GRN_GLOW
OVL_SPEEDOMETER_TURBODIAL
OVL_SPEEDOMETER_TURBODOP
STRING_DAMAGE_TXT
STRING_EURO_MONEY
STRING_EURO_SIGN
```

The speedometer dials, damage text, euro counter, and green/red indicator lights were individually tuned so they line up with the speedometer graphic.

### Communicator moved and corrected

The communicator panel was moved toward the lower-left 16:9 screen edge and its internal elements were aligned together.

Changed communicator-related elements include:

```text
OVL_COMMUNICATOR_BODY
OVL_COMMUNICATOR_GLAS1
OVL_COMMUNICATOR_GLAS2
OVL_COMMUNICATOR_HLTH
OVL_COMMUNICATOR_HLTH_BK
OVL_COMMUNICATOR_RED_LED
OVL_COMMUNICATOR_RED_LUD
OVL_COMMUNICATOR_RED_GLOW
STRING_0_COMMUNICATOR
STRING_1_COMMUNICATOR
STRING_2_COMMUNICATOR
STRING_3_COMMUNICATOR
```

The character portrait popup elements used by the communicator were also adjusted so they line up with the communicator panel.

### Player position portraits moved

The `[Portraits]` composite was moved to fit the 16:9 layout better.

The final tuned value is:

```ini
[Portraits]
Position = -90,-59,0
```

This places the player-position portrait stack near the upper-left edge without falling off-screen.

### Top-right race text moved

The race timer, current lap/round, and current position strings were moved toward the upper-right 16:9 edge.

Changed elements include:

```text
STRING_RACE_TIME_VALUE
STRING_CUR_LAP
STRING_CUR_POS
STRING_COUNTDOWN_TIMER
```

### Direction and turnaround indicators adjusted

Direction arrows and the turnaround indicator were moved and scaled for the wider output.

Changed elements include:

```text
OVL_DIRECTION_GOLEFT
OVL_DIRECTION_GORIGHT
OVL_DIRECTION_GOBOTH
OVL_DIRECTION_GOZIGZAG
OVL_TURNAROUND_FRAME
```

These use:

```ini
Position = 320,-30,0
Scale    = 1,1.25,1.25
```

### Finish text styling

The finish-position strings and finish prompt were adjusted in the `.ini` files.

Changed elements include:

```text
STRING_FINISH_POS_1
STRING_FINISH_POS_2
STRING_FINISH_POS_3
STRING_FINISH_POS_4
STRING_FINISH_POS_5
STRING_FINISH_TEXT
```

Important note: testing showed that the **scale/color** of the finish-position strings is read from the `.ini` files, but their **position appears to be overwritten by the game at runtime**. So the `.ini` position values for `STRING_FINISH_POS_*` may not affect the final in-game placement.

## Known limitation

The finish-position text shown at the end of a race appears to have its position controlled by executable code rather than only by the overlay `.ini` files.

During testing:

- changing `STRING_FINISH_POS_*` color worked;
- changing `STRING_FINISH_POS_*` scale worked;
- changing `STRING_FINISH_POS_*` position did **not** move the text.

This strongly suggests the game assigns finish-result positions dynamically at runtime.

## Installation

1. Back up the original game files:
   - `spel.dat`
   - `overlay0.ini` through `overlay6.ini`

2. Copy the patched `spel.dat` into the game folder.

3. Rename the patched file to:

```text
spel.dat
```

4. Copy `overlay0.ini` through `overlay6.ini` into the game folder, replacing the originals.

5. Start the racing part of the game normally.

## Recommended setup

The patched `spel.dat` fixes the 3D widescreen rendering.  
The patched overlay `.ini` files fix the HUD layout.

Using only one part of the fix is not recommended.

## Notes

- This fix was tuned for **2560×1440 16:9**. I was not able to test other resolutions yet.
- The overlay layout was manually tuned by in-game testing.
- The 3D fix is native and does not rely on stretching the image.
- Back up your original files before installing.
- This project does not include original game assets beyond modified configuration/executable patch files.
- All testing was done on Fedora Linux 44 running the game through Wine using DXVK and DgVoodoo in tandem.
