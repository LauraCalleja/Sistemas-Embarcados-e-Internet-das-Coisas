# Sistemas-Embarcados-e-Internet-das-Coisas
Nosso projeto envolve o desenvolvimento e o design de um sensor que detecta quando um item é roubado. Ele é destinado principalmente a mochilas, detectando quando o item é afastado do dono devido a interferência de sinal. O sensor emite um som até ser destravado por meio de outro dispositivo que utiliza frequências diferentes.


#include <BluetoothSerial.h>
#include <SPI.h>
#include <MFRC522.h>

BluetoothSerial SerialBT;

#define SS_PIN 5
#define RST_PIN 22
MFRC522 mfrc522(SS_PIN, RST_PIN);


int buzzer = 13;
int led = 12;

bool sistemaArmado = false;
bool alarmeDisparado = false;

void setup() {

  Serial.begin(115200);

  pinMode(buzzer, OUTPUT);
  pinMode(led, OUTPUT);

  SerialBT.begin("MochilaSegura");

  SPI.begin();
  mfrc522.PCD_Init();

}

void loop() {

  verificarRFID();

  if(sistemaArmado && !alarmeDisparado){

    if(!SerialBT.hasClient()){
      
      alarmeDisparado = true;

    }

  }

  if(alarmeDisparado){

    digitalWrite(buzzer, HIGH);
    digitalWrite(led, HIGH);
    delay(200);
    digitalWrite(led, LOW);
    delay(200);

  }
  else{

    digitalWrite(buzzer, LOW);

  }

}

void verificarRFID(){

  if (!mfrc522.PICC_IsNewCardPresent()) return;
  if (!mfrc522.PICC_ReadCardSerial()) return;

  Serial.println("Cartao detectado");

  if(!sistemaArmado){

    if(SerialBT.hasClient()){

      sistemaArmado = true;
      Serial.println("Sistema ARMADO");

      piscarLED(3);

    }

  }
  else{

    sistemaArmado = false;
    alarmeDisparado = false;

    Serial.println("Sistema DESARMADO");

    piscarLED(5);

  }

}

void piscarLED(int vezes){

  for(int i=0;i<vezes;i++){

    digitalWrite(led,HIGH);
    delay(200);
    digitalWrite(led,LOW);
    delay(200);

  }

}
