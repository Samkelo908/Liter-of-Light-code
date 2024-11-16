#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

// Define pins for external LEDs and the light sensor
const int ledPins[] = {D1, D2, D3, D4};  // Array for LED pins
const int lightSensorPin = A0;  // Pin for the light sensor (analog input)

const char* ssid = "Logic Legends";
const char* password = "123456789";

ESP8266WebServer server(80);

bool ledsState = false;   // Combined state for both LEDs
int lightThreshold = 30;  // Adjust the threshold value based on your sensor and environment
bool isDaylight = false;  // Flag to indicate if it's daylight or not

// HTML content moved to PROGMEM to save RAM
const char htmlContent[] PROGMEM = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
  <title>LED Control</title>
  <style>
    body { 
      font-family: 'Arial', sans-serif; 
      background-color: #f7f7f7; 
      margin: 0; 
      display: flex; 
      flex-direction: column; 
      justify-content: center; 
      align-items: center; 
      height: 100vh;
      color: #333;
    }
    h1 { 
      font-size: 36px; 
      color: #444; 
      margin-bottom: 20px; 
    }
    button {
      font-size: 18px;
      padding: 12px 24px;
      margin: 15px;
      border-radius: 8px;
      border: none;
      background-color: #4CAF50;
      color: white;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      transition: all 0.3s ease-in-out;
    }
    button:hover {
      background-color: #45a049;
      box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
      transform: translateY(-2px);
    }
    #dotButton {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      background-color: #ff9800;
      border: none;
      transition: all 0.3s ease;
      cursor: pointer;
    }
    .on { background-color: #4CAF50; }
    .off { background-color: #f44336; }
    #dotButton:hover {
      transform: scale(1.1);
    }
  </style>
</head>
<body>
  <h1>LED Control</h1>
  <button id="toggleButton">Toggle LEDs</button>
  <button id="dotButton" class="off"></button>
  <script>
    let ledsState = false;
    document.getElementById('toggleButton').addEventListener('click', function() {
      ledsState = !ledsState;
      var xhr = new XMLHttpRequest();
      xhr.open("GET", "/toggle", true);
      xhr.send();
      let dotButton = document.getElementById('dotButton');
      if (ledsState) {
        dotButton.classList.remove('off');
        dotButton.classList.add('on');
      } else {
        dotButton.classList.remove('on');
        dotButton.classList.add('off');
      }
    });
  </script>
</body>
</html>
)rawliteral";

void setup() {
  for (int i = 0; i < 4; ++i) {
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW); // LEDs off initially (assuming active low)
  }

  WiFi.softAP(ssid, password);
  Serial.begin(115200);
  Serial.println("Access Point started");
  Serial.print("IP Address: ");
  Serial.println(WiFi.softAPIP());

  server.on("/", HTTP_GET, handleRoot);
  server.on("/toggle", HTTP_GET, handleToggleLEDs);
  server.begin();
}

void loop() {
  daylightRoutine();
  delay(1000);
  server.handleClient();
}

void daylightRoutine() {
  int raw_light = analogRead(lightSensorPin);
  bool prevLight = isDaylight;
  isDaylight = raw_light > lightThreshold;
  if (!isDaylight && prevLight) {
    updateLed(true);
  } else if (isDaylight && !prevLight) {
    updateLed(false);
  }
}

void updateLed(bool state) {
  ledsState = state;
  for (int i = 0; i < 4; ++i) {
    digitalWrite(ledPins[i], ledsState ? HIGH : LOW);
  }
}

void handleRoot() {
  server.send_P(200, "text/html", htmlContent);
}

void handleToggleLEDs() {
  ledsState = !ledsState;
  updateLed(ledsState);
  server.send(200, "text/plain", "LEDs toggled");
}



LED Control with ESP8266 🌟💡
This project demonstrates how to control multiple LEDs using an ESP8266 microcontroller, with a light sensor to automatically toggle the LEDs based on daylight. It also includes a web-based interface to manually control the LEDs via a simple HTML page.

Features 🌈
Control LEDs remotely via a web interface
Automatic LED control based on light sensor readings (daylight vs night)
Web interface with modern button styling, including hover effects and transitions
Access Point Mode: The ESP8266 creates its own Wi-Fi network for easy device control
Hardware Required 🔧
ESP8266 Wi-Fi Module
4 LEDs (connected to pins D1, D2, D3, and D4)
1 Light Sensor (connected to pin A0)
How It Works 💡
LED Control:

You can toggle the LEDs on and off by clicking the "Toggle LEDs" button in the web interface.
There's a dot button that changes color based on the LED state. When the LEDs are on, the dot is green, and when off, it’s red.
Light Sensor:

The light sensor (analog input) reads the ambient light level. When the light level goes below a certain threshold (indicating it's getting dark), the system turns the LEDs on automatically. Conversely, when the light level is high (daylight), the LEDs are turned off.
Web Interface:

The ESP8266 acts as a Wi-Fi access point, allowing you to connect to it directly with your phone or computer.
Once connected, navigate to the IP address of the ESP8266 to access the LED control page.
Code Explanation 📜
Wi-Fi Setup:

The ESP8266 is set up in Access Point Mode, which creates its own Wi-Fi network (Logic Legends).
The password to join this network is 123456789.
HTML/CSS Interface:

The HTML content is served from the ESP8266, which includes buttons to toggle LEDs and display the current LED state using a dot button.
The CSS styles the page with modern buttons, hover effects, and a smooth layout for a great user experience.
Light Sensor Logic:

The light sensor is read every second, and based on the light level, the LEDs are automatically toggled on or off.
A threshold value (set to 30 in this code) determines if it's "daylight" or not.
Code Structure 🏗️
Setup:

Initializes the LEDs and the Wi-Fi network.
Starts the web server to serve the HTML page and handle requests.
Web Server:

The handleRoot function serves the main HTML page when you access the ESP8266’s IP address.
The handleToggleLEDs function listens for requests to toggle the LEDs and updates the LED state.
Daylight Routine:

The daylightRoutine function checks the light sensor value. If the light is below the threshold, the LEDs are turned on automatically; if it’s above, the LEDs are turned off.
How to Use 🚀
Connect to the ESP8266's Wi-Fi network:

SSID: Logic Legends
Password: 123456789
Open a web browser and navigate to the IP address of the ESP8266 (displayed in the Serial Monitor after startup).

Control the LEDs:

Click on the Toggle LEDs button to turn the LEDs on and off.
The dot button will show the current state of the LEDs (green = on, red = off).
Code Walkthrough 🖥️
Wi-Fi Configuration:

cpp
Copy code
WiFi.softAP(ssid, password);
Creates a Wi-Fi access point with the specified SSID and password.
Web Server Setup:

cpp
Copy code
server.on("/", HTTP_GET, handleRoot);
server.on("/toggle", HTTP_GET, handleToggleLEDs);
Routes incoming HTTP GET requests to the appropriate functions. / serves the main HTML page, and /toggle toggles the LED state.
Daylight Detection:

cpp
Copy code
int raw_light = analogRead(lightSensorPin);
isDaylight = raw_light > lightThreshold;
Reads the light sensor value and compares it with a threshold to decide whether it's daylight or night.
LED Control:

cpp
Copy code
digitalWrite(ledPins[i], ledsState ? HIGH : LOW);
Updates the state of each LED based on the ledsState (on or off).
Conclusion 🌟
This project is a fun and easy way to get started with IoT and the ESP8266. It combines web development, sensor readings, and hardware control in a simple yet powerful way. 🌐💡
