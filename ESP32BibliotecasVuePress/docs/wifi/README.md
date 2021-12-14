# WiFi

## Conexiones

### Modo Punto de Acceso

#### Importación Biblioteca
```c
#include <WiFi.h>
```
#### void setup()
```c
  Serial.begin(115200);
  
  WiFi.softAP("PuntoAcceso", "contrasena");
  Serial.println("soft Access Point: ");
  Serial.println(WiFi.softAPIP());
```
#### void loop()
```
void loop(){}
```

### Modo Estación

#### Importación Biblioteca
```c
#include <WiFi.h>
```
#### void setup()
```c
  Serial.begin(115200);
  Serial.println();
  
  // Pon aquí tu ssid y contraseña
  WiFi.begin("studiomiranda", "88888888"); 

  Serial.print("Conectando");
  while (WiFi.status() != WL_CONNECTED)
  {
    delay(500);
    Serial.print(".");
  }
  Serial.println();

  Serial.print("Conectado, dirección IP: ");
  Serial.println(WiFi.localIP());
```
#### void loop()
```
void loop(){}
```

### Servidor Web

#### Importación bibliotecas

```c
#include <WiFi.h>
#include <ESPAsyncWebServer.h>
```
#### Pines y variables
```
#define Led 2

char paginaweb[] PROGMEM = R"=====(

<!DOCTYPE html>
<html>
<body>

<center>

<h1>ESP32 HTML</h1>

<h3> LED </h3>

<button onclick="window.location = 'http://192.168.4.1/led/on'">On</button>
<button onclick="window.location = 'http://'+location.hostname+'/led/off'">Off</button>

</center>
</body>
</html>

)=====";
```
#### Instancia y función/es
```c
AsyncWebServer server(80); // server port 80

void notFound(AsyncWebServerRequest *request)
{
  request->send(404, "text/plain", "Página no encontrada");
}
```
#### void setup()
```
  Serial.begin(115200);
  pinMode(Led, OUTPUT);

  WiFi.softAP("PuntoAcceso");
  Serial.println("softAP: ");
  Serial.println(WiFi.softAPIP());
```
```c
  server.on("/", [](AsyncWebServerRequest * request)
  {  
  request->send_P(200, "text/html", paginaweb);
  });

   server.on("/led/on", HTTP_GET, [](AsyncWebServerRequest * request)
  { 
    digitalWrite(Led, HIGH);
  request->send_P(200, "text/html", paginaweb);
  });

  server.on("/led/off", HTTP_GET, [](AsyncWebServerRequest * request)
  { 
    digitalWrite(Led, LOW);
  request->send_P(200, "text/html", paginaweb);
  });

  server.onNotFound(notFound);

  server.begin();  // Finalmente inciamos el servidor
```
#### void loop()
```
void loop(){}
```

### Servidor Web SPIFFS

#### Importación bibliotecas

```c
#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include "SPIFFS.h"
```
#### Pin
```
#define Led 2
```
#### Instacia y función/es
```c
AsyncWebServer server(80);

void notFound(AsyncWebServerRequest *request)
{
  request->send(404, "text/plain", "Página no encontrada");
}
```

#### void setup()
```
  Serial.begin(115200);
  pinMode(Led, OUTPUT);
  WiFi.softAP("PuntoAcceso", "");
  Serial.println("softAP");
  Serial.println("");
  Serial.println(WiFi.softAPIP());
```
```c
  // Initialize SPIFFS
  if(!SPIFFS.begin(true)){
    Serial.println("A ocurrido un error al montando SPIFFS");
    return;
  }
  
  server.on("/", HTTP_GET, [](AsyncWebServerRequest * request){
    request->send(SPIFFS, "/index.html", "text/html");
  });

   server.on("/led/on", HTTP_GET, [](AsyncWebServerRequest * request)
  { 
    digitalWrite(Led, HIGH);
  request->send(SPIFFS, "/index.html", "text/html");
  });

  server.on("/led/off", HTTP_GET, [](AsyncWebServerRequest * request)
  { 
    digitalWrite(Led, LOW);
  request->send(SPIFFS, "/index.html", "text/html");
  });

  server.onNotFound(notFound);

  server.begin();  // Finalmente inciamos el servidor
```
#### void loop()
```
void loop(){}
```

### WebSocket

#### Importar bibliotecas
```c
#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include "SPIFFS.h"
#include <WebSocketsServer.h>
```
```
#define Led 2

AsyncWebServer server(80);
void notFound(AsyncWebServerRequest *request)
{
  request->send(404, "text/plain", "¡Página no encontrada!");
}
```
#### Instancia y Función/es (recibir datos)
```c 
WebSocketsServer websockets(81);

void webSocketEvent(uint8_t num, WStype_t type, uint8_t * payload, size_t length) {

  switch (type) 
  {
    case WStype_DISCONNECTED:
      Serial.printf("[%u] ¡Desconectado!\n", num);
      break;
    case WStype_CONNECTED: {
        IPAddress ip = websockets.remoteIP(num);
        Serial.printf("[%u] Conectado en %d.%d.%d.%d url: %s\n", num, ip[0], ip[1], ip[2], ip[3], payload);

        websockets.sendTXT(num, "Conectado en servidor:");
      }
      break;
    case WStype_TEXT:
      Serial.printf("[%u] Recibe el texto: %s\n", num, payload);
      String mensaje = String((char*)( payload));
      Serial.println(mensaje);

      if(mensaje == "Led esta en OFF"){
        digitalWrite(Led,LOW);
      }

      if(mensaje == "Led esta en ON"){
        digitalWrite(Led,HIGH);
      }
  }
}
```
#### Función (enviar datos)
```c
// Función envío datos por websocket en formato Json
void enviarDatosSensor() {
   String JSON_Data = "{\"temperatura\":";
          JSON_Data += temperatura;
          JSON_Data += ",\"humedad\":";
          JSON_Data += humedad;
          JSON_Data += ",\"termostato\":";
          JSON_Data += termostato;
          JSON_Data += ",\"rele\":";
          JSON_Data += rele;
          JSON_Data += "}";
   Serial.println(JSON_Data);
   websockets.broadcastTXT(JSON_Data);  // envia datos a todos los clientes conectados
}
```

#### void setup()
```  
  Serial.begin(115200);
  pinMode(Led, OUTPUT);
  
  WiFi.softAP("PuntoAcceso", "");
  Serial.println("softAP");
  Serial.println("");
  Serial.println(WiFi.softAPIP());

  if(!SPIFFS.begin(true)){
    Serial.println("A ocurrido un error al montar SPIFFS");
    return;
  }
  
  server.on("/", HTTP_GET, [](AsyncWebServerRequest * request)
  { 
   request->send(SPIFFS, "/index.html", "text/html");
  });

  server.onNotFound(notFound);

  server.begin();
```
```c
  websockets.begin();
  websockets.onEvent(webSocketEvent);
```
#### void loop()
```c
 websockets.loop();
 enviarDatosSensor();
```