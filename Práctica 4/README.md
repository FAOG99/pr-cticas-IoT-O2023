# Práctica 4
---
## 4.1

1. Durante la demostración, ¿qué distancia pudimos alcanzar con la comunicación LoRa entre el ESP32 
y el ReYax RYLR998? ¿Por qué es relevante este largo alcance en aplicaciones de IoT?

Durante la demostracion el dispositivo estaba a mas o menos 350 metros del receptor, aunque pueden llegar a alcanzar distancias mucho mayores.
Esto es relevante ya que se puede utilizar esta tecnología para sistemas que necesiten comunicación inalámbrica para cubrir grandes distancias con 
muy poca infraestructura.

3. En términos de consumo de energía, ¿cómo se compara LoRa con otras tecnologías de comunicación 
inalámbrica como Wi-Fi o Bluetooth?

LoRa es una tecnología de muy bajo consumo, permitiendose operar con una bateria por periodos muy largos de tiempo

5. ¿Cuándo sería apropiado utilizar LoRa en un proyecto de IoT en lugar de otras tecnologías de 
comunicación?

Cuando la distancia que necesitas cubrir es bastante o cuando no tienes acceso a una fuente de alimentación constante, o tal vez cuando necesitas comunicación 
entre edificios o paredes.

7. ¿Porque se usó este número: 915000000 como Banda de frecuencia (Hz) en esta práctica?

Porque esta es la frecuencia que se usa en América, si estuvieramos en Europa entonces seria la frecuencia en 868 MHz

9. Desde tu punto de vista individual crees que esta práctica, aunque no se pidió que la hicieran uds los 
estudiantes y solo la hizo la profesora ayudó a entender mas los conceptos teóricos? Si o no y 
porque

Creo que si ayudó bastante, yo en lo personal ya había hecho investigación de LoRa por lo que verlo en acción si me aclaró muchas cosas.


## 4.2
#### Subscribers:

/v1.6/devices/PlantaBaja/+
---
![image](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/39a4314a-e4c3-43b4-be42-6268ec64f4fb)


/v1.6/devices/PlantaAlta/+
---
![image](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/9b221f09-df9d-4ed1-8d2e-69c7b67cddbb)


Tristemente los demas no funcionaron

![image](https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/d1fd7e00-efdb-4370-a11f-17e7079bfcd1)

#### Preguntas:

1. ¿Cuáles son los componentes principales en una comunicación MQTT?
   
   R: Un cliente, un broker, un device y una conexión
   
3. Durante la práctica, ¿qué temas (topics) se utilizaron para enviar y recibir mensajes MQTT?
   
   R: /v1.6/devices/PlantaAlta/Temperatura
   
   /v1.6/devices/PlantaAlta/Humedad
   
   /v1.6/devices/PlantaAlta/Luminosidad
   
   /v1.6/devices/PlantaBaja/Temperatura
   
   /v1.6/devices/PlantaBaja/Humedad
   
   /v1.6/devices/PlantaBaja/Calidad_del_Aire
   
5. En la práctica, ¿hubo algún problema que tuvieron que solucionar al enviar y recibir 
mensajes MQTT? ¿Cómo lo resolvieron?

Si, hubo problemas con el Token ya que estaba utilizando el de cada device, y no el de la cuenta, ya aprendí que hay tokens generales e individuales

6. Esta práctica fue manual y el objetivo fue ayudar a entender la comunicación con MQTT, 
¿crees que esta práctica ayuda a la comprensión teórica? Si o no y por qué.. 

Si, mientras hacía la práctica estuvo trabajando mi cabeza viendo de que formas se pueden implementar en diversas situaciones, creo que eso ayuda bastante


## 4.3 

#### Código:

```
/**************************************** 
* Include Libraries 
****************************************/ 
#include <WiFi.h> 
#include <PubSubClient.h> 
/**************************************** 
* Define Constants 
****************************************/ 
#define WIFISSID "RED" //WIFI SSID aqui
#define PASSWORD "Contraseña" // WIFI pwd
#define TOKEN "BBFF-pTM3VEQ4k7V6uRSgB8cKMHiRfVpBNg" // Ubidots TOKEN ID el mismo que usamos en clase lo sacan de Ubidtos
#define MQTT_CLIENT_NAME "123456AbCde" //ID del cliente, 8 a 12 chars alfanumericos (ASCII), debe ser random y unico dif a otros devices 
#define VARIABLE_LABEL_temp "temperatura" // Variable Temperatura 
#define VARIABLE_LABEL_hum "humedad" // Variable Humedad 
#define DEVICE_LABEL "MQTT-Publish-esp32" // Nombre del dispositivo a crear 
char mqttBroker[]= "industrial.api.ubidots.com"; 
char payload[200]; // Leer y entender el payload aqui una de tantas referencias https://techterms.com/definition/payload
char topic[150]; //Espacio para el nombre del topico 
// Space to store values to send 
char str_temp[10]; 
char str_hum[10]; 
#include "DHT.h" 
#define pin1 15 
DHT dht1(pin1, DHT11); //El sensor de temp y humedad azul. 
/**************************************** 
* Funciones auxiliares 
****************************************/ 
WiFiClient ubidots; 
PubSubClient client(ubidots); 
void callback(char* topic, byte* payload, unsigned int length) { 
 char p[length + 1]; 
 memcpy(p, payload, length); 
 p[length] = NULL; 
 String message(p); 
 Serial.write(payload, length); 
 Serial.println(topic); 
} 
void reconnect() { 
 // Loop until we're reconnected
 while (!client.connected()) { 
 Serial.println("Attempting MQTT connection..."); 
 
 // Attemp to connect 
 if (client.connect(MQTT_CLIENT_NAME, TOKEN, "")) { 
 Serial.println("Connected"); 
 } else { 
 Serial.print("Failed, rc="); 
 Serial.print(client.state()); 
 Serial.println(" try again in 2 seconds"); 
 // Wait 2 seconds before retrying 
 delay(2000); 
 } 
 } 
} 
/**************************************** 
* Main Functions 
****************************************/ 
void setup() { 
 Serial.begin(115200); 
 WiFi.begin(WIFISSID, PASSWORD); 
 
 Serial.println(); 
 Serial.print("Wait for WiFi..."); 
 
 while (WiFi.status() != WL_CONNECTED) { 
 Serial.print("."); 
 delay(500); 
 } 
 
 Serial.println(""); 
 Serial.println("WiFi Connected"); 
 Serial.println("IP address: "); 
 Serial.println(WiFi.localIP()); 
 client.setServer(mqttBroker, 1883); 
 client.setCallback(callback); 
 // Sensor de temp y humedad 
 dht1.begin(); 
} 
void loop() { 
 if (!client.connected()) { 
 reconnect(); 
 } 
 //Leer los datos del sensor
 Serial.println(); 
 Serial.println("A continuación los datos de los sensores:");
 Serial.println();
 // Publica en el topic de temperatura 
 sprintf(topic, "%s%s", "/v1.6/devices/", DEVICE_LABEL); 
 sprintf(payload, "%s", ""); // Cleans the payload 
 sprintf(payload, "{\"%s\":", VARIABLE_LABEL_temp); // Adds the variable label 
 float t1 = dht1.readTemperature(); 
 Serial.print(" 1. La temperatura detectada en el sensor es de: ");
 Serial.println(t1); // Imprime temperatura en el serial monitor 
 // numero maximo 4 precision 2 y convierte el valor a string
 dtostrf(t1, 4, 2, str_temp); 
 
 sprintf(payload, "%s {\"value\": %s", payload, str_temp); // formatea el mensaje a publicar 
 sprintf(payload, "%s } }", payload); // cierra el mensaje 
 Serial.println(" Enviando la Temp al virtualdevice en ubidots via MQTT...."); 
 client.publish(topic, payload);
 // Agregar una linea para separar temp de humedad
 Serial.println();
 // Publica en el topic de humedad 
 sprintf(topic, "%s%s", "/v1.6/devices/", DEVICE_LABEL); 
 sprintf(payload, "%s", ""); // Cleans the payload 
 sprintf(payload, "{\"%s\":", VARIABLE_LABEL_hum); // Adds the variable label 
 float h1 = dht1.readHumidity(); 
 Serial.print(" 2. La humedad detectada por el sensor es de: ");
 Serial.println(h1); // Imprime la humedad en el monitor serial 
 // numero maximo 4 precision 2 y convierte el valor a string
 dtostrf(h1, 4, 2, str_hum); 
 sprintf(payload, "%s {\"value\": %s", payload, str_hum); // formatea el mensaje a publicar 
 sprintf(payload, "%s } }", payload); // cierra el mensaje 
 Serial.println(" Enviando Humedad al virtual device en ubidots via MQTT...."); 
 client.publish(topic, payload); 
 
 client.loop();
 
 //Esperare 10 seg a leer de nuevo los sensores
 Serial.println();
 Serial.println("Esperaré 12 seg para leer nuevamente los sensores.....");
 Serial.println();
 Serial.println(".......................................................");
 delay(12000); // 12 segundos entre publicaciones en ubidots 
}

```

#### Evidencia:



Uploading 20231013_232913_1.mp4…



