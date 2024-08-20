# Etekcity ESF-551 BLE

This package provides a basic interface for interacting with [Etekcity ESF-551 Smart Fitness Scale](https://etekcity.com/products/smart-fitness-scale-esf551) using Bluetooth Low Energy (BLE). It allows you to easily connect to the scale, receive weight and impedance measurements, and manage the display unit settings. It is a very basic library, providing access to just the most fundamental functionality of the scale.

It has only been tested on the ESF-551 model. I have no idea whether it might also work with some other bluetooth bathroom scale models from Etekcity. If you try it with a different model, please let me know whether it works or not.

[![Buy Me A Coffee](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ronnnnnnn)



## Installation

Install the package using pip:

```bash
pip install etekcity_esf551_ble
```


## Quick Start

Here's a basic example of how to use the library:

```python
import asyncio
from etekcity_esf551_ble import (
    IMPEDANCE_KEY,
    WEIGHT_KEY,
    EtekcitySmartFitnessScale,
    ScaleData,
    WeightUnit,
)

async def main():
    def notification_callback(data: ScaleData):
        print(f"Weight: {data.measurements[WEIGHT_KEY]} KG")
        print(f"Display Unit: {data.display_unit.name}")
        if "impedance" in data.measurements:
            print(f"Impedance: {data.measurements[IMPEDANCE_KEY]} Ω")

    scale = EtekcitySmartFitnessScale("XX:XX:XX:XX:XX:XX", notification_callback)
    scale.display_unit = WeightUnit.KG  # Set display unit to kilograms

    await scale.async_start()
    await asyncio.sleep(30)  # Wait for measurements
    await scale.async_stop()

asyncio.run(main())
```


## API Reference

### `EtekcitySmartFitnessScale`

The main class for interacting with the scale.

#### Methods:

- `__init__(self, address: str, notification_callback: Callable[[ScaleData], None], display_unit: WeightUnit = None)`
- `async_start()`: Start scanning for and connecting to the scale.
- `async_stop()`: Stop the connection to the scale.

#### Properties:

- `display_unit`: Get or set the display unit (WeightUnit.KG, WeightUnit.LB or WeightUnit.ST). Returns None if the display unit is currently unknown (not set by the user and not yet received from the scale together with a stable weight measurement).
- `hw_version`: Get the hardware version of the scale (read-only).
- `sw_version`: Get the software version of the scale (read-only).

### `WeightUnit`

An enum representing the possible display units:

- `WeightUnit.KG`: Kilograms
- `WeightUnit.LB`: Pounds
- `WeightUnit.ST`: Stones

### `ScaleData`

A dataclass containing scale measurement data:

- `name`: Scale name
- `address`: Scale Bluetooth address
- `hw_version`: Hardware version
- `sw_version`: Software version
- `display_unit`: Current display unit (concerns only the weight as displayed on the scale, the measurement itself is always provided by the API in kilograms)
- `measurements`: Dictionary of measurements (currently supports: weight in kilograms and impedance in ohms)


## Compatibility

- Tested on Mac (Apple Silicon) and Raspberry Pi 4
- Compatibility with Windows is unknown


## Troubleshooting

On Raspberry Pi 4 (and possibly other Linux machines using BlueZ), if you encounter a `org.bluez.Error.InProgress` error, try the following in `bluetoothctl`:

```
power off
power on
scan on
```
(See https://github.com/home-assistant/core/issues/76186#issuecomment-1204954485)


## Support the Project

If you find this project helpful, consider buying me a coffee! Your support helps maintain and improve this library.

[![Buy Me A Coffee](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ronnnnnnn)


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
