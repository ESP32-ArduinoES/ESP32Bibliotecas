---
sidebar: auto
---

# SENSORES

## Temperatura

### Biblioteca DHT 11 / 22 / 21

#### Importaciones, pines, instancias y variables

```c
#include "DHT.h" // Importación de la biblioteca
#define DHTTYPE DHT22 // Tipo de sensor DHT11 - DHT21
#define DHTPIN = 27; // Pin de señal del sensor (DOIT ESP32 DEVKIT V1)
DHT dht(DHTPIN, DHTTYPE); // Instancia de la biblioteca
float temperatura;
float humedad;
```
#### Función/es

```c
void leerSensoresDHT(){
  
   // La lectura del sensor tarda 250 milisegundos
  temperatura = dht.readTemperature(); 
  humedad = dht.readHumidity();

   if (isnan(humedad) || isnan(temperatura)) {
      Serial.println("¡Error de lectura en el sensor DHT!");
      return;
   }
```
#### void setup()
```c
   dht.begin();
```
#### void loop()
```c 
   leerSensoresDHT();
   Serial.print("Humedad: ");
   Serial.print(humedad);
   Serial.println(" %\t");
   Serial.print("Temperatura: ");
   Serial.print(temperatura);
   Serial.println(" *C ");
```
