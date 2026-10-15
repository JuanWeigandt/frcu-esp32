# 📌 UTN FRCU – Tecnologías para la Automatización ESP32 2026

**Air Mouse ESP32**

---

## 👥 Team

- **Team number:** 5
- **Members:**
  - Aldecoa, Ludmila
  - Bertoldi, Juliana
  - Braun Molina, Magali
  - Rey, Florencia
  - Scheffer, Catalina

---

## 🤖 Project Description

### Description

The present work consists of the development of a **Wireless Mouse** based on the ESP32 microcontroller, designed to allow free control of a computer's cursor through physical movements without using a conventional mouse.

The device uses an analog Joystick to control the cursor displacement, two buttons to perform the main clicks, and a rotary encoder to scroll vertically. Additionally, the device incorporates an RGB LED and a buzzer to provide visual and acoustic feedback to the user. 

The ESP32 processes the signals from the various components and communicates with the computer via Bluetooth Low Energy (BLE) emulating a standard HID mouse. The project was developed and tested using Arduino IDE and Wokwi, verifying both the functionality and the implemented logic.

#### Technology Used
ESP32 (Espressif Arduino Core), Arduino IDE 2.x, Wokwi Simulator, C++ / Arduino Framework, `BleMouse.h` library.

#### Scope Validation
Validated according to the requirements of the teaching staff for the Microcontrollers and RPA / Automation Technologies course.

---

## 🔩 Hardware Architecture & Components

| Component | Characteristics | Mode | Function |
| :--- | :--- | :--- | :--- |
| **ESP32** | Dual-core microcontroller with Wi-Fi and native BLE Bluetooth[cite: 32]. | All modes | **Input Processing:** Reads analog (joystick) and digital data (buttons/encoder).<br>**BLE HID Connectivity:** Emulates a native Bluetooth mouse without drivers or USB receivers.<br>**Power Saving:** Manages Deep Sleep state by inactivity and wakes up via hardware events.<br>**HMI Control:** Synchronizes visual (RGB LED) and auditory (Buzzer) feedback. |
| **Joystick XY (KY-023)** | 2-axis analog module with proportional voltage outputs (0-3.3V) and integrated center push-button. | Conventional / Precision mode | Reads vectorial displacement (X, Y) to move the cursor. |
| **Buttons with Cap (x2)** | Push buttons with internal pull-up configuration and mechanical response. | Conventional / Right-Left handed | **Button 1 (Right Click):** Opens contextual menus or secondary options.<br>**Button 2 (Left Click):** Selects, opens, and moves elements on screen. |
| **Rotary Encoder (KY-040)** | 360° incremental rotary encoder with output and integrated push button (SW). | Conventional / Precision / Multimedia | **Rotation:** Vertical page scrolling (Scroll UP/DOWN). |
| **RGB LED (KY-009)** | Tricolor visual emulator module (Common Cathode) operated via PWM signals. | Visual state output | Explicitly indicates device mode:<br>• **Blue:** BLE searching / waiting.<br>• **Red:** Connected (Precision mode).<br>• **Yellow:** USB connection / calibrating. |
| **Active Buzzer (KY-012)** | Buzzer with internal fixed-tone oscillator operated by high logic level (HIGH). | Sound output | Emits short confirmation tones (acoustic feedback) for clicks, mode changes by interruption, or Deep Sleep transitions. |

---

## 🔌 Connection Diagram & Pin Assignment

### Pin Assignment

| Component Pin | ESP32 GPIO Pin | Protocol / Signal Type |
| :--- | :---: | :--- |
| **Joystick (VRx)** | D32 | Analog Input (X axis) |
| **Joystick (VRy)** | D33 | Analog Input (Y axis) |
| **Joystick (+5V)** | 3V3 | Power (3.3V) |
| **Joystick (GND)** | GND | Ground |
| **Right Click Button** | D25 | Digital Input (`INPUT_PULLUP` to GND) |
| **Left Click Button** | D26 | Digital Input (`INPUT_PULLUP` to GND)|
| **Encoder (CLK)** | D18 | Digital Input (Clock / Channel A)|
| **Encoder (DT)** | D19 | Digital Input (Data / Channel B) |
| **Encoder (+)** | 3V3 | Power (3.3V)|
| **Encoder (GND)** | GND | Ground |
| **RGB LED (R)** | D13 | Digital Output (PWM) |
| **RGB LED (G)** | D14 | Digital Output (PWM) |
| **RGB LED (B)** | D27 | Digital Output (PWM) |
| **RGB LED (GND)** | GND | Ground |
| **Buzzer (Signal)** | D12 | Digital Output| 
| **Buzzer (+)** | 3V3 | Power (3.3V) |
| **Buzzer (GND)** | GND | Ground|

---

## 💻 Software & Source Code

### Development Environment
- **IDE:** Arduino IDE & Wokwi Simulator.
- **Key Libraries:** `BleMouse.h` (ESP32-BLE-Mouse), native Espressif BLE libraries (`BLEDevice.h`, `BLEUtils.h`, `BLEServer.h`), ADC module (`analogRead`, `analogSetAttenuation` to 11dB), and `HardwareSerial`.

*(The complete code file `air_mouse.ino` is located alongside this file in the repository).*

---

## 🎓 Theoretical Concepts of the Subject

1. **Open-Loop Control Systems:** The project frames within open-loop control systems, which do not use output information or adjust their behavior based on results. The ESP32 acquires joystick analog signals, computes vector increments, and transmits HID packets unidirectionally via Bluetooth without feedback from the graphical interface about the cursor's exact screen position.
2. **Disturbances:** Unwanted signals entering the system altering predictable behavior. They can be *endogenous* (internal thermal/electrical noise of components) or *exogenous* (external electromagnetic interference or mechanical potentiometer tolerances). The firmware incorporates reading and filtering techniques to counteract these signals.
3. **Measurement Elements and Sensors:** The joystick uses a potentiometer to convert mechanical lever displacement into a proportional analog voltage variation, which is sensed and converted to digital values by the ESP32's ADC.

---

## 🛠️ Development Log & Troubleshooting

### Assembly Steps
1. **Structure:** Joined two breadboards to gain physical space and placed the ESP32 in the center for clean wiring.
2. **Buttons:** Connected buttons to the protoboard, verified recognition, and programmed them for left/right click functions.
3. **Joystick:** Wired connections, verified X/Y axis tracking, and tested Bluetooth mouse communication via the library.
4. **Rotary Encoder:** Connected digital pins (CLK, DT, SW) and validated correct rotation reading for vertical scroll.
5. **RGB LED:** Connected color channels (R, G, B) to PWM outputs, testing visual feedback for system states (Blue for standby, Yellow for conventional, Red for precision).
6. **Active Buzzer:** Connected signal pin to digital output, validating acoustic confirmation for keypresses and events.

### Challenges Faced & Solutions
- **Space shortage on breadboard:** Solved by joining two breadboards and centering the ESP32.
- **Movement Delay:** Initial Bluetooth tests presented a delay and the cursor stuck to a fixed center. Resolved by implementing firmware joystick calibration (`calibrateJoystick()`), taking 100 ADC samples to redefine the real center (`centerX`, `centerY`), applying a deadzone (`DEADZONE = 500`), and a scaling factor (`SPEED = 0.010`) to eliminate resting noise and stabilize cursor movement.

---

## 🚀 Proposed Improvements for Stage 2

1. **Right/Left-Handed mode toggle via interrupt:** Inverts click button assignments, reusing the currently free encoder push button (`SW`) without adding stock components.
2. **"Precision" Mode (with LED color change):** Software-based modification that reduces cursor sensitivity for millimeter actions by altering the existing `SPEED` constant.
3. **Interrupt-driven Deep Sleep Mode:** Leverages joystick RTC pins (`ext0`/`ext1`) to enter low power mode after inactivity and wake up upon exceeding a movement threshold.

---

## 📝 Conclusion
Developing the Air Mouse successfully integrated hardware components with the ESP32, applying control systems and programming principles. Overcoming challenges regarding calibration and signal processing via software resulted in a functional, stable device with clear pathways for future expansions like Deep Sleep and precision modes.
