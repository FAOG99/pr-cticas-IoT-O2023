# Práctica 2
---
## Indicaciones
1. Prender y apagar el led interno del ESP32, escribir la temperatura interna del ESP32 en el monitor serial
2. Usar el ESP32 como servidor web
3. Opciones para alimentar el ESP32 cuando esta conectado a wifi
4. Prender y apagar el led interno del ESP32 desde el servidor web
5. Prender y apagar un led externo desde el servidor web ESP32
---
## 1)
#### Código:
```
void setup()
{
pinMode(2, OUTPUT);
}
void loop()
{
digitalWrite(2,HIGH);
delay(2500);
digitalWrite(2,LOW);
delay(2500);
}
```
#### Evidencia:


https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/b4e6846c-1848-4cd4-8036-627e9bd54459

## 1.2)
#### Código:
```
#ifdef __cplusplus
extern "C" {
#endif
uint8_t temprature_sens_read();
#ifdef __cplusplus
}
#endif
uint8_t temprature_sens_read();
void setup()
{
pinMode(2, OUTPUT);
Serial.begin(9600);
}
void loop()
{
digitalWrite(2,HIGH);
Serial.println("Led prendido");
delay(2500);
digitalWrite(2,LOW);
Serial.println("Led apagado");
delay(2500);
Serial.println("la temperatura interna del ESP32 es: ");
Serial.println(((temprature_sens_read() - 32 ) / 1.8));
Serial.println("");
}
```
#### Evidencia:


https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/c55796a5-0100-4517-94e9-b632c16a656a

## 2)
#### Código:
```
#include <WiFi.h>
WiFiServer server(80);
WiFiClient client;
#ifdef __cplusplus
extern "C" {
#endif
uint8_t temprature_sens_read();
#ifdef __cplusplus
}
#endif
uint8_t temprature_sens_read();
void setup()
{
Serial.begin(9600);
WiFi.disconnect();
delay(3000);
Serial.println("Iniciando");
WiFi.begin("CASTOR-PLAY___2.4Gnormal","Cas123Tor2022");
while ((!(WiFi.status() == WL_CONNECTED))){
Serial.print("......");
delay(300);
}
Serial.println("Conexion establecida con el SSDI!");
Serial.println((WiFi.localIP()));
server.begin();
}
void loop()
{
client = server.available();
if (!client) { return; }
while(!client.available()){ delay(1); }
client.flush();
client.println("HTTP/1.1 200 OK");
client.println("Content-Type: text/html");
client.println("");
client.println("<!DOCTYPE HTML>");
client.println("<html>");
client.println("<head>");
client.println("<title> ""MiESP32"" </title>");
client.println("</head>");
client.println("<body>");
client.println("<h1 style=""color:#3366ff"">");
client.println("");
client.println("</h1>");
client.println("<span style=""color:#33cc00;font-size:14px"">");
client.println("Bienvenidos a mi pagina web");
client.println("</span>");
client.println("<br>");
client.println("<span style=""color:#cc6600;font-size:14px"">");
client.println("Esta pagina web ha estado activa por");
client.println("</span>");
client.println("<span style=""color:#009900;font-size:20px"">");
client.println((millis()/1000));
client.println("</span>");
client.println("<span style=""color:#cc6600;font-size:14px"">");
client.println("segundos");
client.println("</span>");
client.println("<br>");
client.println("<span style=""color:#ffcc33;font-size:14px"">");
client.println("La temperatura interna del ES32 es de:");
client.println("</span>");
client.println("<span style=""color:#000000;font-size:14px"">");
client.println(((temprature_sens_read() - 32 ) / 1.8));
client.println("</span>");
client.println("<span style=""color:#ffcc33;font-size:14px"">");
client.println("grados");
client.println("</span>");
client.println("</body>");
client.println("</html>");
delay(1);
}
```
#### Evidencia


https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/0ed811a6-245b-4459-b652-24efe7a7552f


## 3)
#### Evidencia:


https://github.com/FAOG99/practicas-IoT-O2023/assets/92898049/12945904-27dc-4ee4-9b39-f64f515724cb






