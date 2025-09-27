# FM TX MVP (Android, Rooted, Qualcomm-oriented)

This MVP attempts to toggle FM Transmit mode on certain rooted Android devices (historically Qualcomm-based) by invoking root shell commands and common vendor hooks. This mirrors the Symbian "Play via Radio" concept described here: [All About Symbian: Play via Radio](http://www.allaboutsymbian.com/features/item/13261_A_great_name_change_and_a_feat.php).

## Hard constraints

- Modern Android OEMs typically disable FM TX or omit the hardware/routing to the antenna. There is no public Android API.
- This app requires a rooted device with a ROM that exposes FM TX plumbing (device nodes, vendor services, or init scripts). Results will vary wildly.
- iOS is not supported.

## Build

1. Open the `android-fm-tx` project in Android Studio (Giraffe+ recommended).
2. Build the `app` module and install to a rooted test device.

Or via CLI:

```bash
./gradlew :app:installDebug
```

## Usage

- Launch the app, enter a frequency in MHz (e.g., `102.3`), tap Start.
- Tap Stop to attempt to disable.
- Status text will reflect basic success/failure from root calls.

## Implementation overview

- `RootShell` runs commands as `su -c` and captures stdout/stderr/exitCode.
- `QualcommFmTransmitter` issues experimental commands commonly seen on older Qualcomm ROMs (e.g., `fm_qsoc_patches`, `setprop hw.fm.*`). These are placeholders and may require customization per device/ROM.
- `MainActivity` wires a minimal UI to the transmitter.

## Customization (device-specific)

- Inspect `logcat` and `/system/etc/init*` scripts for actual FM service names.
- Replace or augment commands in `QualcommFmTransmitter.start()`/`stop()` to match your environment, e.g.:
  - Vendor service names (e.g., `start fm_hal_service`)
  - Properties (e.g., `setprop vendor.hw.fm.mode 1`)
  - Binaries (e.g., `/vendor/bin/fm_qsoc_patches`)
  - Device nodes (e.g., ioctls on `/dev/radio0` via a small native shim)

## Known limitations

- Audio routing to RF path is not guaranteed. Additional mixer controls (ALSA/Audio HAL) may be needed.
- Frequencies and power are region dependent. Ensure legal compliance.
- Many devices simply cannot TX (hardware missing or antenna path not connected).

## Legal note

Operating FM transmitters may be regulated. You are responsible for compliance with local laws.

## Credits

- Inspired by Nokia/Symbian "Play via Radio" behavior described here: [All About Symbian: Play via Radio](http://www.allaboutsymbian.com/features/item/13261_A_great_name_change_and_a_feat.php)

