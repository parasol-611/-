#include <Wire.h> 
#include <LiquidCrystal_I2C.h>
#include <Servo.h>
#include "pitches.h"
LiquidCrystal_I2C lcd(0x27,16,2);  // set the LCD address to 0x27 for a 16 chars and 2 line display

int melody[] = {
 NOTE_E4, NOTE_E4, NOTE_E4, NOTE_C4, NOTE_E4, NOTE_G4, NOTE_G3,  
NOTE_C4, NOTE_G3, NOTE_E3, NOTE_A3, NOTE_B3, NOTE_AS3, NOTE_A3, NOTE_G3, NOTE_E4, NOTE_G4, NOTE_A4, NOTE_F4, NOTE_G4, NOTE_E4, NOTE_C4, NOTE_D4, NOTE_B3, 
NOTE_C4, NOTE_G3, NOTE_E3, NOTE_A3, NOTE_B3, NOTE_AS3, NOTE_A3, NOTE_G3, NOTE_E4, NOTE_G4, NOTE_A4, NOTE_F4, NOTE_G4, NOTE_E4, NOTE_C4, NOTE_D4, NOTE_B3, 
NOTE_G4, NOTE_FS4, NOTE_E4, NOTE_DS4, NOTE_E4, NOTE_GS3, NOTE_A3, NOTE_C4, NOTE_A3, NOTE_C4, NOTE_D4, NOTE_G4, NOTE_FS4, NOTE_E4, NOTE_DS4, NOTE_E4, NOTE_C5, NOTE_C5, NOTE_C5, 
NOTE_G4, NOTE_FS4, NOTE_E4, NOTE_DS4, NOTE_E4, NOTE_GS3, NOTE_A3, NOTE_C4, NOTE_A3, NOTE_C4, NOTE_D4, NOTE_DS4, NOTE_D4, NOTE_C4, 
NOTE_C4, NOTE_C4, NOTE_C4, NOTE_C4, NOTE_D4, NOTE_E4, NOTE_C4, NOTE_A3, NOTE_G3, NOTE_C4, NOTE_C4, NOTE_C4, NOTE_C4, NOTE_D4, NOTE_E4, 
NOTE_C4, NOTE_C4, NOTE_C4, NOTE_C4, NOTE_D4, NOTE_E4, NOTE_C4, NOTE_A3, NOTE_G3};
int duration = 110;  

Servo myservo;
Servo myservo2;
int analogInPin0 = A0;//定义模拟信号1在A0脚位
int analogInPin1 = A1;//定义模拟信号2在A1脚位
int analogInPin2 = A2;//定义模拟信号3在A2脚位
int analogInPin3 = A3;//定义模拟信号4在A3脚位
int delay_counter = 0;
int sensorValue = 0; 
int sensorValue1 = 0; 
int sensorValue2 = 0; 
int sensorValue3 = 0; 

int R_LED = 6;//定义三色RGB的红色脚位
int G_LED = 5;//定义三色RGB的绿色脚位
int B_LED = 3;//定义三色RGB的蓝色脚位
int W3w_LED = 13;//定义3W LED 脚位
int value = 0;
int Pass_Key = 0;
int Led_PWM2_Counter= 0;
int val;
int val2;
int rate = 20;
int key = 0;
char Pass1_buf[4];
char Pass2_buf[4];
//===========   setup ===========================//
void setup(){
    // set up the LCD's number of columns and rows: 
 
  lcd.init();   // initialize the lcd 
  lcd.init();
  lcd.backlight();
 
  // initialize the serial communications:
  Serial.begin(9600);
  myservo.attach(10); //  定义舵机1的脚位
  myservo2.attach(9); //  定义舵机2的脚位
  pinMode(2, OUTPUT);       //定义无源蜂鸣器的脚位在数字2脚      
  pinMode(4, OUTPUT);       //定义有源蜂鸣器的脚位在数字4脚      
  pinMode(7, OUTPUT);       //定义继电器1的脚位在数字7脚      
  pinMode(8, OUTPUT);       //定义继电器2的脚位在数字8脚      
  pinMode(11, OUTPUT);      //定义继电器3的脚位在数字11脚      
  pinMode(12, OUTPUT);      //定义继电器4的脚位在数字12脚   

  pinMode(R_LED, OUTPUT);   //PWM 輸出腳位 6
  pinMode(G_LED, OUTPUT);   //PWM 輸出腳位 5
  pinMode(B_LED, OUTPUT);   //PWM 輸出腳位 3
  pinMode(W3w_LED, OUTPUT);
  
   lcd.clear();                  // 清除LCD畫面  
  lcd.setCursor(4, 0);          //設定顯示開始位置
  lcd.print("SET Mode");        //顯示內容
  lcd.setCursor(1, 1);          //設定顯示開始位置 
  lcd.print("=*KEYES-HOME*=");   //顯示內容
} 

/*============ Led  Mode  ============================
This mode is control LED on or off,use the cell-phone.
all you need to do , just type in the code
The code is;  
              "1O"  //LED 1 ON
              "1F"  //LED 1 OFF
              "2O"  //LED 2 ON
              "2F"  //LED 2 OFF
              "3O"  //LED 3 ON
              "3F"  //LED 3 OFF
              "4O"  //LED 4 ON
              "4F"  //LED 4 OFF
              "#"   // all LED OFF
              "/"   //jump out this mode
======================================================*/   //1
void Led_Mode()
{  
 
  lcd.clear();                                                                   
  lcd.setCursor(4, 0);
  lcd.print("Led Mode ");
  lcd.setCursor(0, 1); 
  lcd.print("Enter 1o~6o.#.@./");       
      while(key < 1)
        {
            if (Serial.available()) 
            {
              delay(100);
              switch(Serial.read())
                {
                  case '1':                               //第一個字元是1的話進入
                    if ( Serial.read() == 'o' ) digitalWrite(7, HIGH);         //判斷第二個字元是 o 就將燈開啟
                    else  digitalWrite(7, LOW);            //如果不是就將燈關閉  
                    break; 
                  case '2':                                                    //第一個字元是2的話進入
                    if ( Serial.read() == 'o' ) digitalWrite(8, HIGH);         //判斷第二個字元是 o 就將燈開啟
                    else  digitalWrite(8, LOW);              //如果不是就將燈關閉 
                    break;
                  case '3':                                //第一個字元是3的話進入
                    if ( Serial.read() == 'o' ) digitalWrite(11, HIGH);        //判斷第二個字元是 o 就將燈開啟
                    else  digitalWrite(11, LOW);           //如果不是就將燈關閉
                    break; 
                 case '4':                                //第一個字元是4的話進入
                    if ( Serial.read() == 'o' ) digitalWrite(12, HIGH);         //判斷第二個字元是 o 就將燈開啟
                    else  digitalWrite(12, LOW);           //如果不是就將燈關閉 
                    break;
                  
                    break; 
                 case '6':                                //第一個字元是6的話進入
                    if ( Serial.read() == 'o' )                           //判斷第二個字元是 o 就將開蜂鸣器
                    digitalWrite(4, HIGH); 
                    delay(1000);
                    digitalWrite(4, LOW);                //有源蜂鸣器响一秒，停一秒
                    delay(1000);
                    break; 
                  case '7':                              //第一個字元是7的話進入开有源蜂鸣器
                   
                   for (int thisNote = 0; thisNote < 93; thisNote++) 
                        {                    
                          tone(2, melody[thisNote], duration);                       
                          delay(120);
                        }
                    break;  
                  case '#':                              //第一個字元是#的話進入
                     digitalWrite(7, LOW);               //把所有的燈全部關閉
                     digitalWrite(8, LOW);
                     digitalWrite(11, LOW); 
                     digitalWrite(12, LOW);
                     digitalWrite(2, LOW); 
                     digitalWrite(4, LOW);    
                    break;
                    case'@':                        //第一個字元是#的話進入
                     digitalWrite(7, HIGH);          //把所有的燈全部關閉
                     digitalWrite(8, HIGH);
                     digitalWrite(11, HIGH); 
                     digitalWrite(12, HIGH);
                     digitalWrite(14, HIGH);                  
                     delay(1000);
                     digitalWrite(4, LOW);           //有源蜂鸣器响一秒，停一秒
                     delay(1000);
                     for (int thisNote = 0; thisNote < 93; thisNote++) 
                        {                    
                          tone(2, melody[thisNote], duration);                       
                          delay(120);
                        }
                    break;
                  case '/':                            //第一個字元是/的話進入
                     key = 2;                        //改變條件跳出迴圈
                     break;     
                }               
            }
        }
}
/*============ Led  PWM  Mode  ============================
This mode is control PWM ++ or -- or white light,use the cell-phone.
all you need to do , just type in the code
The code is;  
              "R+"  //R_LED  ++
              "G+"  //G_LED  ++
              "B+"  //B_LED  ++
              "W"   //white light ON             
              "#"   // all LED OFF
              "/"   //jump out this mode
======================================================*/   //2
void Led_PWM()
{ 
   
  lcd.clear();
  
    lcd.setCursor(2, 0);
    lcd.print("Led PWM Mode");
    lcd.setCursor(0, 1); // set LCD word position
    lcd.print(" r+.g+.b+.W.a.#./");
    while(key < 1)
      { 
            if (Serial.available()) 
              {
                delay(100);
                switch(Serial.read())
                  {
                    case'r':                                                              //第一個字元是R的話進入
                      value = value + 15;                                                    //值每次加15
                      if ( Serial.read() == '+' ) analogWrite(R_LED,value);                   //判斷第二個字元是 + 紅燈漸亮 
                      break; 
                    case'g':                                                                //第一個字元是G的話進入
                      value = value + 15;                                                    //值每次加15
                      if ( Serial.read() == '+' ) analogWrite(G_LED,value);                   //判斷第二個字元是 + 綠燈漸亮
                      break;
                    case'b':                                                                //第一個字元是B的話進入
                      value = value + 15;                                                    //值每次加15
                      if ( Serial.read() == '+' ) analogWrite(B_LED,value);                   //判斷第二個字元是 + 綠燈漸亮
                      break;
                    case'w':                                                                //第一個字元是B的話進入
                      value = value + 15;                                                    //值每次加15
                      if ( Serial.read() == '+' ) analogWrite(W3w_LED,value);                   //判斷第二個字元是 + 燈漸亮
                      break;      
                    case'a':                                                                 //第一個字元是W的話進入
                       analogWrite(R_LED,255);                                                //亮白光
                       analogWrite(G_LED,255);
                       analogWrite(B_LED,255); 
                      break;  
                    case'#':                                                                 //第一個字元是#的話進入
                       value = 0; 
                       analogWrite(R_LED,0);                                                  //關掉所有的燈
                       analogWrite(G_LED,0);
                       analogWrite(B_LED,0);
                       analogWrite(W3w_LED,0);
                      break;  
                    case'/':                                                                 //第一個字元是/的話進入
                       key = 2;                                                               //改變條件跳出迴圈
                       break;    
                  }               
              }  
      }   
}
/*============ Led PWM2 Mode  ============================
This mode is control RGB_LED in circle mode ,use the cell-phone.
all you need to do , just type in the code
The code is;  
              "U"  //R to G to B
              "D"  //B to G to R
              "#"   // all LED OFF
              "/"   //jump out this mode
======================================================*/   //3
void Led_PWM2()
{  
      lcd.clear();
    
      lcd.setCursor(2, 0);
      lcd.print("Led PWM Mode2");
      lcd.setCursor(2, 1); // set LCD word position
      lcd.print("Enter u.d.#./");
      while(key < 1)
        {
              if (Serial.available()) 
              {
                delay(100);
                switch(Serial.read())
                {
                  case 'u':                              //第一個字元是U的話進入           
                     Led_PWM2_Counter++;                                     //上循環   
                    break;
                  case 'd':                              //第一個字元是D的話進入
                     Led_PWM2_Counter--;               //下循環
                    break; 
                  case '#':                               //第一個字元是#的話進入
                     analogWrite(R_LED,0);              //關掉所有的燈
                     analogWrite(G_LED,0);
                     analogWrite(B_LED,0);
                    break;  
                  case '/':                               //第一個字元是/的話進入
                     key = 2;                            //改變條件跳出迴圈 
                    break;        
                }          
                switch( Led_PWM2_Counter % 4)             //除4取餘數
                {
                  case 0:                                  //整除 紅燈亮
                    analogWrite(R_LED,255);
                    analogWrite(G_LED,0);
                    analogWrite(G_LED,0);
                   break;
                  case 1:                                   //餘1  綠燈亮
                    analogWrite(R_LED,0);
                    analogWrite(G_LED,255);
                    analogWrite(B_LED,0);
                   break;
                  case 2:                                    //餘2  藍燈亮
                    analogWrite(R_LED,0);
                    analogWrite(G_LED,0);
                    analogWrite(B_LED,255);
                   break;
                  case 3:                                      //餘3 白光亮
                    analogWrite(R_LED,255);
                    analogWrite(G_LED,255);
                    analogWrite(B_LED,255);
                   break;
                }
              } 
        }
}
/*============         Text_Mode    ============================
This mode is control 1602_LCD  mode ,use the cell-phone.
all you need to do , just type in the code
The code is;  
              Type everything is will be show at LCD
              "/"   //jump out this mode
==============================================================*/   //4
void Text_Mode()
{
  lcd.clear();

  lcd.setCursor(4, 0);
  lcd.print("Text_Mode");
  lcd.setCursor(1, 1); // set LCD word position
  lcd.print("Enter Anything");

  while(key < 1)
    { 
        if (Serial.available()) {
        delay(100);
        lcd.clear();
        while (Serial.available() > 0) 
        {       
          lcd.write(Serial.read());          
        }
      }
      if ( Serial.read() == '/' ) key = 2 ;
    }
}
/*============        Pass_Code1    ============================
This mode is set Password Mode  ,use the cell-phone.
all you need to do , just type in the code
The code is;  
              set 4 Password in this mode,
              when LED show "Get it" that mean setting is done
              "/"   //jump out this mode
==============================================================*/   //5
void Enter_Pass_Code1()
{
     lcd.clear();
  
     lcd.setCursor(1, 0);
     lcd.print("Pass_Code1_Mode");
     delay(2000);
     lcd.setCursor(0, 1);   
     lcd.print("Set Code1 4 Numb");
     while(key < 1)
       {
         if (Pass_Key == 0)
            {
             if (Serial.available()) 
               {
                 delay(100);
                 if (Serial.available() > 0) 
                  {
                     for(int i = 0;i<4;i++)              //將輸入的密碼 存到陣列裡面
                      {
                        Pass1_buf[i] = Serial.read();
                      } 
                    lcd.clear();
                    lcd.setCursor(1, 0);
                    lcd.print("Now goto 6 Mode");
                    lcd.setCursor(4, 1); 
                    lcd.print("Get It"); 
                    Pass_Key = 1;      
                  }              
               }
             }
        if ( Serial.read() == '/' ) key = 2; //break;   
       }  
}
/*============  Password  Mode  ============================
This mode is type in password to open somethign ,use the cell-phone.
all you need to do , just type in the code
The code is;  
              type in 4 password ,the password is your setting
              when you setting in mode 5
              "/"   //jump out this mode
======================================================*/   //1
void PassCode1()
{
      lcd.clear();
 
      lcd.setCursor(2, 0);
      lcd.print("Enter Cade1");
      while(key < 1)
        {
          if(Pass_Key == 1)
            {
              if (Serial.available()) 
               {
                 delay(100);
                 if (Serial.available() > 0) 
                  {
                    for(int i = 0;i<4;i++)
                     { 
                       Pass2_buf[i] = Serial.read();                                  //把密碼2 存到陣列裡面
                      } 
                     lcd.clear();
                      
                     lcd.setCursor(4, 0); 
                     lcd.print("cheak");  
                     lcd.setCursor(2, 1);
                     lcd.print("Enter Again"); 
                    if ( Pass1_buf[0] == Pass2_buf[0] && Pass1_buf[1] == Pass2_buf[1] &&     //資料比對
                       Pass1_buf[2] == Pass2_buf[2] && Pass1_buf[3] == Pass2_buf[3]  )
                     {
                       lcd.clear();
                       lcd.setCursor(2, 0);
                       lcd.print(" / to Exit ");
                       lcd.setCursor(7, 1); 
                       lcd.print("OK");                 
                       Receive_LED();//閃led
                     }          
                  }              
               }
            }             
          if ( Serial.read() == '/' ) key = 2; //break;
        }   
}
/*============ Servo  Mode  ============================
This mode is control servo,use the cell-phone.
all you need to do , just type in the code
The code is;  
              "0"  // 0 degree
              "1"  // 20 degree
              "2"  // 40 degree
              "3"  // 60 degree
              "4"  // 80 degree
              "5"  // 100 degree
              "6"  // 120 degree
              "7"  // 140 degree
              "8"  // 160 degree
              "9"  // 180 degree
              "+"  // +5 degree
              "-"  // -5 degree
              "C"  // 90 degree
              "/"  // jump out this mode
======================================================*/   //7
void Sevro_Mode()
{
  lcd.clear();

  lcd.setCursor(3, 0);
  lcd.print("Servo_Mode");
  lcd.setCursor(0, 1); // set LCD word position
  lcd.print("Enter 0~9.+.-.C./");
  while(key < 1)
    {
     if (Serial.available()) 
       {
         delay(100);
          switch(Serial.read())
           {  
             case '0':                          //第一個字元是0的話進入
                 val = 0 * rate ;//0度 
                 sevro2();             
               break;
             case '1':                          //第一個字元是1的話進入
                 val = 1 * rate ;//20度
                 sevro2();  
               break;
             case '2':                           //第一個字元是2的話進入
                 val = 2 * rate ;//40度
                 sevro2();  
               break;
             case '3':                            //第一個字元是3的話進入
                 val = 3 * rate ;//60度
                 sevro2();  
               break;
             case '4':                            //第一個字元是4的話進入
                 val = 4 * rate ;//80度
                 sevro2();  
               break;
             case '5':                             //第一個字元是5的話進入
                val = 5 * rate ;//100度
                sevro2();  
               break;
             case '6':                              //第一個字元是6的話進入
                val = 6 * rate ;//120度
                sevro2();  
               break;
             case '7':                               //第一個字元是7的話進入
                val = 7 * rate ;//140度
                sevro2();  
               break;
             case '8':                               //第一個字元是8的話進入
                val = 8 * rate ;//160度
                sevro2();  
               break;
             case '9':                                //第一個字元是9的話進入
                val = 9 * rate ;//180度
                sevro2();  
               break;  
             case 'l':                                 //第一個字元是+的話進入
                 val = val + 5;//每次加5度                              
               break; 
             case 'r':                                 //第一個字元是-的話進入 
                 val = val - 5;//每次減5度
               break;
             case 'u':                                  //第一個字元是+的話進入
                 val2 = val2 + 5;//每次加5度                              
               break; 
             case 'd':                                   //第一個字元是-的話進入 
                 val2 = val2 - 5;//每次減5度
               break;               
             case 'c':                                   //第一個字元是C的話進入
                 val = 90;//回到九十度
                 val2 = 90;
               break; 
              case '/':                                    //第一個字元是/的話進入
                 key = 2;
                 break;              
           }
          myservo.write(val);                
          delay(15);
          myservo2.write(val2); 
          delay(15);    
       }  
    } 
}
/*=========== sevro2======================================
=========================================================*/
void sevro2()
{
   switch(Serial.read())
    {  
             case '0':                                                     //第一個字元是0的話進入
                 val2 = 0 * rate ;//0度              
               break;
             case '1':                                                     //第一個字元是1的話進入
                 val2 = 1 * rate ;//20度
               break;
             case '2':                                                     //第一個字元是2的話進入
                 val2 = 2 * rate ;//40度
               break;
             case '3':                                                     //第一個字元是3的話進入
                 val2 = 3 * rate ;//60度
               break;
             case '4':                                                     //第一個字元是4的話進入
                 val2 = 4 * rate ;//80度
               break;
             case '5':                                                     //第一個字元是5的話進入
                val2 = 5 * rate ;//100度
               break;
             case '6':                                                     //第一個字元是6的話進入
                val2 = 6 * rate ;//120度
               break;
             case '7':                                                     //第一個字元是7的話進入
                val2 = 7 * rate ;//140度
               break;
             case '8':                                                     //第一個字元是8的話進入
                val2 = 8 * rate ;//160度
               break;
             case '9':                                                     //第一個字元是9的話進入
                val2 = 9 * rate ;//180度
               break;
           } 
}
/*============ Led  Mode  =================================
Hi! you can design your code in here              
==========================================================*/   //8
void Serial_analog()
{  
  lcd.clear();

  lcd.setCursor(3, 0);
  lcd.print("Serial_analog");
  lcd.setCursor(0, 1); // set LCD word position
  lcd.print("Enter /");
  while(key < 1)
   { 
     sensorValue = analogRead(analogInPin0); // 读模拟传感器1的值
     sensorValue1 = analogRead(analogInPin1); // 读模拟传感器2的值
     sensorValue2 = analogRead(analogInPin2); // 读模拟传感器3的值
     sensorValue3 = analogRead(analogInPin3);  // 读模拟传感器4的值
     Serial.print("sensor1 = " ); 
     Serial.println(sensorValue );
     delay(1000);
     Serial.print("sensor2 = " );  
     Serial.println(sensorValue1  );
     delay(1000);
     Serial.print("sensor3 = " );            
     Serial.println(sensorValue2 );
     delay(1000);
     Serial.print("sensor4 = " );            
     Serial.println(sensorValue3 );
     delay(1000);

     switch(Serial.read())
      {
         case '/':                                                    //第一個字元是/的話進入
           key = 2;
           break; 
      }                     
   }  
}

/*============ Receive LED ===============================
Flash LED
==========================================================*/
void Receive_LED()//led閃爍
{ 
      digitalWrite(4, HIGH);
      delay(1000);
      digitalWrite(4, LOW);
      digitalWrite(13, HIGH);
      delay(3000);
      digitalWrite(13, LOW);  
    
      
}


//============   loop   ==========================//
void loop()
{
 if (Serial.available() > 0)
   {
      lcd.clear();



      lcd.setCursor(4, 0);
      lcd.print("SET Mode");
      lcd.setCursor(1, 1); // set LCD word position
      lcd.print("= KEYES team =");
      key = 0;
   }  
  switch(Serial.read())
   {
     case '1': //第一個字元是1的話進入   
           Led_Mode();
         break;  
     case '2': //第一個字元是2的話進入    
           Led_PWM();
         break;        
     case '3': //第一個字元是3的話進入  
           Led_PWM2();
         break;        
     case '4': //第一個字元是4的話進入 
           Text_Mode();
         break;      
     case '5': //第一個字元是5的話進入 
           Enter_Pass_Code1(); 
         break;           
     case '6': //第一個字元是6的話進入
           PassCode1();
         break;    
     case '7': //第一個字元是7的話進入  
           Sevro_Mode();
         break;  
     case '8':              //第一個字元是8的話進入       
           Serial_analog();
         break;  
   }     
}# -
