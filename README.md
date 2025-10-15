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

#include <ArduinoJson.h>
#include <ESP8266WiFi.h>
#include <WiFiClient.h>

// === CONFIGURATIE ===
char ssid[] = "JOUW_WIFI_NAAM";         // Vul hier de naam van je WiFi in
char pass[] = "JOUW_WIFI_WACHTWOORD";   // Vul hier je WiFi-wachtwoord in

// TODO: Hier komt straks je boeken-API in plaats van de weer-API
const char server[] = "openlibrary.org"; // voorbeeld, kan je aanpassen
String apiPath = "/isbn/VOORBEELD_ISBN.json"; // TODO: Vervang door eigen endpoint of ISBN

WiFiClient client;

#define JSON_BUFF_DIMENSION 8192
String text;

unsigned long lastConnectionTime = 0;
const unsigned long postInterval = 10000;  // elke 10 sec

void setup() {
  Serial.begin(9600);
  while (!Serial) { ; }

  text.reserve(JSON_BUFF_DIMENSION);

  Serial.println("📡 Verbinden met WiFi...");
  WiFi.begin(ssid, pass);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\n✅ WiFi verbonden!");

  // === Testbare API URL printen voor browser ===
  String testURL = "http://" + String(server) + apiPath;
  Serial.println("\n🔗 Test deze URL in je browser:");
  Serial.println(testURL);
}

void loop() {
  if (millis() - lastConnectionTime > postInterval) {
    lastConnectionTime = millis();
    makeHttpRequest();
  }
}

void makeHttpRequest() {
  client.stop();

  if (client.connect(server, 80)) {
    client.println("GET " + apiPath + " HTTP/1.1");
    client.println("Host: " + String(server));
    client.println("Connection: close");
    client.println();

    // Header overslaan
    bool headerSkipped = false;
    text = "";
    unsigned long timeout = millis();
    while (client.connected() && millis() - timeout < 10000) {
      String line = client.readStringUntil('\n');
      if (!headerSkipped && (line == "\r" || line == "")) {
        headerSkipped = true;
        text = client.readString();
        break;
      }
    }

    if (text.length() > 0) {
      parseJson(text.c_str());
    } else {
      Serial.println("⚠️ Fout: Geen JSON-data ontvangen!");
    }

    client.stop();
  } else {
    Serial.println("❌ Fout: Verbinding met server mislukt!");
  }
}

void parseJson(const char* jsonString) {
  DynamicJsonDocument doc(JSON_BUFF_DIMENSION);
  DeserializationError error = deserializeJson(doc, jsonString);
  if (error) {
    Serial.println("⚠️ Fout bij JSON: " + String(error.c_str()));
    return;
  }

  // === Hier pas je straks je JSON velden aan op basis van de boeken-API ===
  // Bijvoorbeeld: title, number_of_pages, author_name etc.
  String title = doc["title"] | "Onbekende titel";
  int numberOfPages = doc["number_of_pages"] | 0;

  Serial.println("📖 Boek Titel: " + title);
  Serial.println("📄 Aantal pagina's: " + String(numberOfPages));

  // Deze if-structuren waren oorspronkelijk voor weer → nu alleen qua tekst aangepast
  if (numberOfPages == 0) {
    Serial.println("⚠️ Geen boekgegevens gevonden.");
    // TODO: hier iets met je hardware doen (bijv. rode LED's)
  } else if (numberOfPages < 100) {
    Serial.println("📘 Dun boek — lekker snel te lezen!");
    // TODO: hardware feedback
  } else if (numberOfPages >= 100 && numberOfPages < 300) {
    Serial.println("📗 Gemiddeld boek — goed te doen.");
    // TODO: hardware feedback
  } else if (numberOfPages >= 300) {
    Serial.println("📕 Dik boek — dit wordt een uitdaging!");
    // TODO: hardware feedback
  } else {
    Serial.println("ℹ️ Andere boekinformatie ontvangen.");
    // TODO: hardware feedback
  }
}

```

