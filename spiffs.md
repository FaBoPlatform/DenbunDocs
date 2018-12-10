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

## ESPAsyncWebServerの組み込み

https://github.com/me-no-dev/ESPAsyncWebServer

よりESPAsyncWebServerをダウンロードしてきます。

![](./img/spiffs008.png)

![](./img/spiffs009.png)

Arduino IDEの[スケッチ]-[ライブラリのインクルード]-[ZIP形式のライブラリをインクルード]で、ダウンロードしてきたzipを指定します。

![](./img/spiffs010.png)

![](./img/spiffs011.png)

## AsyncTCPの組み込み

https://github.com/me-no-dev/AsyncTCP/tree/idf-update

よりAsyncTCPのidf-updateブランチから、AyncTCPをダウンロードしてきます。

![](./img/spiffs012.png)

![](./img/spiffs013.png)

Arduino IDEの[スケッチ]-[ライブラリのインクルード]-[ZIP形式のライブラリをインクルード]で、ダウンロードしてきたzipを指定します。

![](./img/spiffs010.png)

![](./img/spiffs014.png)

## APとWebServer

```c
#include <WiFi.h>
#include "ESPAsyncWebServer.h"
#include "FS.h"
#include "SPIFFS.h"

const char ssid[] = "ESP32AP-AKIRA";
const char pass[] = "11111111";
const IPAddress ip(192,168,0,1);
const IPAddress subnet(255,255,255,0);
AsyncWebServer server(80);

void setup()
{
  Serial.begin(115200);
  SPIFFS.begin();
  WiFi.softAP(ssid,pass);
  delay(100);
  WiFi.softAPConfig(ip,ip,subnet);
  IPAddress serverIP = WiFi.softAPIP();
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(SPIFFS, "/index.html", "text/html");
  }); 
  server.begin();

  Serial.println();
  Serial.print("AccessPoint:");
  Serial.println(ssid);
  Serial.print("IP:");
  Serial.println(serverIP);
}

void loop() {}
```



