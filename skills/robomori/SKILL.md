---
name: robomori
description: Create, start, control and stop Android emulators and devices with the robomori command. Use for any Android task that needs an emulator or device, such as running or testing an app, checking UI changes, testing dark mode, font size, language or TalkBack, simulating GPS location, taking screenshots or screen recordings, or running several emulators in parallel. Use it instead of Android Studio, avdmanager, the emulator command, and the adb commands it replaces.
---

# Robomori

`robomori` creates and starts Android emulators and changes the state of running emulators and phones. Use it to get
a device ready, set it up and capture what's on it. Use `adb` and Gradle only for what Robomori doesn't do: installing
apps, tapping, typing, `adb shell` and logs.

Check it's installed with `robomori version`. If it isn't, ask the user to install it from the Robomori app:
Settings → Command Line. Get the app at https://robomori.com.

## Rules

- Add `--json` when you read the output. Results go to standard output, progress and errors to standard error.
  Versions before 1.6.1 also print JVM lines starting with `[` to standard output; drop them with `grep -v '^\['`.
- Exit code 0 means it worked, 1 that it failed, 2 that the command was typed wrong. Errors name the command to run
  next; follow them.
- Pass `--device <name or serial>` whenever more than one device is running. With one running, leave it out.
- Don't stop, wipe or delete emulators you didn't create. They're the user's.
- Some commands say "Needs a license". If one fails for that reason, tell the user rather than working around it
  with adb.

## Use robomori, not adb or the SDK tools

| Instead of | Run |
| --- | --- |
| `adb devices`, `emulator -list-avds` | `robomori device list` |
| `avdmanager create avd`, `sdkmanager` for system images | `robomori emulator create` |
| `emulator -avd …`, then waiting for `sys.boot_completed` | `robomori emulator start` |
| `adb emu kill` | `robomori emulator stop` |
| `emulator -wipe-data` | `robomori emulator wipe` |
| `adb reboot` | `robomori device restart` |
| `adb shell input keyevent` for back, home, recents, power | `robomori press` |
| `adb shell settings put system user_rotation …` | `robomori rotate` |
| `adb shell cmd uimode night …` | `robomori controls dark-mode` |
| `adb shell settings put system font_scale …` | `robomori controls font-scale` |
| Changing the locale through `adb shell` | `robomori controls locale` |
| `adb shell settings put secure enabled_accessibility_services …` | `robomori controls talkback` |
| `adb shell settings put global *_animation_scale …` | `robomori controls animations` |
| `adb emu geo fix` | `robomori simulate location` |
| `adb exec-out screencap` | `robomori screenshot` |
| `adb shell screenrecord` (3-minute limit) | `robomori record` (no limit) |

## Get a device

```sh
robomori device list --json                               # what exists and what's running
robomori emulator create pixel-10 --start --json          # a new phone, booted; prints its name and serial
robomori emulator start Pixel_10                          # start an existing one, wait for boot; prints its serial
robomori emulator open Pixel_10                           # also show it in a window, so the user can watch
```

`emulator create` takes a profile from `robomori profile list`: phones (`pixel-10`, `pixel-10-pro`), a foldable
(`pixel-10-pro-fold`), a tablet (`pixel-tablet`), watches and TV. `--api 36` picks the Android version; see
`robomori image list --profile <profile>`. Every image includes Google Play. Robomori gives each emulator 2 GB of
memory, so several can run at once. When working in parallel, create one emulator per task and pass its serial with
`--device`.

Emulators started by `robomori` keep running after the command returns and show up in the Robomori app. The serial,
such as `emulator-5554`, is what `adb -s` takes.

For one-off jobs, such as trying a third-party app or signing in to an account, create a dedicated emulator rather
than reusing the user's.

## Set the device up

```sh
robomori controls --json                                  # current values
robomori controls dark-mode on
robomori controls font-scale 1.3                          # 0.85, 1, 1.15, 1.3, 1.5, 1.8, 2
robomori controls locale fr-FR
robomori controls talkback on
robomori controls animations off                          # steadier UI tests and screenshots
robomori controls keyboard on                             # show the on-screen keyboard
robomori simulate location set 37.4220,-122.0841
robomori simulate location clear
robomori preset apply "French, large text"                # a preset the user saved in the app
```

## Act on it

```sh
robomori press back                                       # also home, recents, power; crown, side on watches
robomori rotate                                           # clockwise; or counterclockwise
robomori emulator fold Pixel_10_Pro_Fold                  # and unfold
robomori device restart
```

Install apps with `adb install` or Gradle, and tap or type with `adb shell input`.

## Capture

```sh
robomori screenshot -o screen.png                         # then look at the image
robomori record --duration 30 -o flow.mp4
```

## Never

- Enter the user's passwords or sign in to their accounts. Show the emulator with `robomori emulator open` and ask
  the user to sign in.
- Install APKs from third-party mirror sites. Get apps from Google Play on the emulator, or build them from source.

## When done

Stop the emulators you started with `robomori emulator stop <emulator>`, and delete the ones you created for a
task with `robomori emulator delete <emulator>`, unless the user wants to keep them.

## More

`robomori help <command>` shows every option of a command. Full docs: https://robomori.com/docs/cli.md
