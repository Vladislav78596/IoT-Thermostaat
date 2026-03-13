# IoT-Thermostaat
#define BLYNK_TEMPLATE_ID "TMPL5Hj7p3zMh"
#define BLYNK_TEMPLATE_NAME "Vladislav Vlad test"
#define BLYNK_AUTH_TOKEN "5o8BMFzSAY8a2UfP82O7yN-BKlJQu8Yp"

#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
#include "DHT.h"


#define DHTPIN 4
#define DHTTYPE DHT11
#define LED_KETEL 15
#define BTN_UP 18
#define BTN_DOWN 19


char ssid[] = "embed";
char pass[] = "weareincontrol";

float huidigeTemp = 0;
float gewensteTemp = 21.0;
float hysteresys = 0.5;

DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;


BLYNK_WRITE(V3) {
  gewensteTemp = param.asFloat();
  Serial.print("Nieuw doel via App: ");
  Serial.println(gewensteTemp);
}


void sendSensorData() {
  huidigeTemp = dht.readTemperature();

  if (!isnan(huidigeTemp)) {
    Blynk.virtualWrite(V1, huidigeTemp);
    Blynk.virtualWrite(V2, digitalRead(LED_KETEL) * 255); 
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(LED_KETEL, OUTPUT);
  pinMode(BTN_UP, INPUT_PULLUP);
  pinMode(BTN_DOWN, INPUT_PULLUP);

  dht.begin();
  

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

 
  timer.setInterval(2000L, sendSensorData);
}

void loop() {
  Blynk.run();
  timer.run();


  if (digitalRead(BTN_UP) == LOW) {
    gewensteTemp += 0.5;
    Blynk.virtualWrite(V3, gewensteTemp); 
    delay(200);
  }
  if (digitalRead(BTN_DOWN) == LOW) {
    gewensteTemp -= 0.5;
    Blynk.virtualWrite(V3, gewensteTemp);
    delay(200);
  }


  if (huidigeTemp < (gewensteTemp - hysteresys)) {
    digitalWrite(LED_KETEL, HIGH);
  } else if (huidigeTemp > (gewensteTemp + hysteresys)) {
    digitalWrite(LED_KETEL, LOW);
  }
}
https://drive.google.com/file/d/1GW_bfjMaOCTClILYlGAdau2mg7Bb0aJE/view?usp=sharing
https://drive.google.com/file/d/1Etg-dr-SwLcj1Vv32AYvNazh_3hUtRpc/view?usp=sharing
