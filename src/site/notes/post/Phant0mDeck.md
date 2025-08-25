---
{"dg-publish":true,"permalink":"/post/phant0m-deck/"}
---

# Phant0mDeck
![Pasted image 20250821214355.jpg](/img/user/attachments/Pasted%20image%2020250821214355.jpg)

Created: March 26, 2022 9:23 PM

```c
; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:nodemcu]
platform = espressif8266
board = nodemcu
framework = arduino
lib_deps = 
	chris--a/Keypad@^3.1.1
	knolleary/PubSubClient@^2.8
monitor_speed = 115200
```

```c
#include <Arduino.h>
#include <ESP8266WiFi.h>
#include <PubSubClient.h>

#include <Keypad.h>

#define RED D6
#define GREEN D7
#define BLUE D8

const byte ROWS = 3; //four rows
const byte COLS = 3; //four columns

char hexaKeys[ROWS][COLS] = {
  {'1','2','3'},
  {'4','5','6'},
  {'7','8','9'},};

byte rowPins[ROWS] = {D0, D1, D2}; //connect to the row pinouts of the keypad
byte colPins[COLS] = {D3, D4, D5}; //connect to the column pinouts of the keypad

Keypad customKeypad = Keypad( makeKeymap(hexaKeys), rowPins, colPins, ROWS, COLS);

const char* ssid = "TP-Link_F09A";
const char* password = "14742781";
const char* mqtt_server = "192.168.1.2";
String username = "mqtt";
String passwordmqtt = "lkjhgfdsa1";
String topic = "python/mqtt";
WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE	(50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  
}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(),"mqtt","lkjhgfdsa1")) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("python/mqtt", "hello world");
      // ... and resubscribe
      client.subscribe("python/mqtt1");
      digitalWrite(RED, LOW);
      digitalWrite(BLUE, LOW);
      digitalWrite(GREEN, HIGH);
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      digitalWrite(RED, LOW);
      digitalWrite(GREEN, LOW);
      digitalWrite(BLUE, HIGH);
      delay(5000);
    }
  }
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  
}

void keypadEvent(KeypadEvent key){
  switch (customKeypad.getState()){
    case PRESSED:
      switch (key){
        case '1':
         Serial.print("1");
         client.publish("python/mqtt", "1");
        break; 
        case '2': 
          Serial.print("2");
          client.publish("python/mqtt", "2");
        break;
        case '3':
          Serial.print("3");
          client.publish("python/mqtt", "3");
        break;
        case '4':
          Serial.print("4");
          client.publish("python/mqtt", "4");
        break;
        case '5':
          Serial.print("5");
          client.publish("python/mqtt", "5");
        break;
        case '6':
          Serial.print("6");
          client.publish("python/mqtt", "6");
        break;
        case '7':
          Serial.print("7");
          client.publish("python/mqtt", "7");
        break;
        case '8':
          Serial.print("8");
          client.publish("python/mqtt", "8");
        break;
        case '9':
          Serial.print("9");
          client.publish("python/mqtt", "9");
        break;
        }
    break;
    case RELEASED:
      switch (key){
        case '1':
        break; 
        case '2':      
        break;
        case '3':
        break;
        case '4':
        break;
        case '5':
        break;
        case '6':
        break;
        case '7':
        break;
        case '8':
        break;
        case '9':
        break;
        }
    break;
    case HOLD:
      switch (key){}
    break;
  }
}

void setup() {
  pinMode(RED, OUTPUT);
  pinMode(GREEN, OUTPUT);
  pinMode(BLUE, OUTPUT);

  digitalWrite(RED, HIGH);

  customKeypad.addEventListener(keypadEvent);
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  
}

void loop() {
char key = customKeypad.getKey();
if (!client.connected()) {
    reconnect();}

  client.loop();
}
}
```