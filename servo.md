# Servoの制御

FaBo DenbunでServoを制御します。

## Servoの制御

HTML
```xml
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
</head>
<body>
<br>
<center>
  <input type="range" min="200" max="500" step="1" value="%VALUE0%" style="width:80%;" onchange="location.href='/?value='+this.value">
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
uint16_t value = 300;

String processor(const String& var)
{
  if(var == "VALUE0") {
    return String(value, DEC);
  }
  return String();
}


void setup() {
  Serial.begin(115200);
  if(faboPWM.begin()) {
    Serial.println("Find PCA9685");
    faboPWM.init(300);
  }
  faboPWM.set_hz(50);

  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    if(request->hasParam("value")) {
      AsyncWebParameter* p = request->getParam("value");
      String valueStr = p->value().c_str();
      value = valueStr.toInt();
      faboPWM.set_channel_value(0, value);
    }
    request->send(SPIFFS, "/index.html", String(), false, processor);
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
