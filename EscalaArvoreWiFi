/*
  WiFiAccessPoint.ino creates a WiFi access point and provides a web server on it.

  Steps:
  1. Connect to the access point "yourAp"
  2. Point your web browser to http://192.168.4.1/H to turn the LED on or http://192.168.4.1/L to turn it off
     OR
     Run raw TCP "GET /H" and "GET /L" on PuTTY terminal with 192.168.4.1 as IP address and 80 as port

  Created for arduino-esp32 on 04 July, 2018
  by Elochukwu Ifediora (fedy0)
*/

#include <WiFi.h>
#include <NetworkClient.h>
#include <WiFiAP.h>

#ifndef LED_BUILTIN
#define LED_BUILTIN 2  // Set the GPIO pin where you connected your test LED or comment this line out if your dev board has a built-in LED

#endif


//Definindo os pinos a serem utilizados
#define IN1 16
#define IN2 17
#define IN3 25
#define IN4 26


// Set these to your desired credentials.
const char *ssid = "EscalaArvore";
const char *password = "escaladorprofissional";

NetworkServer server(80);

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);

  Serial.begin(115200);
  Serial.println();
  Serial.println("Configuring access point...");

  // You can remove the password parameter if you want the AP to be open.
  // a valid password must have more than 7 characters
  if (!WiFi.softAP(ssid, password)) {
    log_e("Soft AP creation failed.");
    while (1);
  }
  IPAddress myIP = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(myIP);
  server.begin();

  Serial.println("Server started");

// Setup dos pinos dos dois motores
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

// Todos iniciam no LOW, ou seja, o motor inicia desligado
  digitalWrite(IN1, LOW); 
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);

}

void loop() {
  NetworkClient client = server.accept();  // listen for incoming clients

  if (client) {                     // if you get a client,
    Serial.println("New Client.");  // print a message out the serial port
    String currentLine = "";        // make a String to hold incoming data from the client
    while (client.connected()) {    // loop while the client's connected
      if (client.available()) {     // if there's bytes to read from the client,
        char c = client.read();     // read a byte, then
        Serial.write(c);            // print it out the serial monitor
        if (c == '\n') {            // if the byte is a newline character

          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println();

            // the content of the HTTP response follows the header:
            client.print("Click <a href=\"/H\">here</a> to turn ON the LED.<br>");
            client.print("Click <a href=\"/L\">here</a> to turn OFF the LED.<br>");

            // The HTTP response ends with another blank line:
            client.println();
            // break out of the while loop:
            break;
          } else {  // if you got a newline, then clear currentLine:
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }

        // Check to see if the client request was "GET /H" or "GET /L":
        if (currentLine.endsWith("GET /SUBIR")) {
          digitalWrite(LED_BUILTIN, HIGH);  // GET /H turns the LED on
        }
        if (currentLine.endsWith("GET /DESCER")) {
          digitalWrite(LED_BUILTIN, LOW);  // GET /L turns the LED off
        }


        //Movimento motores (controle por app)

        //SUBIR
        if (currentLine.endsWith("GET /SUBIR")) {
           digitalWrite(IN1, HIGH); 
           digitalWrite(IN2, LOW);
           digitalWrite(IN3, LOW);
           digitalWrite(IN4, LOW);
        }
        //DESCER
        if (currentLine.endsWith("GET /DESCER")) {
           digitalWrite(IN1, LOW); 
           digitalWrite(IN2, HIGH);
           digitalWrite(IN3, LOW);
           digitalWrite(IN4, LOW);
        }
        //BRAÇO HORÁRIO
        if (currentLine.endsWith("GET /HORARIO")) {
           digitalWrite(IN1, LOW); 
           digitalWrite(IN2, LOW);
           digitalWrite(IN3, HIGH);
           digitalWrite(IN4, LOW);
        }
        //BRAÇO ANTI-HORÁRIO
        if (currentLine.endsWith("GET /ANTI-HORARIO")) {
           digitalWrite(IN1, LOW); 
           digitalWrite(IN2, LOW);
           digitalWrite(IN3, LOW);
           digitalWrite(IN4, HIGH);
        }
        //PARAR
        if (currentLine.endsWith("GET /PARAR")) {
           digitalWrite(IN1, LOW); 
           digitalWrite(IN2, LOW);
           digitalWrite(IN3, LOW);
           digitalWrite(IN4, LOW);
        }

        }

        //OBS: Fiz o código pra um motor funcionar por vez, então ativar o Braço vai certamente desativar o movimento vertical!
        //Nesse caso, vale notar que o Motor A, correspondente aos pinos IN1 e IN2, será o de subida/descida, equanto o Motor B, correspondente aos pinos IN3 e IN4, será o do Braço


      }
    }
    // close the connection:
    client.stop();
    Serial.println("Client Disconnected.");
  }
