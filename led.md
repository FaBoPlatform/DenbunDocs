# LEDの点灯/消灯

## LEDの点灯/消灯

```c
void setup() {
  pinMode(4, OUTPUT);
}

void loop() {
  digitalWrite(4, HIGH);
  delay(1000);
  digitalWrite(4, LOW);
  delay(1000);
}
```

## LEDをWebから操作

```c
#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>

const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
WebServer server(80);

void handleRoot() {
  server.send(200, "text/html", "LED Control");
}

void handleOn(){
  digitalWrite(4, HIGH);
  Serial.println("LED ON");
  server.send(200, "text/html", "LED On");
}

void handleOff(){
  digitalWrite(4, LOW);
  Serial.println("LED OFF");
  server.send(200, "text/html", "LED Off");
}

void setup()
{
  Serial.begin(115200);
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", handleRoot);
  server.on("/on", handleOn);
  server.on("/off", handleOff);
  server.begin();

  pinMode(4, OUTPUT);
  
  Serial.println();
  Serial.print("AccessPoint:");
  Serial.println(ssid);
  Serial.print("IP:");
  Serial.println(serverIP);
}

void loop() {
  server.handleClient();
}
```

|項目|値|
|:--|:--|
|http://192.168.0.1/on|LED on|
|http://192.168.0.1/off|LED off|

