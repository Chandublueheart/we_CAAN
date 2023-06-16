#include <SoftwareSerial.h>
#include <TinyGPS++.h>
const int buzz= 5;
const int trigPin = A2;
const int echoPin = A3;
long duration;
int distance;
int button = 12;

float latitude;
float longitude;

float n[2];

SoftwareSerial gsmSerial(8,9);
SoftwareSerial gpsSerial(6,7);
TinyGPSPlus gps;
const char *gpsStream =
  "$GPRMC,045103.000,A,3014.1984,N,09749.2872,W,0.67,161.46,030913,,,A*7C\r\n"
  "$GPGGA,045104.000,3014.1985,N,09749.2873,W,1,09,1.2,211.6,M,-22.5,M,,0000*62\r\n"
  "$GPRMC,045200.000,A,3014.3820,N,09748.9514,W,36.88,65.02,030913,,,A*77\r\n"
  "$GPGGA,045201.000,3014.3864,N,09748.9411,W,1,10,1.2,200.8,M,-22.5,M,,0000*6C\r\n"
  "$GPRMC,045251.000,A,3014.4275,N,09749.0626,W,0.51,217.94,030913,,,A*7D\r\n"
  "$GPGGA,045252.000,3014.4273,N,09749.0628,W,1,09,1.3,206.9,M,-22.5,M,,0000*6F\r\n";


void setup()
{
  pinMode(5,OUTPUT);
  pinMode(trigPin,OUTPUT);
  pinMode(echoPin,INPUT);
  pinMode(A4,INPUT_PULLUP);
  Serial.begin(115200);
  pinMode(button,INPUT_PULLUP);
  Serial.begin(115200);
  delay(1000);
  gpsSerial.begin(115200);
  delay(1000);
  Serial.println("-TRACKING-");
  Serial.println("LOCATION");
  Serial.println("INITIALIZING");
  delay(2000);
  Serial.println("System ready");
  delay(1000);
  Serial.begin(115200);
  while (*gpsStream)
    if (gps.encode(*gpsStream++))
    {
      displayInfo();
    }
  Serial.println(F("Done."));
}

void loop()
{
  digitalWrite(trigPin,LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin,HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin,LOW);
  duration = pulseIn(echoPin,HIGH);
  distance = duration*0.034/2;
  Serial.println("Distance:");
  Serial.println(distance);
  

  if(distance<=20)
  {
    Serial.print("OBJECT IS AT A DISTANCE 20CM!");
    Serial.println(distance);
    digitalWrite(5,HIGH);
    delay(2000);
    digitalWrite(5,LOW);
    delay(2000);
  }
  else if(distance<=15)
  {
    Serial.print("OBJECT IS AT A DISTANCE OF 15CM");
    Serial.println(distance);
    digitalWrite(5,HIGH);
    digitalWrite(5,LOW);
    delay(500);
    digitalWrite(5,HIGH);
    digitalWrite(5,LOW);
    delay(500);
  }
  if(digitalRead(button)==LOW)
  {
    Serial.println("BUTTON PRESSED");
    delay(2000);
    displayInfo();
  }
}
void displayInfo()
{
  Serial.print(F("Location: ")); 
  if (gps.location.isValid())
  {
    Serial.print(gps.location.lat(), 6);
    Serial.print(F(","));
    Serial.print(gps.location.lng(), 6);
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F("  Date/Time: "));
  if (gps.date.isValid())
  {
    Serial.print(gps.date.month());
    Serial.print(F("/"));
    Serial.print(gps.date.day());
    Serial.print(F("/"));
    Serial.print(gps.date.year());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F(" "));
  if (gps.time.isValid())
  {
    if (gps.time.hour() < 10) Serial.print(F("0"));
    Serial.print(gps.time.hour());
    Serial.print(F(":"));
    if (gps.time.minute() < 10) Serial.print(F("0"));
    Serial.print(gps.time.minute());
    Serial.print(F(":"));
    if (gps.time.second() < 10) Serial.print(F("0"));
    Serial.print(gps.time.second());
    Serial.print(F("."));
    if (gps.time.centisecond() < 10) Serial.print(F("0"));
    Serial.print(gps.time.centisecond());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.println();
}
