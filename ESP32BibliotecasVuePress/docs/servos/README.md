# SERVOS

## Biblioteca servo.h

### Servo

#### Importar biblioteca, pin, instancia y variable
```c
#include <Servo.h>

#define SERVOPIN 13

Servo mi_servo;  // Instancia, 12 objetos como máximo

int posicion = 0;    // Posición inicial
```

#### void setup()
```c
  mi_servo.attach(SERVOPIN));
```
#### void loop() 
```c
  for (posicion = 0; posicion <= 180; posicion += 1) { // De 0 a 180 grados, un paso = un grado
    mi_servo.write(posicion);
    delay(15);
  }
  for (posicion = 180; posicion >= 0; posicion -= 1) { //  De  180 a 0 grados
    myservo.write(posicion); 
    delay(15);                       
  }
```
