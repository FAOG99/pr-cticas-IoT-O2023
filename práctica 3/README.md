

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


# 3.4
Leer y escribir al monitor serial información del sensor RFID (NXP RC522)

![image](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/16582b01-0030-44ea-a15a-93f49f1bd231)

#### Código:
```

#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 22 //Pin 22 para el reset del RC522
#define SS_PIN 21 //Pin 21 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); //Iniciamos la comunicación serial
SPI.begin(); //Iniciamos el Bus SPI
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Lectura del UID");
}
void loop() {
delay(500);
// Revisamos si hay nuevas tarjetas presentes
// Look for new cards
if ( mfrc522.PICC_IsNewCardPresent()) 
{ 
delay(50);
//Seleccionamos una tarjeta
if ( mfrc522.PICC_ReadCardSerial()) 
{
Serial.println("Present");
// Enviamos serialemente su UID
Serial.println("Card UID:");
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX); 
} 
Serial.println();
// Terminamos la lectura de la tarjeta actual
mfrc522.PICC_HaltA(); 
} 
}
```
#### Evidencia:



https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/aa186965-3ac0-4fd6-8b6a-95a20c9c21c6


# 3.5
Leer y escribir al monitor serial información del sensor RFID (NXP RC522) – control de accesos con leds

#### Código:
```
#include <SPI.h>
#include <MFRC522.h>

#define RST_PIN 22 //Pin 22 para el reset del RC522
#define SS_PIN 21 //Pin 21 para el SS (SDA) del RC522
#define greenLed 12
#define redLed 14

MFRC522 mfrc522(SS_PIN, RST_PIN); ///Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); //Iniciamos La comunicacion serial
SPI.begin(); //Iniciamos el Bus SPI
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Control de acceso:");
pinMode(greenLed, OUTPUT); 
pinMode(redLed, OUTPUT); 
}
byte ActualUID[4]; //almacenará el código del Tag leído
byte Usuario1[4]= {0x86, 0x78, 0x76, 0x33} ; //código del usuario 1 (CRED ITESO)
byte Usuario2[4]= {0xC1, 0x2F, 0xD6, 0x0E} ; //código del usuario 2
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent()) 
{ 
//Seleccionamos una tarjeta
if ( mfrc522.PICC_ReadCardSerial()) 
{
// Enviamos serialemente su UID
Serial.print(F("Card UID:"));
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX); 
ActualUID[i]=mfrc522.uid.uidByte[i]; 
} 
Serial.print(" "); 
//comparamos los UID para determinar si es uno de nuestros usuarios 
if(compareArray(ActualUID,Usuario1)) {
Serial.println("Acceso concedido...");
digitalWrite(greenLed, HIGH);
delay(1500);
digitalWrite(greenLed, LOW);
}
else if(compareArray(ActualUID,Usuario2)) {
Serial.println("Acceso concedido...");
digitalWrite(greenLed, HIGH);
delay(1500);
digitalWrite(greenLed, LOW);
}
else {
Serial.println("Acceso denegado...");
digitalWrite(redLed, HIGH);
delay(1500);
digitalWrite(redLed, LOW);
}
// Terminamos la lectura de la tarjeta tarjeta actual
mfrc522.PICC_HaltA();
}
} 
}
//Función para comparar dos vectores
boolean compareArray(byte array1[],byte array2[])
{
if(array1[0] != array2[0])return(false);
if(array1[1] != array2[1])return(false);
if(array1[2] != array2[2])return(false);
if(array1[3] != array2[3])return(false);
return(true);
}
```

#### Evidencia:




https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/1a064b86-7239-4a94-84e1-137229fa4b3c



