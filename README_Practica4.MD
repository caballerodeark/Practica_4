# Práctica 4: WIFI y BLUETOOTH

## Práctica A generación de una página web

Script:

```cpp
/*
 ESP32 Web Server - STA Mode
 modified on 25 MAy 2019
 by Mohammadreza Akbari @ Electropeak
 https://electropeak.com/learn
*/

#include <WiFi.h>
#include <WebServer.h>

// SSID & Password
const char* ssid = "*****";  // Enter your SSID here
const char* password = "*****";  //Enter your Password here

WebServer server(80);  // Object of WebServer(HTTP port, 80 is defult)

void setup()
{
    Serial.begin(115200);
    Serial.println("Try Connecting to ");
    Serial.println(ssid);

    // Connect to your wi-fi modem
    WiFi.begin(ssid, password);

    // Check wi-fi is connected to wi-fi network
    while (WiFi.status() != WL_CONNECTED)
    {
        delay(1000);
        Serial.print(".");
    }
    Serial.println("");
    Serial.println("WiFi connected successfully");
    Serial.print("Got IP: ");
    Serial.println(WiFi.localIP());  //Show ESP32 IP on serial

    server.on("/", handle_root);

    server.begin();
    Serial.println("HTTP server started");
    delay(100); 
}

void loop() 
{
    server.handleClient();
}

// HTML & CSS contents which display on web server
String HTML = "<!DOCTYPE html>\
<html>\
<body>\
<h1>My Primera Pagina con ESP32 - Station Mode &#128522;</h1>\
</body>\
</html>";

// Handle root url (/)
void handle_root() 
{
    server.send(200, "text/html", HTML);
}
```

Explicación: Inicializa la conexión a un puerto WiFi declarando su respectivo nombre y contraseña (En este caso sin especificar). Si hay exito en la conexión, muestra la IP. Despues inicializa un pequeño servidor para luego en el loop crear un acceso a una página web en la cual envia con el subprograma handle_root() envia un texto en lenguaje HTML el definido en el String HTML anterior en la cual podremos acceder desde nuestro movil usando la misma conexion wifi y la http adecuada, en el caso de no obtener conexion se queda imprimiendo "." en el puerto serie indefinidamente.

## Práctica B comunicación Bluetooth con el móvil

Script:

```cpp
//This example code is in the Public Domain (or CC0 licensed, at your option.)
//By Evandro Copercini - 2018
//
//This example creates a bridge between Serial and Classical Bluetooth (SPP)
//and also demonstrate that SerialBT have the same functionalities of a normal Serial

#include "BluetoothSerial.h"

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth is not enabled! Please run `make menuconfig` to and enable it
#endif

BluetoothSerial SerialBT;

void setup() 
{
    Serial.begin(115200);
    SerialBT.begin("ESP32test"); //Bluetooth device name
    Serial.println("The device started, now you can pair it with bluetooth!");
}

void loop() 
{
    if(Serial.available())
    {
        SerialBT.write(Serial.read());
    }
    if(SerialBT.available())
    {
        Serial.write(SerialBT.read());
    }
    delay(20);
}
```

Explicación: Crea una relacion entre el puerto Serie y la conexion por Bluetooth con otro dispositivo, primero pone nombre a la conexion Bluetooth que inicializa desde la ESP32 ("ESP32test") y luego declara en el terminal Serie que lo ha inicializado. En el loop(), por franjas de 20 microsegundos, va controlando si van habiendo intercambios de datos Serie-Bluetooth entre la ESP32 y el dispositivo enlazado.
