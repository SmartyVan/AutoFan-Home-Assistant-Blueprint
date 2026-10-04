# Auto Fan: Home Assistant Blueprint

[![Join our Discord](https://img.shields.io/discord/1303421267545821245?label=Join%20Discord&logo=discord)](https://discord.gg/3rqeqES3zP)
[![YouTube](https://img.shields.io/badge/YouTube-Smarty%20Van-red?logo=youtube&logoColor=white)](https://www.youtube.com/@SmartyVan)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-donate-yellow.svg)](https://www.buymeacoffee.com/smartyvan)

A Home Assistant automation blueprint that sets a fan's speed from a temperature sensor. It was written for the 10-speed MaxxAir fan and works with any Home Assistant `fan` entity.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FSmartyVan%2FAutoFan-Home-Assistant-Blueprint%2Fblob%2Fmain%2Fauto_fan.yaml)

## What it does

While **Auto Fan Control** is on, each update from the temperature sensor sets the fan speed:

- At or below the **Low Temperature Threshold**, the fan runs at **Minimum Fan Speed**.
- At or above the **High Temperature Threshold**, it runs at **Maximum Fan Speed**.
- Between the two, speed scales linearly and is rounded to a whole speed from 1 to 10.

With the defaults (72 °F to 85 °F, speeds 1 to 10), a reading of 80 °F gives speed 7.

Speeds are sent as percentages in steps of 10 (speed 7 = 70%). A fan with fewer speeds will map those percentages to its own steps.

## Manual override

Auto Fan Control is a switch or `input_boolean` you choose. It interacts with the fan like this:

| You do this | The blueprint does this |
|---|---|
| Turn Auto Fan Control on | Turns the fan on at the speed for the current temperature |
| Turn Auto Fan Control off | Turns the fan off |
| Change the fan speed yourself | Turns Auto Fan Control off; the fan stays at your speed |
| Turn the fan off yourself | Turns Auto Fan Control off |

Only changes made by a user count as manual. Speed changes made by the automation itself do not turn Auto Fan Control off.

## Optional fan cover sensor

If your fan has a lid or cover, you can select a `binary_sensor` that reports whether it is closed.

- **Cover closed:** the fan and Auto Fan Control are turned off. Temperature updates are ignored while it stays closed.
- **Cover opened:** Auto Fan Control is turned back on, which starts the fan at the speed for the current temperature.

By default the sensor's `on` state means closed. Door and window sensors (device class `door` or `opening`) usually report `on` when open; for those, turn on **Invert Cover Sensor**. Leave the cover sensor empty to skip this feature.

## Inputs

| Input | Description | Default |
|---|---|---|
| Fan Entity | The fan to control | — |
| Auto Fan Control | A `switch` or `input_boolean` that turns automatic control on and off | — |
| Fan Cover Sensor | Optional `binary_sensor` for the fan's lid or cover | none |
| Invert Cover Sensor | Treat the sensor's `off` state as closed | off |
| Temperature Sensor | A `sensor` with device class `temperature` | — |
| Threshold Temperature Unit | °F or °C, for the two thresholds below | °F |
| Low Temperature Threshold | Temperature for minimum speed | 72 |
| High Temperature Threshold | Temperature for maximum speed | 85 |
| Minimum Fan Speed | 1–10 | 1 |
| Maximum Fan Speed | 1–10 | 10 |

The sensor's unit is read from its `unit_of_measurement`. If it differs from the threshold unit, the reading is converted before comparison, so a °C sensor works with °F thresholds.

## Installation

1. Click the **Import Blueprint** button above, or in Home Assistant go to *Settings → Automations & Scenes → Blueprints → Import Blueprint* and paste:
   ```
   https://github.com/SmartyVan/AutoFan-Home-Assistant-Blueprint/blob/main/auto_fan.yaml
   ```
2. If you don't already have a switch for Auto Fan Control, create a Toggle helper (*Settings → Devices & Services → Helpers → Create Helper → Toggle*).
3. Create an automation from the **Smarty Van Auto Fan** blueprint and fill in the inputs.

## Notes

- The fan speed is only recalculated when the temperature sensor reports a new state, so how often the speed changes depends on how often your sensor updates.
- The automation runs in `queued` mode with up to 10 runs waiting.

## Related

- [MaxxAir-Fan-ESPHome](https://github.com/SmartyVan/MaxxAir-Fan-ESPHome): ESPHome firmware to control an IR MaxxAir fan from Home Assistant. It provides a fan entity and an Auto Fan switch for this blueprint.

---

Made by [Smarty Van](https://www.youtube.com/@SmartyVan). Free and open source. If it's useful to you, a [Smarty Van YouTube Membership](https://www.youtube.com/channel/UCtDZoJa423TcB1ri-ZjDoqA/join) helps support the work.
