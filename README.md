#Arduino-IDE
===
#Some projects about arduino

#source code
===
#include <LiquidCrystal.h>//LCD
LiquidCrystal lcd(49, 48, 47, 43, 42, 41); //set lcd pin RS E D4 D5 D6 D7 
int K1state,K2state,K3state,K4state;
char DATA_TX[7] ={
  0xFF,0x00,0xFF,0xC0,0x03,0xA3,0xA0};
byte data;
byte data_H;
byte data_L;
int data_TOTAL;
void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);  
  Serial3.begin(115200); 
  lcd.begin(16, 2);//設定LCD種類 2行16字元
  lcd.setCursor(0, 0);
  lcd.print("BloodPressure"); //設定顯示標題字串 
  pinMode(33, INPUT_PULLUP);  
  pinMode(34, INPUT_PULLUP);  
  pinMode(35, INPUT_PULLUP);  
  pinMode(36, INPUT_PULLUP);         
  pinMode(A15, OUTPUT);//set pin mode 244 LED EN
  pinMode(A14, OUTPUT);//set pin mode 244 8X8 EN
  pinMode(A13, OUTPUT);//set pin mode 244 SEG EN
  digitalWrite(A15,LOW);
  digitalWrite(A14, HIGH);
  digitalWrite(A13, HIGH);  //選擇需致能的部份LED 8X8 SEG                      
  for (int i = 2; i < 10; i ++){//for迴圈123456789 
    pinMode(i, OUTPUT);
    digitalWrite(i,HIGH);//輸出HIGH LED不亮  
  }
  pinMode(44, OUTPUT);
  pinMode(45, OUTPUT);
  pinMode(46, OUTPUT);   
  analogWrite(44, 255);//類比輸出R0,RGB值為RFF亮,RGB00000黑,RGBFFFFFF白
  analogWrite(45, 255);//類比輸出G0,RGB值為GFF亮,RGB00000黑,RGBFFFFFF白
  analogWrite(46, 255);//類比輸出B0,RGB值為BFF亮,RGB00000黑,RGBFFFFFF白       
  LCD2_CLS();//清除LCD第二行副程式    
  //lcd.setCursor(0, 1);;lcd.print("BPM=");  
  //lcd.setCursor(9, 1);;lcd.print("O2="); 
  delay(500); 
}

void loop() {
  // put your main code here, to run repeatedly: 
  K1state = digitalRead(33);
  if(K1state==0){
    Serial3.write((byte*) DATA_TX,7);
    lcd.setCursor(0, 1);
    lcd.print(" START     ");
    for (int i = 2; i < 10; i ++){//for迴圈123456789 
      digitalWrite(i,HIGH);//輸出HIGH LED不亮  
    }
  }  
  while (Serial3.available()>=7){
    data=Serial3.read();      
    if(data ==255){         
      data=Serial3.read();  
      if(data ==192){ 
        data=Serial3.read();   
        if(data ==5){ 
          data=Serial3.read();
          data=Serial3.read();
          data_H=Serial3.read(); 
          data_L=Serial3.read();  
          lcd.setCursor(0, 1);
          lcd.print(" START     ");
          lcd.setCursor(8,1);
          lcd.print(data_L);
          lcd.print("mmHg  ");    
        }       
        if(data ==8){ 
          for (int i = 2; i < 10; i ++){//for迴圈123456789 
            digitalWrite(i,LOW);//輸出LOW LED亮  
          }         
          LCD2_CLS();//清除LCD第二行副程式           
          data=Serial3.read();
          data=Serial3.read();
          data=Serial3.read();
          data=Serial3.read();
          lcd.setCursor(0,1);
          lcd.print(data);
          lcd.print("  ");        
          data=Serial3.read();
          data=Serial3.read();
          lcd.setCursor(4,1);
          lcd.print(data);
          lcd.print("  ");        
          data=Serial3.read();   
          lcd.setCursor(8,1);
          lcd.print("BPM:");
          lcd.print(data);
          lcd.print("  ");             
        }              
      }       
    }
  }    
} 
//清除LCD第二行副程式
void LCD2_CLS(){
  lcd.setCursor(0, 1);
  lcd.print("                "); //LCD填入16個空白字串即清除LCD顯示 
}
//==========================================================================
