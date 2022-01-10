# PDT
The code is for The Programmable Digital Thermostat.

#include <LiquidCrystal.h>
#include<EEPROM.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
#define ENC_A 6
#define ENC_B 7
#define ENC_BUTTON 8
#define SENSOR A0
#define RELAY 13
#define BUZZER A1

int counter,initialState,finalState,lowLimit,upLimit,mode,waitTime=5000,delayTime,relayPwr=LOW;
float temp,temperature;

void setTemp(int value,int set) {
  if(set==0) {
    Serial.print("Lower Limit:");
    Serial.println(value);
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Lower Limit:");
    lcd.setCursor(7, 1);
    lcd.print(value);
    while(digitalRead(ENC_BUTTON)==LOW) {
      initialState=digitalRead(ENC_A);
      delay(1);
      finalState=digitalRead(ENC_A);
      if(finalState!=initialState){
        if(digitalRead(ENC_B)!=finalState) {
          if(value<upLimit-1 && value<80) {
            value++;
            lcd.clear();
            Serial.print("Lower Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Lower Limit:");
            lcd.setCursor(7, 1);
            lcd.print(value);
          }
          else {
            lcd.clear();
            Serial.print("Lower Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Lower Limit:");
            lcd.setCursor(7, 1);
            digitalWrite(BUZZER,LOW);
            delay(10);
            lcd.print(value);
            digitalWrite(BUZZER,HIGH);
          }
        }
        else {
          if(value>16) {
            value--;
            lcd.clear();
            Serial.print("Lower Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Lower Limit:");
            lcd.setCursor(7, 1);
            lcd.print(value);
          }
          else {
            lcd.clear();
            Serial.print("Lower Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Lower Limit:");
            lcd.setCursor(7, 1);
            digitalWrite(BUZZER,LOW);
            delay(10);
            lcd.print(value);
            digitalWrite(BUZZER,HIGH);
          }
        }
      }
    }
    lowLimit=value;
    lcd.clear();
  }
  else {
    Serial.print("Upper Limit:");
    Serial.println(value);
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Upper Limit:");
    lcd.setCursor(7, 1);
    lcd.print(value);
    while(digitalRead(ENC_BUTTON)==LOW) {
      initialState=digitalRead(ENC_A);
      delay(1);
      finalState=digitalRead(ENC_A);
      if(finalState!=initialState){
        if(digitalRead(ENC_B)!=finalState) {
          if(value<80) {
            value++;
            lcd.clear();
            Serial.print("Upper Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Upper Limit:");
            lcd.setCursor(7, 1);
            lcd.print(value);
          }
          else {
            lcd.clear();
            Serial.print("Upper Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Upper Limit:");
            lcd.setCursor(7, 1);
            digitalWrite(BUZZER,LOW);
            delay(10);
            lcd.print(value);
            digitalWrite(BUZZER,HIGH);
          }
        }
        else {
          if(value>16 && value>lowLimit+1) {
            value--;
            lcd.clear();
            Serial.print("Upper Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Upper Limit:");
            lcd.setCursor(7, 1);
            lcd.print(value);
          }
          else {
            lcd.clear();
            Serial.print("Upper Limit:");
            Serial.println(value);
            lcd.setCursor(0, 0);
            lcd.print("Upper Limit:");
            lcd.setCursor(7, 1);
            digitalWrite(BUZZER,LOW);
            delay(10);
            lcd.print(value);
            digitalWrite(BUZZER,HIGH);
          }
        }
      }
    }
    upLimit=value;
    lcd.clear();
  }
}

void setMode() {
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Mode:");
  lcd.setCursor(5,1);
  if(mode==1) {
    lcd.print("Cooling");
  }
  else {
    lcd.print("Heating");
  }
  while(digitalRead(ENC_BUTTON)==LOW) {
    initialState=digitalRead(ENC_A);
    delay(1);
    finalState=digitalRead(ENC_A);
    if(finalState!=initialState){
      mode=-mode;
      lcd.clear();
      lcd.setCursor(0,0);
      lcd.print("Mode:");
      lcd.setCursor(5,1);
      if(mode==1) {
        lcd.print("Cooling");
      }
      else {
        lcd.print("Heating");
      }
    }
  }
}

void setDelay() {
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Delay:");
  lcd.setCursor(2,1);
  lcd.print(delayTime/60);
  lcd.print(" Min ");
  lcd.print(delayTime%60);
  lcd.print(" Sec");
  while(digitalRead(ENC_BUTTON)==LOW) {
    initialState=digitalRead(ENC_A);
    delay(1);
    finalState=digitalRead(ENC_A);
    if(finalState!=initialState){
      if(digitalRead(ENC_B)!=finalState) {
        if(delayTime<255) {
          delayTime++;
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("Delay:");
          lcd.setCursor(2,1);
          lcd.print(delayTime/60);
          lcd.print(" Min ");
          lcd.print(delayTime%60);
          lcd.print(" Sec");
        }
        else {
          lcd.clear();
          Serial.print("Delay:");
          Serial.println(delayTime);
          lcd.setCursor(0, 0);
          lcd.print("Delay:");
          lcd.setCursor(2, 1);
          digitalWrite(BUZZER,LOW);
          delay(10);
          lcd.print(delayTime/60);
          lcd.print(" Min ");
          lcd.print(delayTime%60);
          lcd.print(" Sec");
          digitalWrite(BUZZER,HIGH);
        }
      }
      else {
        if(delayTime>0) {
          delayTime--;
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("Delay:");
          lcd.setCursor(2,1);
          lcd.print(delayTime/60);
          lcd.print(" Min ");
          lcd.print(delayTime%60);
          lcd.print(" Sec");
        }
        else {
          lcd.clear();
          Serial.print("Delay:");
          Serial.println(delayTime);
          lcd.setCursor(0, 0);
          lcd.print("Delay:");
          lcd.setCursor(2, 1);
          digitalWrite(BUZZER,LOW);
          delay(10);
          lcd.print(delayTime/60);
          lcd.print(" Min ");
          lcd.print(delayTime%60);
          lcd.print(" Sec");
          digitalWrite(BUZZER,HIGH);
        }
      }
    }
  }
}

void menu() {
  setMode();
  digitalWrite(BUZZER,LOW);
  delay(10);
  digitalWrite(BUZZER,HIGH);
  delay(200);
  setTemp(lowLimit,0);
  digitalWrite(BUZZER,LOW);
  delay(10);
  digitalWrite(BUZZER,HIGH);
  delay(200);
  setTemp(upLimit,1);
  digitalWrite(BUZZER,LOW);
  delay(10);
  digitalWrite(BUZZER,HIGH);
  delay(200);
  setDelay();
  digitalWrite(BUZZER,LOW);
  delay(10);
  digitalWrite(BUZZER,HIGH);
  delay(200);
  if(EEPROM.read(0)!=delayTime) {
    EEPROM.write(0,delayTime);
  }
  if(EEPROM.read(1)!=lowLimit) {
    EEPROM.write(1,lowLimit);
  }
  if(EEPROM.read(2)!=upLimit) {
    EEPROM.write(2,upLimit);
  }
  if(EEPROM.read(3)!=mode+1) {
    EEPROM.write(3,mode+1);
  }
  delay(200);
  
}
void setup() {
  pinMode(ENC_A,INPUT);
  pinMode(ENC_B,INPUT);
  pinMode(ENC_BUTTON,INPUT);
  pinMode(SENSOR,INPUT);
  pinMode(RELAY,OUTPUT);
  pinMode(BUZZER,OUTPUT);
  Serial.begin(9600);
  lcd.begin(16,2);
  digitalWrite(BUZZER,HIGH);
  delayTime=EEPROM.read(0);
  lowLimit=EEPROM.read(1);
  upLimit=EEPROM.read(2);
  mode=EEPROM.read(3)-1;
  counter=2*delayTime;
  while(counter!=0) {
    if(counter%2==1) {
      lcd.clear();
    }
    else {
      lcd.clear();
      lcd.setCursor(2,0);
      lcd.print((counter/2)/60);
      lcd.print(" Min ");
      lcd.print((counter/2)%60);
      lcd.print(" Sec");
      lcd.setCursor(4,1);
      lcd.print(" To Go!");
    }
    counter--;
    delay(500);
  }
  digitalWrite(BUZZER,LOW);
  delay(10);
  digitalWrite(BUZZER,HIGH);
}
 
void loop() {
  temp=analogRead(SENSOR);
  temperature=(1476/(temp-780));
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Temp: ");
  lcd.print(temperature);
  if(relayPwr==HIGH) {
    lcd.setCursor(10,0);
    lcd.print(" | ON");
  }
  else {
    lcd.setCursor(10,0);
    lcd.print(" | OFF");
  }
  lcd.setCursor(0,1);
  lcd.print("Low: ");
  lcd.print(lowLimit);
  lcd.print(" | Up: ");
  lcd.print(upLimit);
  if(mode==1) {
    if(temperature>upLimit) {
      relayPwr=HIGH;
    }
    else if(temperature<lowLimit) {
      relayPwr=LOW;
    }
  }
  else {
    if(temperature>upLimit) {
      relayPwr=LOW;
    }
    else if(temperature<lowLimit) {
      relayPwr=HIGH;
    }
  }
  digitalWrite(RELAY,relayPwr);
  while(digitalRead(ENC_BUTTON)==HIGH) {
    counter++;
    delay(200);
    temp=analogRead(SENSOR);
    temperature=(1476/(temp-780));
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Temp: ");
    lcd.print(temperature);
    if(relayPwr==HIGH) {
      lcd.setCursor(10,0);
      lcd.print(" | ON");
    }
    else {
      lcd.setCursor(10,0);
      lcd.print(" | OFF");
    }
    lcd.setCursor(0,1);
    lcd.print("Low: ");
    lcd.print(lowLimit);
    lcd.print(" | Up: ");
    lcd.print(upLimit);
    if(mode==1) {
      if(temperature>upLimit) {
        relayPwr=HIGH;
      }
      else if(temperature<lowLimit) {
        relayPwr=LOW;
      }
    }
    else {
      if(temperature>upLimit) {
        relayPwr=LOW;
      }
      else if(temperature<lowLimit) {
        relayPwr=HIGH;
      }
    }
    digitalWrite(RELAY,relayPwr);
    if(counter==10) {
      digitalWrite(BUZZER,LOW);
      delay(10);
      digitalWrite(BUZZER,HIGH);
      while(digitalRead(ENC_BUTTON)==HIGH) {
        delay(10);
      }
      menu();
    }
  }
  counter=0;
  delay(200);
}
