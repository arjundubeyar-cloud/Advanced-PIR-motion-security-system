# Smart PIR Motion Security System with LCD and LDR

A smart embedded security project built using **Arduino Uno**, **PIR motion sensor**, **LDR sensor**, **16x2 LCD**, **red LED**, **green LED**, **buzzer**, and a **reset button**.  
The system detects motion, checks light conditions, and provides real-time feedback on the LCD and Serial Monitor.

---

## Project Objective

The objective of this project is to design a smart motion security system that can:
- Detect human movement using a PIR sensor.
- Monitor ambient light using an LDR sensor.
- Trigger an alarm using a buzzer and red LED.
- Show system status on a 16x2 LCD display.
- Use automation logic with IF/ELSE conditions.

This project is suitable for embedded systems and IoT beginner-to-advanced learning.

---

## Features

- PIR-based motion detection.
- LDR-based light monitoring.
- Green LED for normal/armed state.
- Red LED for intrusion alert.
- Buzzer alarm output.
- 16x2 LCD status display.
- Serial Monitor feedback.
- Manual reset button for alarm deactivation.
- Tinkercad simulation friendly.

---

## Components Required

- Arduino Uno
- PIR motion sensor
- LDR sensor
- 16x2 LCD display
- 10k potentiometer
- Red LED
- Green LED
- Buzzer
- Push button
- 220Ω resistors
- 10k resistor for LDR voltage divider
- Jumper wires
- Breadboard

---

## Circuit Connections

### LCD Connections
- VSS → GND
- VDD → 5V
- VO → Middle pin of 10k potentiometer
- RS → D12
- RW → GND
- E → D11
- D4 → D5
- D5 → D4
- D6 → D3
- D7 → D2
- A → 5V through 220Ω resistor
- K → GND

### PIR Sensor
- VCC → 5V
- GND → GND
- OUT → A0

### LDR Sensor
- One side of LDR → 5V
- Other side of LDR → A1
- 10k resistor from A1 → GND

### LEDs
- Red LED anode → D13 through 220Ω resistor
- Red LED cathode → GND
- Green LED anode → D8 through 220Ω resistor
- Green LED cathode → GND

### Buzzer
- Positive pin → D9
- Negative pin → GND

### Reset Button
- One side → D7
- Other side → GND
- Use `INPUT_PULLUP` in the code

---

## Working Principle

1. The green LED turns ON when the system is armed and no motion is detected.
2. The PIR sensor continuously checks for motion.
3. If motion is detected, the Arduino triggers the red LED and buzzer.
4. The LCD displays messages like:
   - System Armed
   - Motion Detected
   - ALARM TRIGGERED
   - Alarm Reset
5. The LDR reads light intensity and helps indicate whether the environment is bright or dark.
6. The reset button stops the alarm and returns the system to armed mode.

---

## Arduino Code

```cpp
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

const int pirPin = A0;
const int ldrPin = A1;
const int buzzerPin = 9;
const int redLedPin = 13;
const int greenLedPin = 8;
const int resetPin = 7;

bool alarmTriggered = false;
bool lastMotionState = LOW;

int lightThreshold = 450;

void setup() {
  pinMode(pirPin, INPUT);
  pinMode(ldrPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(redLedPin, OUTPUT);
  pinMode(greenLedPin, OUTPUT);
  pinMode(resetPin, INPUT_PULLUP);

  Serial.begin(9600);
  lcd.begin(16, 2);

  digitalWrite(buzzerPin, LOW);
  digitalWrite(redLedPin, LOW);
  digitalWrite(greenLedPin, HIGH);

  lcd.clear();
  lcd.print("System Booting");
  lcd.setCursor(0, 1);
  lcd.print("Please Wait");
  Serial.println("System Booting");
  delay(2000);

  lcd.clear();
  lcd.print("System Armed");
  lcd.setCursor(0, 1);
  lcd.print("No Motion");
  Serial.println("System Armed");
}

void loop() {
  int motionState = digitalRead(pirPin);
  int lightValue = analogRead(ldrPin);
  int resetState = digitalRead(resetPin);

  bool isDark = (lightValue < lightThreshold);

  if (alarmTriggered == false) {
    digitalWrite(greenLedPin, HIGH);
    digitalWrite(redLedPin, LOW);
    digitalWrite(buzzerPin, LOW);
  }

  if (motionState == HIGH && alarmTriggered == false) {
    alarmTriggered = true;

    digitalWrite(greenLedPin, LOW);
    digitalWrite(redLedPin, HIGH);
    digitalWrite(buzzerPin, HIGH);

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Motion Detected");
    lcd.setCursor(0, 1);
    if (isDark) {
      lcd.print("Night Alert");
    } else {
      lcd.print("Day Alert");
    }

    Serial.print("Motion Detected | Light: ");
    Serial.print(lightValue);
    Serial.print(" | ");
    Serial.println(isDark ? "Night" : "Day");
    delay(300);
  }

  if (alarmTriggered == true) {
    if (resetState == LOW) {
      alarmTriggered = false;

      digitalWrite(buzzerPin, LOW);
      digitalWrite(redLedPin, LOW);
      digitalWrite(greenLedPin, HIGH);

      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Alarm Reset");
      lcd.setCursor(0, 1);
      lcd.print("System Armed");

      Serial.println("Alarm Reset");
      Serial.println("System Armed");
      delay(500);
    }
  } else {
    lcd.setCursor(0, 0);
    lcd.print("System Armed   ");
    lcd.setCursor(0, 1);
    if (isDark) {
      lcd.print("Dark Mode      ");
    } else {
      lcd.print("Bright Mode    ");
    }
  }

  lastMotionState = motionState;

  Serial.print("PIR: ");
  Serial.print(motionState);
  Serial.print(" LDR: ");
  Serial.println(lightValue);

  delay(200);
}
```

---

## Expected Output

When the project runs successfully, the system will display:
- **System Booting**
- **System Armed**
- **Motion Detected**
- **ALARM TRIGGERED**
- **Alarm Reset**
- **Dark Mode / Bright Mode**

---



## GitHub Description

**Advanced Arduino-based PIR motion security system with LCD display, LDR sensor, red/green LEDs, buzzer alarm, and reset button. Simulated in Tinkercad.**

---

## Project Summary

This project demonstrates a smart embedded security system that uses sensor input, automation logic, and visual/audio feedback. It is a practical example of real-world embedded systems and basic IoT-style monitoring.
