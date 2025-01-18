#include <WiFi.h>
#include "VOneMqttClient.h"
#include <Wire.h>
#include <LiquidCrystal_PCF8574.h>
#include <cmath> // For fmax()

// LCD I2C address
LiquidCrystal_PCF8574 lcd(0x27);

// GPIO pins for LEDs and sound sensor
#define SOUND_SENSOR_PIN 36  // GPIO36 for sound sensor
#define RED_LED_PIN 13       // GPIO13 for red LED
#define YELLOW_LED_PIN 12    // GPIO12 for yellow LED
#define GREEN_LED_PIN 14     // GPIO14 for green LED

// Device IDs
const char* MAX4466Sensor = "ab57b00e-b538-4df8-9501-bb8e53b0b81d";
const char* LCDDevice = "d4d6f21e-89cb-4da4-b960-3ef58c34f3d4";
const char* RedLED = "ec6fe5d9-2a21-4ea9-853f-273d87fa6d27";
const char* YellowLED = "58abdab9-41b3-4050-9d7c-42786f6d121e";
const char* GreenLED = "bfff1db6-069b-40db-ac12-5c92ffbb92ba";

// MQTT Client
VOneMqttClient voneClient;

// Timing variables
unsigned long lastMsgTime = 0;
#define INTERVAL 1000 // 1 second

void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(WIFI_SSID);

  WiFi.mode(WIFI_STA);
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println();
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void triggerActuator_callback(const char* actuatorDeviceId, const char* actuatorCommand) {
  Serial.print("Received callback: ");
  Serial.print(actuatorDeviceId);
  Serial.print(" : ");
  Serial.println(actuatorCommand);

  String errorMsg = "";
  JSONVar commandObject = JSON.parse(actuatorCommand);

  if (JSON.typeof(commandObject) == "undefined") {
    errorMsg = "Invalid JSON command";
    voneClient.publishActuatorStatusEvent(actuatorDeviceId, actuatorCommand, errorMsg.c_str(), false);
    return;
  }

  if (String(actuatorDeviceId) == RedLED) {
    bool state = (bool)commandObject["Red Led"];
    digitalWrite(RED_LED_PIN, state ? HIGH : LOW);
    voneClient.publishActuatorStatusEvent(actuatorDeviceId, actuatorCommand, true);
  } else if (String(actuatorDeviceId) == YellowLED) {
    bool state = (bool)commandObject["Yellow Led"];
    digitalWrite(YELLOW_LED_PIN, state ? HIGH : LOW);
    voneClient.publishActuatorStatusEvent(actuatorDeviceId, actuatorCommand, true);
  } else if (String(actuatorDeviceId) == GreenLED) {
    bool state = (bool)commandObject["Green Led"];
    digitalWrite(GREEN_LED_PIN, state ? HIGH : LOW);
    voneClient.publishActuatorStatusEvent(actuatorDeviceId, actuatorCommand, true);
  }
}

void setup() {
  Serial.begin(9600);

  setup_wifi();
  voneClient.setup();
  voneClient.registerActuatorCallback(triggerActuator_callback);

  lcd.begin(16, 2); // Initialize LCD (16 columns, 2 rows)
  lcd.setBacklight(255); // Turn on backlight
  lcd.setCursor(0, 0);
  lcd.print("  Noise Monitor");

  pinMode(RED_LED_PIN, OUTPUT);
  pinMode(YELLOW_LED_PIN, OUTPUT);
  pinMode(GREEN_LED_PIN, OUTPUT);
}

void loop() {
  if (!voneClient.connected()) {
    voneClient.reconnect();
  }
  voneClient.loop();

  unsigned long cur = millis();
  if (cur - lastMsgTime > INTERVAL) {
    lastMsgTime = cur;

    // Read sound sensor value
    int soundLevel = analogRead(SOUND_SENSOR_PIN);
    float voltage = (soundLevel / 4095.0f) * 3.3f;
    float decibels = voltage > 0.001f ? 20 * log10(voltage / 0.00019f) : 0.0f;
    decibels = fmax(decibels, 0.0f);

    // Determine noise level
    String noiseLevel;
    bool redLedState = false, yellowLedState = false, greenLedState = false;

    if (decibels <= 50) {
      redLedState = false;
      yellowLedState = false;
      greenLedState = true;
      noiseLevel = "Low";
    } else if (decibels <= 70) {
      redLedState = false;
      yellowLedState = true;
      greenLedState = false;
      noiseLevel = "Moderate";
    } else {
      redLedState = true;
      yellowLedState = false;
      greenLedState = false;
      noiseLevel = "High";
    }

    // Set LED states
    digitalWrite(RED_LED_PIN, redLedState ? HIGH : LOW);
    digitalWrite(YELLOW_LED_PIN, yellowLedState ? HIGH : LOW);
    digitalWrite(GREEN_LED_PIN, greenLedState ? HIGH : LOW);

    // Publish LED statuses
    String redPayload = "{\"Red Led\":" + String(redLedState ? "true" : "false") + "}";
    voneClient.publishActuatorStatusEvent(RedLED, redPayload.c_str(), true);

    String yellowPayload = "{\"Yellow Led\":" + String(yellowLedState ? "true" : "false") + "}";
    voneClient.publishActuatorStatusEvent(YellowLED, yellowPayload.c_str(), true);

    String greenPayload = "{\"Green Led\":" + String(greenLedState ? "true" : "false") + "}";
    voneClient.publishActuatorStatusEvent(GreenLED, greenPayload.c_str(), true);

    // Update LCD
    lcd.setCursor(0, 0);
    lcd.print(" Sound: ");
    lcd.print(decibels, 2);
    lcd.print("dB       ");
    lcd.setCursor(0, 1);
    lcd.print(noiseLevel);
    lcd.print("         ");

    // Publish telemetry for MAX4466 sensor
    JSONVar sensorPayload;
    sensorPayload["Sound"] = decibels;
    sensorPayload["Noise Level"] = noiseLevel;
    voneClient.publishTelemetryData(MAX4466Sensor, sensorPayload);

    // Publish telemetry for LCD
    JSONVar lcdPayload;
    lcdPayload["First Row"] = String("Sound: ") + String(decibels, 2) + "dB";
    lcdPayload["Second Row"] = noiseLevel;
    voneClient.publishTelemetryData(LCDDevice, lcdPayload);

    // Log to Serial
    Serial.printf("Sound Level: %.2f dB, Noise Level: %s\n", decibels, noiseLevel.c_str());
  }
}
