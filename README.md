//WaterClass.ion

#include "WaterClass.h"

Water water;

int ledPin = 4;
int waterPin = 2;
float waterInterval = 5;
bool waterIsInInterval = false;
float waterStartTime;
bool value = true;

void setup() {
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  unsigned long currentMillis = millis();
  if(waterIsInInterval == false)
  {
    waterStartTime = water.MillisecondsToSeconds(currentMillis);
    water.waterSensor(waterPin, 200, ledPin);
  }
 
  waterIsInInterval = water.IsTimePassedFromInterval(waterStartTime, waterInterval);
}

//WaterClass.cpp#include "HardwareSerial.h"
#include "esp32-hal.h"
#include "esp32-hal-gpio.h"
#include "WaterClass.h"
#include "Arduino.h"

Water::Water()
{

}


void Water::waterSensor(int waterPin, int threshold, int ledPin)
{
  int value = analogRead(waterPin);
  Serial.println(value);
  if(value < threshold)
  {
    toggleOff(ledPin);
  }else {
    toggleOn(ledPin);
  }
}

void Water::toggleOn(int pin)
{
  digitalWrite(pin, HIGH);
}

void Water::toggleOff(int pin)
{
  digitalWrite(pin, LOW);
}

float Water::MillisecondsToSeconds(int milliseconds) 
{
  float second = float(milliseconds) / 1000;
  return second;
}

bool Water::IsTimePassedFromInterval(float startTimeInSeconds, float intervalInSeconds)
{
  float endTimeSeconds = startTimeInSeconds + intervalInSeconds;
  unsigned long currentMillis = millis();
  float currentSeconds = MillisecondsToSeconds(currentMillis);

  if(currentSeconds < endTimeSeconds)
  {
    return true;
  }
  else
  {
    return false;
  }
}
//WaterClass.h
#include "soc/soc_caps.h"
#ifndef WaterClass_h
#define WaterClass_h

#include "Arduino.h"

class Water
{
  public:
    Water();
    void waterSensor(int waterPin, int threshold, int ledPin);
    void toggleOn(int pin);
    void toggleOff(int pin);
    bool IsTimePassedFromInterval(float startTimeInSeconds, float intervalInSeconds);
    float MillisecondsToSeconds(int milliseconds);

};
#endif
