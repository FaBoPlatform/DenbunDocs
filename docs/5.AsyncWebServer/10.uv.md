# 206 UV Index

206をI2Cの端子につなぎます。

## ソースコード

index.html

```xml
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
</head>
<body>
UV INDEX: %UVINDEX%<br>
IR: %IR%<br>
VISIBLE: %VISIBLE%<br>
</body>
</html>
```

Arduino

```c
#include "WiFi.h"
#include "ESPAsyncWebServer.h"
#include "FS.h"
#include "SPIFFS.h"
#include "FaBoUV_Si1132.h"

FaBoUV faboUV;

const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
AsyncWebServer server(80);

double uvindex = 0;
double ir = 0;
double visible = 0;

String processor(const String& var)
{
  if(var == "UVINDEX") {
    return String(uvindex, DEC);
  }
  if(var == "IR") {
    return String(ir, DEC);
  }
  if(var == "VISIBLE") {
    return String(visible, DEC);
  }
  return String();
}

void setup()
{
  Serial.begin(115200);

  while(!faboUV.begin()){
    Serial.println("Si1132 Not Found");
    delay(5000);
  }
  
  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
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
  uvindex = faboUV.readUV()/100;
  ir = faboUV.readIR();
  visible = faboUV.readVisible();
  
  Serial.print("UV:");
  Serial.println(uvindex);
  Serial.print("IR:");
  Serial.println(ir);
  Serial.print("Visible:");
  Serial.println(visible);
  Serial.println("");  

  delay(1000);
}
```

## 改造

```xml
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="Refresh" content="3">
<meta name="viewport" content="width=device-width,initial-scale=1">
</head>
<body>
UV INDEX: %UVINDEX%<br>
IR: %IR%<br>
VISIBLE: %VISIBLE%<br>
</body>
</html>
```

