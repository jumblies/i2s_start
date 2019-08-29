#include <Arduino.h>
#ifdef ESP32
#include <WiFi.h>
#include "SPIFFS.h"
#else
#include <ESP8266WiFi.h>
#endif
#include "AudioFileSourceSPIFFS.h"
#include "AudioFileSourceID3.h"
#include "AudioGeneratorMP3.h"
#include "AudioOutputI2S.h"
#include <PubSubClient.h>
#include <DNSServer.h>
#include <ESP8266WebServer.h>
#include <WiFiManager.h> //https://github.com/tzapu/WiFiManager
#include <ArduinoOTA.h>

// Globals
bool otaInProgress = false;
const char topic1[] = "testtopic/espweb";

// To run, set your ESP8266 build to 160MHz, and include a SPIFFS of 512KB or greater.
// Use the "Tools->ESP8266/ESP32 Sketch Data Upload" menu to write the MP3 to SPIFFS
// Then upload the sketch normally.

// Instantiate objects
WiFiClient espClient;
PubSubClient client(espClient);
AudioGeneratorMP3 *mp3;
AudioFileSourceSPIFFS *file;
AudioOutputI2S *out;
AudioFileSourceID3 *id3;

// Called when a metadata event occurs (i.e. an ID3 tag, an ICY block, etc.
void MDCallback(void *cbData, const char *type, bool isUnicode, const char *string)
{
  (void)cbData;
  Serial.printf("ID3 callback for: %s = '", type);

  if (isUnicode)
  {
    string += 2;
  }

  while (*string)
  {
    char a = *(string++);
    if (isUnicode)
    {
      string++;
    }
    Serial.printf("%c", a);
  }
  Serial.printf("'\n");
  Serial.flush();
}

// const char *mqtt_server = "10.10.10.142";
const char *mqtt_server = "broker.mqtt-dashboard.com";

long lastMsg = 0;
char msg[50];
const char *ssid = "shmoo3";
const char *password = "s1lkw0rm";
int value = 0;

void callback(char *topic, byte *payload, unsigned int length)
{
  String topicStr = topic;
  //EJ: Note:  the "topic" value gets overwritten everytime it receives confirmation (callback) message from MQTT

  //Print out some debugging info
  Serial.println("Callback update.");
  Serial.print("Topic: ");
  Serial.println(topicStr);

  if (topicStr == topic1)
  {

    //turn the switch on if the payload is '1' and publi   to the MQTT server a confirmation message
    if (payload[0] == '1')
    {

      if (mp3->isRunning())
      {
        mp3->stop();
      }
      // digitalWrite(switchPin1, HIGH);
      // client.publish(switchTopic1Confirm, "1");
      file = new AudioFileSourceSPIFFS("/bender.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }

    //turn the switch off if the payload is '0' and publish to the MQTT server a confirmation message
    else if (payload[0] == '0')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      // client.publish(switchTopic1Confirm, "1");
      file = new AudioFileSourceSPIFFS("/pno-cs.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else if (payload[0] == '2')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      file = new AudioFileSourceSPIFFS("/hal.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else if (payload[0] == '3')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      file = new AudioFileSourceSPIFFS("/Jibba Jabba.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else if (payload[0] == '4')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      file = new AudioFileSourceSPIFFS("/Hello peasants.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else if (payload[0] == '5')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      file = new AudioFileSourceSPIFFS("/Lied to me.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else if (payload[0] == '6')
    {
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      file = new AudioFileSourceSPIFFS("/You got it genius.mp3");
      id3 = new AudioFileSourceID3(file);
      id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
      out = new AudioOutputI2S();
      mp3 = new AudioGeneratorMP3();
      mp3->begin(id3, out);
    }
    else
    {mp3->stop();
      if (mp3->isRunning())
      {
        mp3->stop();
      }
      Serial.println("No Valid Message Received");
    }
  }
}

void reconnect()
{
  //attempt to connect to the wifi if connection is lost
  if (WiFi.status() != WL_CONNECTED)
  {
    //loop while we wait for connection
    while (WiFi.status() != WL_CONNECTED)
    {
      delay(500);
      Serial.print(".");
    }

    //print out some more debug once connected
    Serial.println("");
    Serial.println("WiFi connected");
    Serial.println("IP address: ");
    Serial.println(WiFi.localIP());
  }
  //make sure we are connected to WIFI before attemping to reconnect to MQTT
  if (WiFi.status() == WL_CONNECTED)
  {
    // Loop until we're reconnected to the MQTT server
    while (!client.connected())
    {
      Serial.print("Attempting MQTT connection...");

      // Generate client name based on MAC address and last 8 bits of microsecond counter
      String clientName;
      clientName += "espmp3";

      //if connected, subscribe to the topic(s) we want to be notified about
      //EJ: Delete "mqtt_username", and "mqtt_password" here if you are not using any
      if (client.connect((char *)clientName.c_str(), "", ""))
      {
        //EJ: Update accordingly with your MQTT account
        Serial.println("\tMQTT Connected");
        client.subscribe(topic1);
      }
      //otherwise print failed for debugging
      else
      {
        Serial.println("\tFailed.");
        abort();
      }
    }
  }
}

void setup()
{

  SPIFFS.begin();
  Serial.begin(115200);
  delay(1000);
  WiFiManager wifiManager;
  wifiManager.autoConnect("AutoConnectAP");
  Serial.println("connected...yeey :)");

  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);

  // Serial.printf("Sample MP3 playback begins...\n");

  audioLogger = &Serial;
  file = new AudioFileSourceSPIFFS("/Hello peasants.mp3");
  id3 = new AudioFileSourceID3(file);
  id3->RegisterMetadataCB(MDCallback, (void *)"ID3TAG");
  out = new AudioOutputI2S();
  mp3 = new AudioGeneratorMP3();
  mp3->begin(id3, out);
  // file.close();

  // INIT OTA
  ArduinoOTA.setHostname("ESPAUDIO");
  ArduinoOTA.onStart([]() {
    otaInProgress = true;
    Serial.println(F("Start updating..."));
  });
  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.println(progress / (total / 100));
  });
  ArduinoOTA.onEnd([]() {
    Serial.println(F("\nend"));
    otaInProgress = false;
  });
  ArduinoOTA.onError([](ota_error_t error) {
    String msg;
    if (error == OTA_AUTH_ERROR)
      msg = F("auth failed");
    else if (error == OTA_BEGIN_ERROR)
      msg = F("begin failed");
    else if (error == OTA_CONNECT_ERROR)
      msg = F("connect failed");
    else if (error == OTA_RECEIVE_ERROR)
      msg = F("receive failed");
    else if (error == OTA_END_ERROR)
      msg = F("end failed");
    Serial.printf_P(PSTR("Error: %s"), msg.c_str());
  });
  ArduinoOTA.begin();
}

void loop()
{
  if (!client.connected())
  {
    reconnect();
  }
  client.loop();
  if (mp3->isRunning())
  {
    if (!mp3->loop())
      mp3->stop();
  }
  // else
  // {
  //   Serial.printf("MP3 done\n");
  //   delay(1000);
  // }
  ArduinoOTA.handle();
}
