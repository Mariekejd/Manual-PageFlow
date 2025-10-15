# 📚 PageFlow Manual

This manual explains how to build a PageFlow prototype using a NodeMCU, an LED strip, WiFi, and the Open Library API.
The goal is to visualize reading progress with LED lights.
This prototype focuses on the WiFi + API + LED feedback part of the concept.

## 🧠 Concept

The original idea involves giving reading feedback through a smartphone screen.
In this prototype, the feedback is simulated through LED colors and the serial monitor.

The NodeMCU connects to WiFi.
- It requests book data from the Open Library API (or a different API with book data).
- It reads values such as title and number of pages.
- It shows simple feedback using LEDs.

## 🧰 Materials

- NodeMCU (ESP8266)
- LED strip
- WiFi connection
- Open Library API → https://openlibrary.org/developers/api

## 💡 LED Strip Setup
1. Connect Din (yellow) to D1 on the NodeMCU.
2. Connect +5V (red) to a 3V pin on the NodeMCU.
3. Connect G (black) to G next to the 3V pin.
 
## 🌐 Step 1: New Sketch
Open Arduino and click on file > new sketch, it should look like this:

<img width="328" height="246" alt="Scherm­afbeelding 2025-10-12 om 19 54 58" src="https://github.com/user-attachments/assets/3a421249-6ed4-4ed4-bd0e-a7b3b1aceaf7" />

After making a new sketch go to the library and install Adafruit Neopixel. This is needed for our LEDS to be included in our code. Be sure to add the right one just like in the picture below.

<img width="278" height="624" alt="Scherm­afbeelding 2025-10-12 om 19 55 41" src="https://github.com/user-attachments/assets/5c6f66d9-9a07-49eb-af93-802c3b7df02f" />

This step sets up the NodeMCU to connect to a WiFi network.

```cpp

#include <ESP8266WiFi.h>

const char* ssid = "YOUR_WIFI";
const char* password = "YOUR_PASSWORD";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\n✅ Connected!");
}

void loop() {}

```

