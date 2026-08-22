# Automatic Solar Tracking System

An Arduino-based **single-axis automatic solar tracking system** designed to improve solar energy capture by continuously adjusting the orientation of a solar panel according to the direction of maximum sunlight.

The system uses **two LDR sensors**, positioned on opposite sides of the solar panel, to detect differences in light intensity. An Arduino processes these readings and controls a servo motor to rotate the panel toward the brighter side.

The system also includes **solar-panel voltage and current monitoring** and a **16×2 LCD display** for real-time system information.

---

## Project Overview

Conventional fixed solar panels remain at a fixed orientation throughout the day. Since the Sun continuously changes its position, the angle of incident sunlight on the panel also changes, which can reduce the amount of solar energy captured.

This project addresses this limitation using a simple embedded control system. Two LDRs are placed on opposite sides of the panel with a small divider between them. When one LDR receives more light than the other, the Arduino determines the direction in which the panel needs to move and commands the servo motor accordingly.

The process continues throughout the day, allowing the panel to follow the apparent movement of the Sun along a single axis.

---

## Objectives

* Develop a low-cost automatic solar tracking system.
* Detect the direction of maximum sunlight using two LDR sensors.
* Automatically adjust the solar-panel orientation using a servo motor.
* Maintain the panel closer to the optimum angle of incidence.
* Monitor solar-panel voltage and current.
* Display real-time system parameters on a 16×2 LCD.
* Demonstrate the application of embedded systems and automation in renewable-energy systems.

---

## Working Principle

The system uses two LDR sensors placed on opposite sides of the solar panel.

```text
                SUNLIGHT
                   ↓
          ┌─────────────────┐
          │                 │
       LDR LEFT          LDR RIGHT
          │                 │
          └───────┬─────────┘
                  │
             Arduino
                  │
             Servo Motor
                  │
                  ▼
            Solar Panel
```

The Arduino continuously reads the analog values from both LDRs and compares them.

### Tracking Logic

```text
LDR Left > LDR Right
        ↓
Panel rotates toward the LEFT

LDR Right > LDR Left
        ↓
Panel rotates toward the RIGHT

Difference within deadband
        ↓
Panel is sufficiently aligned
        ↓
Servo remains stationary
```

A small **deadband** is used to prevent unnecessary servo movement caused by small fluctuations in the LDR readings.

---

## System Operation

The tracking process follows a feedback loop:

```text
      Sunlight
         ↓
     Two LDRs
         ↓
   Light Intensity
      Comparison
         ↓
   Arduino Decision
         ↓
     Servo Motor
         ↓
   Panel Movement
         ↓
  New LDR Readings
         │
         └───────────↺
```

The Arduino repeatedly measures the two LDR values and adjusts the servo whenever the difference between them exceeds the defined threshold.

---

## Hardware Components

| Component             | Quantity | Purpose                            |
| --------------------- | -------: | ---------------------------------- |
| Arduino               |        1 | Main controller                    |
| LDR                   |        2 | Detect relative sunlight intensity |
| Servo Motor           |        1 | Rotate the solar panel             |
| Solar Panel           |        1 | Generate electrical power          |
| 10 kΩ Resistors       |        2 | LDR voltage-divider circuits       |
| ACS712 Current Sensor |        1 | Measure panel current              |
| Voltage Divider       |        1 | Measure panel voltage              |
| 16×2 LCD              |        1 | Display system parameters          |
| Breadboard            |        1 | Prototype assembly                 |
| Jumper Wires          |        — | Electrical connections             |

---

## Hardware Architecture

```text
                    ┌─────────────────┐
                    │   SOLAR PANEL   │
                    └────────┬────────┘
                             │
                   ┌─────────┴─────────┐
                   │                   │
              Voltage Divider       ACS712
                   │                   │
                   ▼                   ▼
              Arduino ADC          Arduino ADC
                   │                   │
                   └─────────┬─────────┘
                             │
        ┌────────────────────┴────────────────────┐
        │                 ARDUINO                 │
        │                                         │
        │     LDR Left ──┐                        │
        │                 ├── Light Comparison   │
        │     LDR Right ─┘                        │
        │                                         │
        │                 │                       │
        │                 ▼                       │
        │            Servo Control                │
        └─────────────────┬───────────────────────┘
                          │
                          ▼
                   SERVO MOTOR
                          │
                          ▼
                    PANEL ROTATION

                          │
                          ▼
                     16×2 LCD
```

---

## Sensor Processing

The two LDRs generate analog readings corresponding to the light intensity received from their respective sides.

The Arduino calculates the difference:

```text
ΔL = LDR_Left − LDR_Right
```

The tracking decision is based on this difference.

If the difference is larger than the defined deadband, the servo rotates the panel toward the side receiving greater illumination.

A deadband prevents continuous oscillation when both LDRs receive nearly equal light.

---

## Solar Panel Monitoring

### Current Measurement

An **ACS712 current sensor** is used to monitor the current generated by the solar panel.

The measured current can be calculated from the sensor output using:

```text
I = (Vout − Vzero) / Sensitivity
```

The sensor's zero-current offset can be calibrated during system startup.

### Voltage Measurement

The solar-panel voltage is measured through a resistor-divider circuit before being supplied to the Arduino's analog input.

The voltage-divider allows the Arduino to measure a panel voltage higher than its direct ADC input range.

---

## LCD Monitoring

The 16×2 LCD provides real-time information about the system.

Depending on the operating condition, the display can be used to show:

* Solar-panel current
* Solar-panel voltage
* Servo position
* System status
* Light-detection events

---

## Control Algorithm

```text
START
  │
  ▼
Initialize Arduino
  │
  ▼
Initialize LDRs, Servo,
LCD and Monitoring Sensors
  │
  ▼
Read Left LDR
  │
  ▼
Read Right LDR
  │
  ▼
Calculate Light Difference
  │
  ▼
Is Difference > Deadband?
       │
   ┌───┴───┐
   │       │
  YES      NO
   │       │
   ▼       ▼
Determine  Keep Servo
Direction  Stationary
   │
   ▼
Move Servo
Toward Brighter Side
   │
   ▼
Read Panel Voltage
and Current
   │
   ▼
Update LCD
   │
   └──────────────↺
```

---

## Testing and Observations

The implemented system was tested by changing the direction and intensity of light falling on the two LDR sensors.

Observed operation includes:

* Servo movement toward the side receiving greater light intensity.
* Stable positioning when both LDRs receive approximately equal illumination.
* Automatic panel repositioning when the light source changes direction.
* Real-time monitoring of electrical parameters through the LCD.
* Stable sensor operation using filtering and appropriate thresholding.
* Successful operation of the prototype under controlled lighting conditions.

---

## Prototype
[Solar Tracker Prototype](images/prototype.jpg)
```

Suggested photographs:

* Complete solar-tracker prototype
* Front view of solar panel
* Two-LDR arrangement
* Arduino and electronics
* Servo mechanism
* LCD display during operation
* Testing setup

---


## Software

The project is programmed using the **Arduino IDE**.

### Main Arduino Libraries

```cpp
#include <Servo.h>
#include <LiquidCrystal.h>
#include <math.h>
```

---

## Future Scope

The current prototype can be further improved through:

* IoT-based remote monitoring
* Cloud-based solar-energy data logging
* Real-time performance dashboards
* AI/ML-based solar optimization
* Automatic fault detection
* Improved energy-storage integration
* Higher-power solar-panel implementation
* More precise tracking mechanisms
* Integration with smart-grid systems

---

## Applications

The system can be adapted for:

* Small-scale photovoltaic systems
* Educational renewable-energy projects
* Embedded-system demonstrations
* Automated solar-energy harvesting
* Smart solar installations
* Remote renewable-energy systems

---

## Project Benefits

* **Automatic:** Requires minimal manual intervention.
* **Low Cost:** Uses commonly available electronic components.
* **Energy Efficient:** Continuously adjusts the panel orientation according to sunlight direction.
* **Embedded Control:** Demonstrates sensor interfacing and microcontroller-based decision making.
* **Scalable:** The concept can be extended to larger solar installations.
* **Renewable:** Supports improved utilization of solar energy.

---

## Documentation

The complete project documentation is available in the `docs/` directory.

* [Project Report](docs/project-report.pdf)
* [System Architecture](docs/system-architecture.png)
* [Circuit Diagram](docs/circuit-diagram.png)

---

## License

This project is developed for **educational and academic purposes**.

You are free to study, modify, and extend the project for learning and research.

## Author

Piyush Samuel M, Pragati Verma
