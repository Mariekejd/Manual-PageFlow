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
