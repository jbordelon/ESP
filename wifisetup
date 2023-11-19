/*
José Bordelon 19nov2023
Ejemplo de configuración de WiFi y campo personalizado DirWeb para ESP8266 y ESP32
Label GPIO  Input       Output          Notes
 D0    16   no int      LED no PWM I2C  support HIGH at boot used to wake up from deep sleep
 D1     5   OK          OK              often used as SCL (I2C)
 D2     4   OK          OK              often used as SDA (I2C)
 D3     0   pulled up   OK              connected to FLASH button, boot fails if pulled LOW
 D4     2   pulled up   OK LED          LED INTERNO HIGH at boot connected, boot fails if pulled LOW
 D5    14   OK          OK              SPI (SCLK)
 D6    12   OK          OK              SPI (MISO)
 D7    13   OK          OK              SPI (MOSI)
 D8    15   pulled GND  OK              SPI (CS) Boot fails if pulled HIGH
 RX     3   OK          RX pin          HIGH at boot
 TX     1   TX pin      OK              HIGH at boot debug output at boot, boot fails if pulled LOW
 A0  ADC0   Analog In   X
*/

#include <LittleFS.h>
#include <WiFiManager.h>

#define PinFlash D3
#define Led1 D0
#define Led2 D4
#define ArchConf "/config"

WiFiManager wm;
int Cnt=0;
String DirWeb = "http://";

void CambiaConfig(){
  wm.setConfigPortalTimeout(180);
  WiFiManagerParameter CampoDirWeb("label", "DirWeb", DirWeb.c_str(), 50);
  wm.addParameter(&CampoDirWeb);
  if (!wm.startConfigPortal("DirWeb")) {
    Serial.println("==== Timeout ====");
    delay(3000); ESP.restart(); delay(5000); return;
  }
  String DirIn = CampoDirWeb.getValue(); delay(2000);
  if (DirIn == DirWeb){Serial.print("Dir igual: "); Serial.println(DirIn);}
  else {
    Serial.print("Guardando dir: "); Serial.println(DirIn);
    DirWeb = DirIn;
    File f = LittleFS.open(ArchConf, "w");
    if (f){ f.print(DirWeb); f.close(); Serial.println("==== Guardado ====");}
    else {Serial.println("Error guardando DirWeb");}
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(Led1, OUTPUT);
  pinMode(Led2, OUTPUT);
  pinMode(PinFlash, INPUT_PULLUP); 
  Serial.println("===="); Serial.println("==== WiFiSetup V 1.0 ====");
  if(LittleFS.begin()){
    File f = LittleFS.open(ArchConf, "r");
    if (f){DirWeb = f.readString(); f.close();}
    else{Serial.println("Error obteniendo DirWeb");}
  }
  if (!wm.autoConnect("DirWeb")) {
    Serial.print("No pudo conectarse al WiFi "); Serial.println(WiFi.SSID());
    Serial.println("Reinicio en 3 segundos...");
    delay(3000); ESP.reset(); delay(5000);
  }
}

void loop() {
  Cnt++;
  if(Cnt > 9){
    Cnt=0;
    Serial.print(WiFi.SSID()); Serial.print(" "); Serial.print(WiFi.localIP()); Serial.print(" "); Serial.print(WiFi.hostname());
    Serial.print(" "); Serial.print(WiFi.RSSI()); Serial.print("dBm "); Serial.println(DirWeb);
  }
  if(digitalRead(PinFlash)==LOW){
    Serial.println("Abre AP DirWeb");
    CambiaConfig();
  }
  if(digitalRead(Led1)==HIGH){
    digitalWrite(Led1, LOW);
    digitalWrite(Led2, HIGH);
  }else{
    digitalWrite(Led1, HIGH);
    digitalWrite(Led2, LOW);
  }
  delay(1000);
}
