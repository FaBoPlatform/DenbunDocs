# SPIFFS

ESP32には、保存領域としてSPIFFSが使用できます。

## SPIFFSへArduino IDEからアクセスを可能にするPlugin

https://github.com/me-no-dev/arduino-esp32fs-plugin/releases

からESP32FS-v0.1.zipをダウンロードし、解凍します。

![](./img/spiffs001.png)

![](./img/spiffs002.png)

解凍してできたフォルダをArdiono-Contents-Java-tools 以下にコピーします。

![](./img/spiffs003.png)

![](./img/spiffs004.png)

Arduino　IDEを再起動し、ツールに、ESP32 Sketch Data Uploadが追加されていることを確認。

![](./img/spiffs005.png)

## DataのUpload

Arduno IDEから、スケッチ-スケッチフォルダを表示を選択します。

![](./img/spiffs006.png)

dataフォルダを作成します。

![](./img/spiffs007.png)

次にdataフォルダの中に、index.html を作成し、保存します。

```xml
<html>
<body>
Hello World!
</body>
</html>
```

ESP32 Sketch Data Uploadでindex.htmlをUploadします。

![](./img/spiffs005.png)

## APとWebServer

```c
#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>
#include "FS.h"
#include "SPIFFS.h"

const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
WebServer server(80);

void handleRoot() {
  File file = SPIFFS.open("/index.html", "r");
  String contents =file.readString();
  file.close();
  server.send(200, "text/html", contents);
}

void setup()
{
  Serial.begin(115200);
  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", handleRoot);
  server.begin();

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



