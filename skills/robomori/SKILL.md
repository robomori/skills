---
name: robomori
description: Create, start and control Android emulators and devices with the robomori command. Use when an Android task needs an emulator or device, such as checking UI changes, testing dark mode, font size, language or TalkBack, simulating GPS location, taking screenshots or screen recordings, or running several emulators in parallel.
---

# Robomori

`robomori` creates and starts Android emulators and changes the state of running emulators and phones. Use it to get
a device ready and to capture what's on it. Keep using `adb` and Gradle for installing apps, tapping, typing and logs.

Check it's installed with `robomori version`. If it isn't, ask the user to install it from the Robomori app:
Settings → Command Line. Get the app at https://robomori.com.

## Rules

- Add `--json` when you read the output. Results go to standard output, progress and errors to standard error.
- Exit code 0 means it worked, 1 that it failed, 2 that the command was typed wrong. Errors name the command to run
  next; follow them.
- Pass `--device <name or serial>` whenever more than one device is running. With one running, leave it out.
- Don't stop, wipe or delete emulators you didn't create. They're the user's.
- Some commands say "Needs a license". If one fails for that reason, tell the user rather than working around it.

## Get a device

```sh
robomori device list --json                               # what exists and what's running
robomori emulator create pixel-10 --start --json          # a new phone, booted; prints its name and serial
robomori emulator start Pixel_10                          # start an existing one; prints its serial
robomori emulator open Pixel_10                           # also show it in a window, so the user can watch
```

`emulator create` takes a profile from `robomori profile list`: phones (`pixel-10`, `pixel-10-pro`), a foldable
(`pixel-10-pro-fold`), a tablet (`pixel-tablet`), watches and TV. `--api 36` picks the Android version; see
`robomori image list --profile <profile>`. Robomori gives each emulator 2 GB of memory, so several can run at once. When working
in parallel, create one emulator per task and pass its serial with `--device`.

The serial, such as `emulator-5554`, is what `adb -s` takes.

## Set the device up

```sh
robomori controls --json                                  # current values
robomori controls dark-mode on
robomori controls font-scale 1.3                          # 0.85, 1, 1.15, 1.3, 1.5, 1.8, 2
robomori controls locale fr-FR
robomori controls talkback on
robomori controls animations off                          # steadier UI tests and screenshots
robomori simulate location set 37.4220,-122.0841
robomori simulate location clear
robomori preset apply "French, large text"                # a preset the user saved in the app
```

## Act on it

```sh
robomori press back                                       # also home, recents, power
robomori rotate                                           # clockwise; or counterclockwise
robomori emulator fold Pixel_10_Pro_Fold                  # and unfold
robomori device restart
```

## Capture

```sh
robomori screenshot -o screen.png                         # then look at the image
robomori record --duration 30 -o flow.mp4
```

## When done

Stop the emulators you started with `robomori emulator stop <emulator>`, and delete the ones you created for a
task with `robomori emulator delete <emulator>`, unless the user wants to keep them.

## More

`robomori help <command>` shows every option of a command. Full docs: https://robomori.com/docs/cli.md
