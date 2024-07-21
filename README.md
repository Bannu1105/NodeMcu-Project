Here is the README content for your NodeMCU Car project:

---

# NodeMCU Car

This project allows you to control a car using a NodeMCU ESP8266 board and an L298N motor driver via a web interface. The NodeMCU creates a WiFi access point that you can connect to and control the car's movements through simple HTTP commands.

## Components Required

- NodeMCU ESP8266 board
- L298N motor driver
- Motors and wheels
- Power supply
- Jumper wires
- Breadboard (optional)

## Pin Configuration

- ENA: GPIO14 (D5) - Enable/speed control for right motors
- ENB: GPIO12 (D6) - Enable/speed control for left motors
- IN1: GPIO15 (D8) - L298N in1 for right motors
- IN2: GPIO13 (D7) - L298N in2 for right motors
- IN3: GPIO2 (D4) - L298N in3 for left motors
- IN4: GPIO0 (D3) - L298N in4 for left motors

## Software Requirements

- Arduino IDE
- ESP8266 board support for Arduino IDE
- ESP8266WiFi library
- ESP8266WebServer library

## Installation

1. **Install the Arduino IDE**: Download and install the Arduino IDE from the [official website](https://www.arduino.cc/en/software).

2. **Install ESP8266 Board Support**:
    - Open the Arduino IDE.
    - Go to `File` > `Preferences`.
    - In the "Additional Boards Manager URLs" field, add the following URL: `http://arduino.esp8266.com/stable/package_esp8266com_index.json`
    - Go to `Tools` > `Board` > `Boards Manager`.
    - Search for `esp8266` and install the latest version.

3. **Install Required Libraries**:
    - Go to `Sketch` > `Include Library` > `Manage Libraries`.
    - Search for `ESP8266WiFi` and install the library.
    - Search for `ESP8266WebServer` and install the library.

4. **Download and Upload Code**:
    - Copy the code from `NodeMCU_Car.ino` (provided below) into a new sketch in the Arduino IDE.
    - Save the sketch as `NodeMCU_Car.ino`.
    - Connect your NodeMCU to your computer.
    - Select the correct board and port from the `Tools` menu.
    - Click the upload button to upload the code to your NodeMCU.

```cpp
#define ENA   14          // Enable/speed motors Right        GPIO14(D5)
#define ENB   12          // Enable/speed motors Left         GPIO12(D6)
#define IN_1  15          // L298N in1 motors Right           GPIO15(D8)
#define IN_2  13          // L298N in2 motors Right           GPIO13(D7)
#define IN_3  2           // L298N in3 motors Left            GPIO2(D4)
#define IN_4  0           // L298N in4 motors Left            GPIO0(D3)

#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

String command;             // String to store app command state.
int speedCar = 800;         // 400 - 1023.
int speed_Coeff = 3;

const char* ssid = "NodeMCU_Car";
const char* password = "Nag@2004";
const char* adminUsername = "admin";     // Changed username to avoid conflict with C++ keyword.
const char* adminPassword = "Nag@2004";
ESP8266WebServer server(80);

void setup() {
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);  
  pinMode(IN_1, OUTPUT);
  pinMode(IN_2, OUTPUT);
  pinMode(IN_3, OUTPUT);
  pinMode(IN_4, OUTPUT);  
  
  Serial.begin(115200);
  
  // Connecting to WiFi
  WiFi.mode(WIFI_AP);
  WiFi.softAP(ssid, password);

  IPAddress myIP = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(myIP);
 
  // Starting Web Server 
  server.on("/", HTTP_handleRoot);
  server.onNotFound(HTTP_handleRoot);
  server.begin();    
}

void goAhead() { 
  digitalWrite(IN_1, LOW);
  digitalWrite(IN_2, HIGH);
  analogWrite(ENA, speedCar);

  digitalWrite(IN_3, LOW);
  digitalWrite(IN_4, HIGH);
  analogWrite(ENB, speedCar);
}

void goBack() { 
  digitalWrite(IN_1, HIGH);
  digitalWrite(IN_2, LOW);
  analogWrite(ENA, speedCar);

  digitalWrite(IN_3, HIGH);
  digitalWrite(IN_4, LOW);
  analogWrite(ENB, speedCar);
}

void goLeft() { 
  digitalWrite(IN_1, LOW);
  digitalWrite(IN_2, HIGH);
  analogWrite(ENA, speedCar);

  digitalWrite(IN_3, HIGH);
  digitalWrite(IN_4, LOW);
  analogWrite(ENB, speedCar);
}

void goRight() { 
  digitalWrite(IN_1, HIGH);
  digitalWrite(IN_2, LOW);
  analogWrite(ENA, speedCar);

  digitalWrite(IN_3, LOW);
  digitalWrite(IN_4, HIGH);
  analogWrite(ENB, speedCar);
}

void stopRobot() {  
  digitalWrite(IN_1, LOW);
  digitalWrite(IN_2, LOW);
  analogWrite(ENA, 0);

  digitalWrite(IN_3, LOW);
  digitalWrite(IN_4, LOW);
  analogWrite(ENB, 0);
}

void loop() {
  server.handleClient();
  
  command = server.arg("State");
  if (command == "F") goAhead();
  else if (command == "B") goBack();
  else if (command == "L") goLeft();
  else if (command == "R") goRight();
  else if (command == "S") stopRobot();
}

void HTTP_handleRoot() {
  if (!server.authenticate(adminUsername, adminPassword)) {
    return server.requestAuthentication();
  }
  server.send(200, "text/html", "<html><body><h1>NodeMCU Car Control</h1></body></html>");
  delay(1);
}
```

## How to Use

1. **Power the NodeMCU**: Connect the NodeMCU to a power source.
2. **Connect to WiFi**: On your computer or smartphone, connect to the WiFi network `NodeMCU_Car` with the password `Nag@2004`.
3. **Access the Control Interface**: Open a web browser and go to the IP address displayed in the Serial Monitor (e.g., `192.168.4.1`).
4. **Authenticate**: Enter the username `admin` and password `Nag@2004` to access the control interface.
5. **Control the Car**: Use the web interface to send commands to the car:
    - `F`: Move forward
    - `B`: Move backward
    - `L`: Turn left
    - `R`: Turn right
    - `S`: Stop

## Troubleshooting

- **Connection Issues**: Ensure the NodeMCU is powered properly and the correct SSID and password are used.
- **Movement Issues**: Verify the wiring between the NodeMCU, motor driver, and motors. Ensure the motors are connected to the correct pins.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.

---

This README provides a comprehensive guide for setting up, running, and troubleshooting your NodeMCU Car project.
