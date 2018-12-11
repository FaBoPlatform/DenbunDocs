# RobotCarの制御

FaBo DenbunをつかってRobotCarを制御します。


## ソースコード

HTML

```xml
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="Refresh" content="3">
<meta name="viewport" content="width=device-width,initial-scale=1">
</head>
<body>
<center>
<input type="button" value="↑" onClick="location.href='/forward'"><br>
<input type="button" value="←" onClick="location.href='/right'">
<input type="button" value="□" onClick="location.href='/stop'">
<input type="button" value="→" onClick="location.href='/left'"><br>
<input type="button" value="↓" onClick="location.href='/back'"><br>
</center>
</body>
</html>
```

Arduino

```c

#include "ESPAsyncWebServer.h"
#include "FS.h"
#include "SPIFFS.h"
#include "FaBoPWM_PCA9685.h"

FaBoPWM faboPWM;

const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
AsyncWebServer server(80);

int STOP = 0;
int FORWARD = 400;
int BACK = 250;

void setup() {
  Serial.begin(115200);
  if(faboPWM.begin()) {
    Serial.println("Find PCA9685");
    faboPWM.init(300);
  }
  faboPWM.set_hz(50);
  
  faboPWM.set_channel_value(0, STOP); 
  faboPWM.set_channel_value(1, STOP); 
    
  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
  });
  server.on("/stop", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
    faboPWM.set_channel_value(0, STOP); 
    faboPWM.set_channel_value(1, STOP); 
  });
  server.on("/forward", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
    faboPWM.set_channel_value(0, FORWARD); 
    faboPWM.set_channel_value(1, BACK); 
  });
  server.on("/back", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
    faboPWM.set_channel_value(0, BACK); 
    faboPWM.set_channel_value(1, FORWARD); 
  });
  server.on("/left", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
    faboPWM.set_channel_value(0, FORWARD); 
    faboPWM.set_channel_value(1, FORWARD); 
  }); 
  server.on("/right", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html","text/html");
    faboPWM.set_channel_value(0, BACK); 
    faboPWM.set_channel_value(1, BACK); 
  });  
  server.begin();

  Serial.println();
  Serial.print("AccessPoint:");
  Serial.println(ssid);
  Serial.print("IP:");
  Serial.println(serverIP);
}

void loop() {
}
```

