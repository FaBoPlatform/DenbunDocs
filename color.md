# 203 Color 

203をI2Cの端子につなぎます。

## ライブラリの取り込み

![](./img/color001.png)

![](./img/color002.png)

![](./img/color003.png)

サンプルコードを実行し、実際に色が取れている事を確認しましょう。

## ソースコード

index.html

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
#include "WiFi.h"
#include "ESPAsyncWebServer.h"
#include "FS.h"
#include "SPIFFS.h"
#include <FaBoColor_S11059.h>

FaBoColor faboColor;

uint16_t r,g,b,i;
const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
AsyncWebServer server(80);

String processor(const String& var)
{
  if(var == "COLOR") {
    String color = "#" + String(r&0xff,HEX) + String(g&0xff, HEX) + String(b&0xff, HEX);
    return color;
  }
  return String();
}

void setup()
{
  Serial.begin(115200);

  faboColor.begin();
  
  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html", "text/html");
  });  
  server.on("/color", HTTP_GET, [](AsyncWebServerRequest *request){
    Serial.println("Color");
    faboColor.readRGBI(&r, &g, &b, &i);
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

