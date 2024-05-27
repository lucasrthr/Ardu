#include <ArduinoMqttClient.h>
#include <WiFiNINA.h>
 
char ssid[] = SECRET_SSID;        // your network SSID (name)
char pass[] = SECRET_PASS;    // your network password (use for WPA, or use as key for WEP)
 
WiFiClient wifiClient;
MqttClient mqttClient(wifiClient);
 
const char broker[] = "test.mosquitto.org";
int        port     = 1883;
const char topic[]  = "real_unique_topic";  //Das Thema, in welches der Wert unten geschrieben werden soll
 
 
//set interval for sending messages (milliseconds)
const long interval = 2000;                //nur, wenn durchgehend gesendet werden soll
unsigned long previousMillis = 0;     //nur, wenn durchgehend gesendet werden soll
 
int count = 0;
 
void setup() {
  //Initialize serial and wait for port to open:
  Serial.begin(9600);
  while (!Serial) {
    ; // wait for serial port to connect. Needed for native USB port only
  }
 
  // attempt to connect to Wifi network:
  while (WiFi.begin(ssid, pass) != WL_CONNECTED) {
    // failed, retry
    Serial.print(".");
    delay(5000);
  }
 
  if (!mqttClient.connect(broker, port)) {
    Serial.print("MQTT connection failed! Error code = ");
    Serial.println(mqttClient.connectError());
 
    while (1);
  }
}
 
void loop() {
  //Keep-alive senden
  mqttClient.poll();
 
  unsigned long currentMillis = millis();                //nur, wenn durchgehend was gesendet werden soll!
                                                                             //
  if (currentMillis - previousMillis >= interval) {   //
    // save the last time a message was sent        //
    previousMillis = currentMillis;                         //
 
    //Wert, der an Broker gesendet werden soll
    int value = "test"
 
    // send message, the Print interface can be used to set the message contents
    mqttClient.beginMessage(topic);
    mqttClient.print(value);
    mqttClient.endMessage();
  }
}
