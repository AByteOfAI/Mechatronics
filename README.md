# Closed-Loop DC Motor Position Control using Simulink and Arduino

**Author:** Abhijit Sinha  
**Course:** RAS 550 — Final Project

## Overview

This project implements a closed-loop DC motor position control system on an Arduino microcontroller using Simulink for model-based design and real-time code generation. A quadrature encoder provides continuous position feedback, and the system is used to systematically tune and compare P, PD, and PID controllers for sinusoidal trajectory tracking.

## Demo

https://github.com/user-attachments/assets/demo.mp4

> A video demonstration of the motor tracking a sinusoidal reference signal under PID control is included in the repository (`final.mp4`).

## System Architecture

```
┌──────────────┐    ┌───────────┐    ┌────────────┐    ┌─────────────┐
│  Sine Wave   │───▶│  Error    │───▶│ Controller │───▶│ Motor Drive │
│  Reference   │    │ e = θref  │    │ (P/PD/PID) │    │ PWM + Dir   │
│  Generator   │    │   - θmeas │    │            │    │ (H-Bridge)  │
└──────────────┘    └───────────┘    └────────────┘    └──────┬──────┘
                          ▲                                    │
                          │         ┌──────────────┐           │
                          └─────────│  Quadrature  │◀──────────┘
                                    │  Encoder     │      DC Motor
                                    └──────────────┘
```

### Block Descriptions

1. **Command / Reference Generation** — Sine wave block producing a position trajectory (configurable amplitude and frequency).
2. **Measurement & Encoder Interface** — Reads quadrature encoder channels via Arduino, converts counts to position (degrees/revolutions) using counts-per-revolution and gear ratio.
3. **Error Generation** — Computes `e(t) = θ_ref(t) − θ_meas(t)`.
4. **Controller (P / PD / PID)** — Configurable PID block with tunable `Kp`, `Kd`, and `Ki` gains. Output is conditioned (absolute value, saturation) for PWM compatibility (0–255).
5. **Motor Drive & Direction Logic** — PWM duty cycle sets motor speed/torque; two digital pins control rotation direction through an H-bridge driver.
6. **Arduino Target & Execution** — Hardware configuration, solver/step-size settings, and automatic code generation for deployment.

## Controller Configurations Tested

| Controller | Kp | Kd  | Ki  | Data Type | Ts (s) | Frequency (Hz) |
|------------|-----|------|------|-----------|--------|-----------------|
| PD         | 5   | 1.5  | —    | single    | 0.01   | 0.2             |
| PD         | 9   | 1    | —    | single    | 0.01   | 0.2             |
| PD         | 9   | 1    | —    | double    | 0.005  | 0.2             |
| P          | 3   | —    | —    | double    | 0.005  | 0.2             |
| P          | 4   | —    | —    | single    | 0.01   | 0.1             |
| P          | 3   | —    | —    | single    | 0.01   | 0.1             |
| PID        | 3   | 1.5  | 0.5  | single    | 0.01   | 0.1             |
| PID        | 3   | 2    | 1    | single    | 0.01   | 0.1             |
| PID        | 3   | 2    | 1    | double    | 0.001  | 0.1             |

## Results Summary

- **P control** — Tracks the general shape of the sinusoid but exhibits significant phase lag and steady-state error, especially at higher frequencies.
- **PD control** — Derivative action reduces phase lag and improves transient response. Higher `Kp` with moderate `Kd` yields tighter tracking.
- **PID control** — Integral action reduces steady-state error. Best tracking performance observed with `Kp=3, Kd=2, Ki=1` (double precision, Ts=0.001s). Slight overshoot noted in the negative region for some configurations.

## Hardware

- Arduino (configured via Simulink Support Package for Arduino Hardware)
- DC motor with quadrature encoder
- H-bridge motor driver
- Encoder connected to Arduino digital pins (A/B channels)
- PWM output pin + two direction pins

## Software Requirements

- MATLAB / Simulink
- Simulink Support Package for Arduino Hardware
- Simulink Coder (for code generation)

## How to Run

1. Open the Simulink model (`.slx` file) in MATLAB.
2. Configure the Arduino board type and COM port under **Hardware Settings**.
3. Set the desired controller gains (`Kp`, `Kd`, `Ki`) in the PID block.
4. Click **Deploy to Hardware** to flash the model onto the Arduino.
5. Observe real-time tracking via the Simulink scope or workspace variables.

## Repository

**GitHub:** [AByteOfAI/Mechatronics](https://github.com/AByteOfAI/Mechatronics)

```
├── README.md
├── AbhijitRAS550final.pdf   # Full project report with diagrams and results
└── final.mp4                 # Video demonstration of the motor in action
```

## License

This project was completed as part of an academic course. Please cite appropriately if referencing this work.
