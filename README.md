# r8

Fantasy console based on 6502 and rendered using Raylib.

![screenshot](./screenshot.png)

Mostly inspired by things like [Uxn](https://100r.co/site/uxn.html) but uses [6502](https://en.wikipedia.org/wiki/MOS_Technology_6502) as the underlying base VM. The environment your ROMs are running in is completely made up and doesn't correspond to any real hardware that ever existed (hense the "fantasy" part).

## Quick Start

```console
$ cc -o nob nob.c
$ ./nob
$ ./build/r8 ./build/examples/checker.rom
```

We have only tested on Linux so far. But [Windows](./tasks/20260801-113453/TASK.md) and [MacOS](./tasks/20260801-113457/TASK.md) supports are coming eventually.

## Controls

| Key               | Desciption             |
|-------------------|------------------------|
| <kbd>Ctrl+R</kbd> | Reload the current ROM |

You can Drag&Drop ROM files onto the window.

## ROM specs

ROMs are just binary files consisted of 6502 machine code instructions. Produce them with whatever assembler your heart desire (even manually if you feel spicy). We supply some binaries of [vasm](./vasm6502_oldstyle/) that we stole from [http://www.compilers.de/vasm.html](http://www.compilers.de/vasm.html). You can use them as a starting point. Check out [examples](./examples/) for some ROM assemblies.

### Entry point `$8000`

The ROMs are loaded at address `$8000` and start executing at `$8000`. So a basic ROM that does nothing and exits in vasm would look like:

```asm
    org $8000
init:
    rts
```

### Update Vector `$FFFE`

Update Vector is executed by r8 at a constant FPS. It is usually set in the Entry Point:

```asm
    org $8000
init:
    ; Set the Update Vector
    lda #<update
    sta $FFFE
    lda #>update
    sta $FFFE+1
    rts

update:
    ; Render a frame
    rts
```

### FPS config `$FFFD`

By default the Update Vector is ran at 5 FPS, but the ROMs can configure it:

```asm
    org $8000
init:
    ; Set the Update Vector
    lda #<update
    sta $FFFE
    lda #>update
    sta $FFFE+1

    ; Run the Update Vector at 30 FPS
    lda #30
    sta $FFFD

update:
    ; This should be run at 30 FPS
    rts
```

Lower 5 bits of `$FFFD` set the FPS the 6502 emulator is running at. The upper high bits are ignored for now.

### Canvas `$1000`

Canvas is a grayscale 8-bit 64 by 64 pixels image located at `$1000`. It is what's displayed by the main window of r8.

```asm
    org $8000
init:
    ; Set the Update Vector
    lda #<update
    sta $FFFE
    lda #>update
    sta $FFFE+1

    ; Draw something on the Canvas
    lda #$FF
    sta $1000+1
    sta $1000+3
    sta $1000+64*2+0
    sta $1000+64*2+4
    sta $1000+64*3+1
    sta $1000+64*3+2
    sta $1000+64*3+3
    rts

update:
    rts
```

### Keyboard `$2000`

TBD

### Mouse `$2080`

TBD

### Sound chip `$3000`

TBD

## Special Thanks

Special Thanks goes to codecat69, sushi, and many other chatters in the Twitch chat who were patient with me and provided invaluable help as I was impatiently familiarizing myself with the wonders of 6502 raw assembly programming ^^"
