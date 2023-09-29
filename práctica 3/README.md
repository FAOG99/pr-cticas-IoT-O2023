

# Práctica 3
---
## 3.1
Usar la fuente de poder para alimentar el ESP32

El + de la fuente de poder a la línea roja del 
protoboard.

El – de la fuente de poder a la línea azul
Con eso tendremos voltaje en toda la línea 
roja y azul desde la fuente de poder.



https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/a5cc97e0-e0be-4af6-934d-415e8ce05bda

## 3.2
Leer y escribir al monitor serial información del sensor de 
temperatura y humedad (DHT)
![Captura de pantalla 2023-09-29 111743](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/916f45c8-2a89-4b74-92a6-d5f37afbd983)

#### Código:
```
include "DHT.h"
DHT dht15(15,DHT11);
void setup()
{
Serial.begin(9600);
delay(2000); //agregar al código de easycoding.tn
dht15.begin(); //agregar al código de easycoding.tn
}
void loop()
{
Serial.println("Temperatura en el sensor");
Serial.println((dht15.readTemperature( )));
Serial.println("Humedad en el sensor");
Serial.println((dht15.readHumidity()));
delay(5000);
}

```
#### Evidencia:




https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/9cb0d528-3c58-46b3-b715-01db51830ca7


## 3.3
Leer y escribir al monitor serial información del sensor ultrasónico

Vcc a voltaje (+) del esp32 
2. Trigger al pin D5
3. Echo al pin D18
4. Gnd (tierra) a GND tierra del esp32

![image](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/7a6f75ec-7858-4da5-8121-3e788a04d636)


#### Código:
```
//Mi medidor de “xxxxxxxxxxxxxxxx”
const int trigPin = 5;
const int echoPin = 18;
//define sound speed in cm/uS
#define SOUND_SPEED 0.034
#define CM_TO_INCH 0.393701
long duration;
float distanceCm;
float distanceInch;
void setup() {
Serial.begin(115200); // Starts the serial communication
pinMode(trigPin, OUTPUT); // Sets the trigPin as an Output
pinMode(echoPin, INPUT); // Sets the echoPin as an Input
}
void loop() {
// Clears the trigPin
digitalWrite(trigPin, LOW);
delayMicroseconds(2);
// Sets the trigPin on HIGH state for 10 micro seconds
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);
// Reads the echoPin, returns the sound wave travel time in microseconds
duration = pulseIn(echoPin, HIGH);
// Calculate the distance
distanceCm = duration * SOUND_SPEED/2;
// Convert to inches
distanceInch = distanceCm * CM_TO_INCH;
// Prints the distance in the Serial Monitor
Serial.print("Distance (cm): ");
Serial.println(distanceCm);
Serial.print("Distance (inch): ");
Serial.println(distanceInch);
delay(1000);
}

```

#### Evidencia:



https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/314e9b59-8a5a-47e6-b494-95f7fc92b714

