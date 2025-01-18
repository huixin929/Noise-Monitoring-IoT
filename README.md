# IoT-Based Noise Level Monitoring System

This project implements an **IoT-based Noise Level Monitoring System** using the ESP32 microcontroller, MAX4466 sound sensor, and the V-One cloud platform. The system monitors real-time noise levels and provides feedback through LEDs, an LCD display, and a cloud-based dashboard for data visualization and trend analysis.

---

## **Hardware Setup**
The hardware setup includes the following components:
- **ESP32 NodeMCU**: Microcontroller.
- **MAX4466 Sound Sensor**: Measures ambient noise levels.
- **16x2 LCD Display (I2C interface)**: Displays real-time noise data.
- **LEDs (Red, Yellow, Green)**: Indicate noise levels.
- **Breadboard and Jumper Wires**: Circuit connections.

### Circuit Diagram
![Hardware Setup](Diagram/Sound%20Level%20Detection%20IoT%20Hardware%20Setup%20Diagram.png)

---

## **Installation and Setup**
### **Hardware Setup**
1. Assemble the hardware components as shown in the circuit diagram.
2. Connect the MAX4466 sound sensor to the ESP32.
3. Connect the LEDs and LCD to the ESP32.

### **Software Setup**
1. Install the required libraries in Arduino IDE:
   - `WiFi.h`
   - `VOneMqttClient.h`
   - `Wire.h`
   - `LiquidCrystal_PCF8574.h`
   - `ArduinoJson.h`
2. Update the firmware with your Wi-Fi credentials and upload it to the ESP32.
3. Register your devices on the V-One cloud platform and configure MQTT topics.

---

## **Usage**
1. Power on the device to start monitoring.
2. Observe real-time noise levels on the LCD display and LEDs.
3. Use the V-One dashboard to monitor and analyze noise trends.
4. Receive email and push notifications for noise threshold breaches.

---

## **Features**
- **Real-Time Noise Detection**:
  - Measures noise levels in decibels (dB) using the MAX4466 sound sensor.
  - Categorizes noise as:
    - **Low** (≤ 50 dB): Green LED ON.
    - **Moderate** (51-70 dB): Yellow LED ON.
    - **High** (> 70 dB): Red LED ON.
- **User-Friendly Visual Feedback**:
  - LCD displays current noise level and category.
  - LEDs provide instant visual feedback.
- **Cloud Integration**:
  - Stores noise data for long-term analysis.
  - Visualizes trends on the V-One dashboard.
  - Sends email and push notifications for noise threshold breaches.

---

## **Project Workflow**
Below is the workflow used for noise level data preprocessing, training, validation, and visualization:

![Classifier Workflow](Diagram/Noise%20Level%20Monitoring%20Classifier%20Workflow.png)

---

## **Dashboard**
The V-One dashboard provides an intuitive visualization of noise levels:

![Dashboard](Diagram/Noise%20Level%20Monitoring%20in%20Study%20Area%20Dashboard.jpg)

---

## **Notification System**
The system notifies users through email and push notifications when noise levels exceed predefined thresholds:

![Notification Workflow](Diagram/Noise%20Level%20Monitoring%20Notification.png)

---

## **Future Enhancements**
- Integration with additional sensors (e.g., temperature, humidity).
- Mobile application for real-time control and notifications.
- Advanced analytics for predictive noise control.

---

## **License**
This project is licensed under the **Apache License 2.0**. You may use this project under the terms specified in the [LICENSE](LICENSE) file.

---

## **Contact**
For questions or support, feel free to contact at [huixins929@gmail.com].
