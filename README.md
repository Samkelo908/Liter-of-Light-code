



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
