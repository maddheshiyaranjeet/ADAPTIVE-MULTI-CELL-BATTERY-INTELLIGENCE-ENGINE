# ADAPTIVE-MULTI-CELL-BATTERY-INTELLIGENCE-ENGINE
An intelligent battery management system (BMS) designed to monitor, analyze, and optimize the performance of multi-cell battery packs in real time. This project combines embedded systems, sensor data acquisition, and adaptive algorithms to improve battery safety, efficiency, and lifespan.

# 🔋 Adaptive Multi-Cell Battery Intelligence Engine 

**ESP32-Based Multi-Cell Voltage Monitoring & Diagnostics System**

An intelligent Battery Management System (BMS) prototype built using the ESP32 microcontroller. This project continuously monitors the voltage of four simulated battery cells, calculates pack statistics, detects cell imbalance, and provides real-time battery health diagnostics through the Serial Monitor.

---

## 📖 Overview  

The **Adaptive Multi-Cell Battery Intelligence Engine** demonstrates the core functionality of a modern Battery Management System (BMS). Instead of using real battery cells, four potentiometers simulate individual cell voltages, allowing safe testing and development of battery monitoring algorithms.

The system reads analog voltages from each simulated cell, computes pack voltage, identifies the strongest and weakest cells, measures cell imbalance, and classifies the overall battery health.

---

## ✨ Features

- 🔋 Real-time monitoring of four battery cells
- 📊 Pack voltage and average cell voltage calculation
- ⚡ Automatic weakest and strongest cell detection
- 📈 Cell imbalance percentage calculation
- 🛡️ Battery health diagnosis
  - Healthy
  - Minor Imbalance
  - Critical Imbalance
  - Pack Failure
- 📟 Live diagnostic reports through Serial Monitor
- 🚀 Built using ESP32 and Arduino Framework

---

## 🛠 Hardware Requirements

- ESP32 Development Board
- 4 × Potentiometers
- Breadboard
- Jumper Wires
- USB Cable

---

## 🔌 Circuit Connections

| Simulated Cell | ESP32 GPIO |
|----------------|------------|
| Cell 1 | GPIO 32 |
| Cell 2 | GPIO 33 |
| Cell 3 | GPIO 34 |
| Cell 4 | GPIO 35 |

---

## ⚙️ Working Principle

1. ESP32 reads analog values from four potentiometers.
2. ADC values are converted into simulated Li-ion cell voltages.
3. The system calculates:
   - Pack Voltage
   - Average Cell Voltage
   - Weakest Cell
   - Strongest Cell
   - Cell Imbalance
4. Battery health is evaluated.
5. Results are displayed every second on the Serial Monitor.

---

# 💻 Source Code

```cpp
/*
 Adaptive Multi-Cell Battery Intelligence Engine
 ESP32 + 4 Potentiometers
*/

const int cellPins[4] = {32, 33, 34, 35};

float cellVoltage[4];

float packVoltage;
float averageVoltage;

int weakestCell;
int strongestCell;

float weakestVoltage;
float strongestVoltage;

float imbalance;

String batteryHealth;

const float MAX_CELL_VOLTAGE = 4.2;

void readCells()
{
  for (int i = 0; i < 4; i++)
  {
    int adc = analogRead(cellPins[i]);

    cellVoltage[i] = ((float)adc / 4095.0) * MAX_CELL_VOLTAGE;
  }
}

void calculatePack()
{
  packVoltage = 0;

  weakestVoltage = cellVoltage[0];
  strongestVoltage = cellVoltage[0];

  weakestCell = 0;
  strongestCell = 0;

  for (int i = 0; i < 4; i++)
  {
    packVoltage += cellVoltage[i];

    if (cellVoltage[i] < weakestVoltage)
    {
      weakestVoltage = cellVoltage[i];
      weakestCell = i;
    }

    if (cellVoltage[i] > strongestVoltage)
    {
      strongestVoltage = cellVoltage[i];
      strongestCell = i;
    }
  }

  averageVoltage = packVoltage / 4.0;

  imbalance =
      ((strongestVoltage - weakestVoltage) / averageVoltage) * 100.0;
}

void batteryDiagnosis()
{
  if (weakestVoltage < 2.8)
  {
    batteryHealth = "PACK FAILURE";
  }
  else if (imbalance >= 10)
  {
    batteryHealth = "CRITICAL IMBALANCE";
  }
  else if (imbalance >= 5)
  {
    batteryHealth = "MINOR IMBALANCE";
  }
  else
  {
    batteryHealth = "HEALTHY";
  }
}

void displayReport()
{
  Serial.println("--------------------------------------");

  for (int i = 0; i < 4; i++)
  {
    Serial.print("Cell ");
    Serial.print(i + 1);
    Serial.print(": ");
    Serial.print(cellVoltage[i], 2);
    Serial.println(" V");
  }

  Serial.println();

  Serial.print("Pack Voltage : ");
  Serial.print(packVoltage, 2);
  Serial.println(" V");

  Serial.print("Average Cell : ");
  Serial.print(averageVoltage, 2);
  Serial.println(" V");

  Serial.print("Weakest Cell : ");
  Serial.print(weakestCell + 1);
  Serial.print(" (");
  Serial.print(weakestVoltage, 2);
  Serial.println(" V)");

  Serial.print("Strongest Cell : ");
  Serial.print(strongestCell + 1);
  Serial.print(" (");
  Serial.print(strongestVoltage, 2);
  Serial.println(" V)");

  Serial.print("Imbalance : ");
  Serial.print(imbalance, 2);
  Serial.println(" %");

  Serial.print("Battery Health : ");
  Serial.println(batteryHealth);

  Serial.println("--------------------------------------");
}

void setup()
{
  Serial.begin(115200);

  analogReadResolution(12);

  for (int i = 0; i < 4; i++)
  {
    pinMode(cellPins[i], INPUT);
  }
}

void loop()
{
  readCells();

  calculatePack();

  batteryDiagnosis();

  displayReport();

  delay(1000);
}
```

---

## 📊 Battery Health Logic

| Condition | Status |
|-----------|--------|
| Weakest Cell < 2.8V | ❌ PACK FAILURE |
| Imbalance ≥ 10% | ⚠️ CRITICAL IMBALANCE |
| Imbalance 5–10% | ⚠️ MINOR IMBALANCE |
| Imbalance < 5% | ✅ HEALTHY |

---

## 📟 Sample Output

```text
--------------------------------------
Cell 1: 4.02 V
Cell 2: 3.98 V
Cell 3: 4.10 V
Cell 4: 3.95 V

Pack Voltage : 16.05 V
Average Cell : 4.01 V

Weakest Cell : 4 (3.95 V)
Strongest Cell : 3 (4.10 V)

Imbalance : 3.74 %

Battery Health : HEALTHY
--------------------------------------
```

---

## 🚀 Applications

- Electric Vehicle Battery Management Systems
- Solar Energy Storage
- Portable Power Banks
- UPS Monitoring
- Robotics
- Embedded Systems Education

---

## 🔮 Future Scope

- Real battery pack integration
- Active cell balancing
- OLED/TFT display
- Wi-Fi & Bluetooth monitoring
- Cloud logging
- Mobile App dashboard
- State of Charge (SoC)
- State of Health (SoH)

---

## 📄 License

This project is licensed under the MIT License.

---

## ⭐ Support

If you found this project useful, please consider giving it a ⭐ on GitHub.
