# Sub-GHz Scheduler with on-device playlists

A Flipper Zero app for transmitting saved Sub-GHz signals on a schedule. This derivative of [Sub-GHz Scheduler by Patrick Edwards](https://github.com/shalebridge/flipper-subghz-scheduler), based on [upstream commit `62a526f` (v3.0)](https://github.com/shalebridge/flipper-subghz-scheduler/commit/62a526f52870c9b7e6b6f1fa5878d57711523155), adds an on-device playlist builder and tools to rename or delete saved playlists. It works with the official Flipper firmware; custom firmware is not required.

## What it does

- Build an ordered playlist by choosing saved `.sub` files one at a time, including the same file more than once.
- Save the playlist as a `.txt` file on the Flipper, then select it automatically for a schedule.
- Rename or delete saved playlists from the app. Deletion asks for confirmation.
- Use the original Scheduler features: single-file or playlist transmission, 1 to 6 transmissions per signal, internal or external CC1101 radio, interval and TX delay settings, and saved `.sch` schedules.

The playlist builder holds up to 32 signals. It does not edit the contents of a previously saved playlist. To change one, build and save a new list.

## Install

Download `subghz_scheduler.fap` from the [latest release](https://github.com/justinwlin/flipper-subghz-scheduler/releases/latest) and copy it to `/ext/apps/Sub-GHz/subghz_scheduler.fap` on the Flipper's SD card. You can use qFlipper's file manager or another SD card file manager. Open **Apps → Sub-GHz → Sub-GHz Scheduler**.

The downloadable build is for **official firmware 1.5.1, API 88.2, target f7**. A `.fap` must match the firmware API on the device. If yours differs, build from source using the matching uFBT SDK. Installing this fork at the path above replaces the catalog version of Scheduler on that SD card; reinstalling the catalog version replaces this fork.

## Build and transmit a playlist

1. Save or capture the signals you want as `.sub` files under `/ext/subghz/`.
2. Open **Sub-GHz Scheduler → Build Playlist**.
3. Choose **Add signal...**, select a `.sub` file, and repeat in transmission order. The numbered list shows the order. Use **Remove last** or **Clear list** to correct the draft.
4. Choose **Save playlist...** and enter a name. The app writes `/ext/subghz/playlist/<name>.txt`, selects it, and opens the schedule settings.
5. Set **TX Mode**, **Interval**, **TX Count**, and **TX Delay**, then choose **Start**.

**One-Shot** waits for the configured interval, transmits the full list once, then exits. The shortest interval is `00:00:01`. **Immediate** starts without that initial wait and continues transmitting at the interval until you stop it. **TX Delay** is a separate millisecond delay after each signal transmission.

To use an existing `.txt` playlist, choose **New Schedule → Select File**. You can also select a single `.sub` file there.

## Rename or delete a playlist

Open **Build Playlist → Manage playlists...**. The app lists `.txt` files in `/ext/subghz/playlist/`. Select one, then choose **Rename** or **Delete**. Rename refuses a name that already exists. Delete asks for confirmation and removes only the selected playlist file; it does not delete the `.sub` files it references.

If the playlist is selected in the current schedule, renaming updates that selection and deleting clears it. Previously saved `.sch` schedule files are not rewritten, so they may need their playlist selected again after a rename or deletion.

## Playlist file format

Each line is one absolute `.sub` path. Lines are sent in file order:

```text
sub: /ext/subghz/Biglight.sub
sub: /ext/subghz/Minilight.sub
sub: /ext/subghz/Singlelight.sub
```

The builder saves this same format under `/ext/subghz/playlist/`. A playlist can also be created or edited on a computer and copied to the Flipper.

## Build from source

Install [uFBT](https://github.com/flipperdevices/flipperzero-ufbt) and select the SDK channel that matches your Flipper firmware. From the repository root:

```sh
ufbt                 # builds dist/subghz_scheduler.fap
ufbt launch          # installs and launches it on a connected Flipper
```

Close qFlipper before `ufbt launch`, because both programs use the USB serial port. On a Mac where Homebrew Python blocks `pip install`, `uv tool install ufbt` is an alternative. This fork was built and launched with uFBT 0.2.6 against official firmware 1.5.1 / API 88.2.

## Known limits

- The app creates new playlists and can rename or delete existing ones, but it cannot insert, reorder, or remove individual entries in an existing saved playlist.
- One-Shot has a minimum one-second wait before it starts. Use Immediate for an immediate start with repeated runs.
- The published binary is verified for the firmware version stated above. Other firmware versions may require a rebuild.

## Credits and license

The original Scheduler was written by [Patrick Edwards (@shalebridge)](https://github.com/shalebridge/flipper-subghz-scheduler). This fork adds the on-device playlist features. The original project's [MIT license](LICENSE) is retained. See [CHANGELOG.md](CHANGELOG.md) for changes in this fork.
