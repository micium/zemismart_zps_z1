# zemismart_zps_z1
ZHA quirk file 

**Implemented In The Quirk**

Added ZHA support for the Zemismart ZPS-Z1 / `_TZE284_ft7qqpx3` mmWave presence sensor, including:

- Presence state
- Illuminance
- Detection range
- Presence clear cooldown
- LED indicator
- Sensitivity preset: `high`, `medium`, `low`, `custom`
- Auto calibration command: `standby`, `start`, `cancel`
- Auto calibration status: `standby`, `start`, `learning`, `success`, `fail`, `cancel`
- Energy streaming switch
- Per-zone enable/disable for 10 zones
- Per-zone live motion/presence energy values
- Per-zone motion/presence threshold configuration

The quirk handles these Tuya datapoints:

- DP1: presence state
- DP2: detection range
- DP101: illuminance
- DP102: per-zone energy values
- DP103: auto calibration command/status
- DP104: energy streaming
- DP112: sensitivity preset
- DP117: zone active map
- DP119: no-person timeout / presence clear cooldown
- DP123: LED indicator
- DP124: per-zone motion/presence thresholds

**Auto Calibration Behavior**

The sensor requires energy streaming to be active for auto calibration to progress beyond `start`.

The quirk now automatically:

1. Enables energy streaming if it is currently off.
2. Sends DP103 `start`.
3. Tracks calibration status updates:
   - `start`
   - `learning`
   - `success`
   - `fail`
   - `cancel`
4. Turns energy streaming back off after calibration finishes, but only if the quirk enabled it automatically.

If energy streaming was already enabled manually, the quirk leaves it enabled.

**Calibration Instructions**

1. Restart Home Assistant after installing the quirk.
2. Wait for the sensor to wake up and report `auto_calibration_status = standby`.
3. Ideally wait around 10 minutes after pairing/restart before calibrating.
4. Make sure the room is empty and still.
5. Set `Auto calibration` to `start`.
6. Wait without changing other sensor settings.
7. Watch `Auto calibration status`.

Expected sequence:

```text
start -> learning -> success
```

A temporary `fail` can happen if the room is not empty, there is movement, or the sensor is still settling. Retry with the room empty.

After a successful calibration, `Sensitivity preset` may switch to `custom`. This is expected: the device has generated custom per-zone thresholds.
