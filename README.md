# CR-10-Smart-BLTouch-upgrade
Installing BLTouch on CR-10 Smart (CRC2405 V1.2) Without Replacing the Mainboard

# ✅ Installing BLTouch on CR-10 Smart (CRC2405 V1.2) Without Replacing the Mainboard

This guide shows you how to install a **BLTouch** on a **Creality CR-10 Smart** without changing the original **CRC2405 V1.2 mainboard**. It **should** work with **Creality V4.5x**. This is useful if you're using **Klipper** and have already replaced the toolhead, making the **stock strain gauge** unusable.

---

## ⚠️ WARNING

**DO NOT TRY TO REUSE STOCK STRAIN GAUGE PROBE'S PINS.**

The strain gauge probe is an **analog device**. It connects through an **ADC (Analog-to-Digital Converter)** on the CR-Z2 daughterboard. The pins **PA4** and **PA5** are **not reusable** (at least in practice — I couldn’t get them to work, and any misuse might **damage your board**).

---

## 🛠️ Steps

### 1. Install Klipper on the Wi-Fi Module

Use the guide from this GitHub repo to install Klipper on the CR-10 Smart's Wi-Fi board:

👉 [https://github.com/shivajiva101/KlipperWrt](https://github.com/shivajiva101/KlipperWrt)

You **do not** need replace the mainboard.

---

### 2. Use the Provided Klipper Config

Download the config from the [this](https://github.com/slavaz/klipper-cr10Smart) repo. It was originally written for the **Creality V4.5x** mainboard but works well on **CRC2405 V1.2**.

> ⚠️ Don't forget to calibrate:
> - `z_offset`
> - `rotation_distance` for the extruder
> - any other printer-specific values

With this config, the **stock strain gauge** will still work — unless you remove it (see next step).

---

### 3. Repurpose Existing Ports for BLTouch

The CRC2405 has limited I/O pins, so you’ll need to **sacrifice** some unused or less critical parts:

- 🔌 **Filament runout sensor**
- 🔌 **Z-axis optical endstop**

> DO NOT TRY to use LED pins, they will burn out your bl-touch

#### Wiring

| Component             | Function                  | Use for BLTouch          |
|----------------------|---------------------------|--------------------------|
| Filament Sensor       | Red = +5V                 | BLTouch VCC              |
|                      | Black = GND               | BLTouch GND              |
|                      | Yellow = Signal           | BLTouch control/servo    |
| Z Optical Endstop    | Middle = Signal           | BLTouch trigger input    |
|                      | Use multimeter to find GND|                          |

---

### 4. Update the `printer.cfg`

Edit your Klipper config file:

#### Remove the following sections:
```ini
[probe]
[filament_switch_sensor runout_sensor]  # If filament sensor is being reused
[output_pin lights]                     # If LED pin is reused
````

#### Add or modify the `[bltouch]` section:

```ini
[bltouch]
sensor_pin: ^PB2    # PB2 = Z endstop; PA7 = filament sensor; PA6 = LED
control_pin: PA7    # Servo control pin (connects to filament sensor yellow wire)
x_offset: 0.0
y_offset: 0.0
z_offset: 0.0       # Set this using Z-offset calibration
speed: 5.0
```

---

## 🎉 You're Done!

With this setup, your **BLTouch** should work with the stock **CRC2405 V1.2 board** under **Klipper**, no mainboard replacement required.

Just make sure to:

* Test probe deployment and retraction
* Perform full Z-offset calibration
* Validate mesh probing before starting your first print

Enjoy your upgraded CR-10 Smart!

---

