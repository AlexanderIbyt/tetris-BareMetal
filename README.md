# Tetris

A complete Tetris game written in ibyt, small enough to fit in a **12 KB boot image**. It runs on bare metal: boot a PC from a USB stick (or start it in an emulator) and play. There is no operating system underneath. The game draws in a 32-bit VESA graphics mode, reads the keyboard itself, keeps time with the PC timer and plays sound on the PC speaker.

The source is also meant as a readable example of ibyt style: loops are `loop`, decisions are `if / else`, and registers carry names that say what is inside (`@Bit`, `@Mask`, `@Cell`, `@PieceX` ...). They are still plain registers, there is no hidden work and no optimizer.

## Controls

| Key | Action |
|---|---|
| Left / A | move left |
| Right / D | move right |
| Up / W | rotate (with wall kick) |
| Down / S | fall one step faster (+1 point) |
| Space | drop at once |
| P | pause / continue |
| Q or Esc | quit |
| R | new game (after game over) |

## Rules

- The field is 10 x 20. The next piece is shown on the right.
- Points for rows cleared with one piece: 1 row = 100, 2 = 300, 3 = 500, 4 = 800, each multiplied by (level + 1).
- Every 10 lines the level goes up and the pieces fall faster.
- The best score is kept until you quit the program.
- Sound: a low tone when a piece lands, rising tones for cleared rows, a fanfare for four rows at once and a falling tune for game over.

## Settings

The block at the top of `Main.ibyt` can be changed, then compile again:

| Name | Default | Meaning |
|---|---|---|
| `DROP_START` | 800 | fall time in level 0, in ms |
| `DROP_MIN` | 100 | fastest fall time, in ms |
| `DROP_STEP` | 70 | so many ms faster per level |
| `LINES_PER_LEVEL` | 10 | lines until the next level |

## Building

You need the ibyt compiler (version 1.32 was used) and the ibyt `library` folder next to the source.

```
cd tetris
<ibyt-compiler>          # produces MyProgram.img
```

Write the image to a stick with **ibytWriter**, or by hand (`/dev/sdX` is the whole stick, **not your hard disk**):

```
sudo dd if=MyProgram.img of=/dev/sdX bs=1M conv=fsync status=progress
```

To try it in an emulator:

```
qemu-system-x86_64 -drive format=raw,file=MyProgram.img -vga std
```

On a real PC boot from the stick in BIOS / Legacy / CSM mode (Secure Boot off). Tetris asks the BIOS for 1024 x 768, then 800 x 600, then 640 x 480 with 32 bits per pixel and takes the first one that exists. If none exists it prints a message and stops.

## Tested

In QEMU: moving, rotating, falling, hard drop, pause and continue, clearing one to four rows (also several full rows next to each other), game over, new game with `R`, quit with `Q`. The screen was compared pixel by pixel with an earlier version of the same game after the same key sequence.

**Not tested yet:** real hardware (graphics mode, keyboard, speaker) and how the sound actually sounds.

## Limits (version 1)

- Needs a BIOS with a 32-bit VESA graphics mode. No UEFI-only boot.
- Keyboard only, no mouse.
- The best score is not saved to disk.
- One game mode, no two-player mode, no hold piece.

## No warranty

Tetris is provided as it is, without any warranty (see `LICENSE`).

## License

Tetris is released under the **MIT License**, see the file `LICENSE`. You may use, change and share it freely, also commercially, as long as the copyright notice and the license text are kept.

Copyright (c) 2026 Alexander Zima, https://www.ibyt.tech
