---
sidebar: auto
---

# Motores

## Motores paso a paso

Biblioteca [stepper](https://randomnerdtutorials.com/stepper-motor-esp32-websocket/)

## Biblioteca stepper

### 28BYJ-48 Motor + ULN2003 Driver

```c
#include <Stepper.h>

#define IN1 19 // Pines driver
#define IN2 18
#define IN3 5
#define IN4 17

#define PASOS 2048 // Pasos - revoluciones
#define NUMPASOS 100
int velocidad = 5;
Stepper myStepper(PASOS, IN1, IN3, IN2, IN4);
```
#### void setup()
```c
myStepper.setSpeed(velocidad);
```
#### void loop()
```c
  // Movemos el motor un número determinado de pasos
  stepper.step(NUMPASOS);
  delay(2000);
```

