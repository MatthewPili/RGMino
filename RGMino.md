# RGMino
#include <GSM.h>
#define PINNUMBER ""
#include <LiquidCrystal.h>


GSM gsmAccess;
GSM_SMS sms;
const int rs = 11, en = 10, d4 = 9, d5 = 6, d6 = 5, d7 = 4;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

int ingresso = 2;
double litri;
volatile unsigned long  count;

char senderNumber[20];
bool acceso = false;
bool acceso_2=false;
char c;
//void (* Riavvia)(void)=0;


void setup()
{
  pinMode(ingresso, INPUT);
  attachInterrupt(0, impulso, RISING);
  Serial.begin(9600);
  pinMode(8, OUTPUT);
  pinMode(12,OUTPUT);
  pinMode(13,OUTPUT);
  //lcd.begin(16,2);
  //lcd.setCursor(0,0);
  lcd.print("Inizializzando");
  
  //Serial.println("RICEVITORE DI SMS ");
  boolean notConnected = true;

  while (notConnected)
  {
    if (gsmAccess.begin(PINNUMBER) == GSM_READY)
    {
      notConnected = false;
    }
    else
    {
      Serial.println("Connessione non riuscita");
      delay(1000);
    }
  }
  //Serial.println("GSM Inizializzato");

  char messaggio [20]="Inizializzato";
  //Serial.println("Invio del seguente messaggio:");
lcd.clear();
   //Serial.println("In attesa di mess");
   lcd.print("In attesa di sms");

 
}

void loop()
{
  char c;
  char mex[2];
  String ricevuto = "";
  int conta = 0;
  char messaggio;
  sms.remoteNumber(senderNumber, 20);
  if (sms.available())
  {
    while (c = sms.read()) 
    {
      //COMANDO DI ACCENSIONE
      if (c == '1')
      {
       //IL SISTEMA é GIà ACCESO
       if (acceso == true) 
       {
       lcd.clear();
         //Serial.println("Gia' acceso");
         lcd.print("Gia' acceso");
        //Serial.println("0->OFF ");
          delay(1000);
          break;
        }
       //ACCENSIONE DEL SISTEMA 
       else
       {
          lcd.clear();
          lcd.print("Accensione");
         //Serial.println("Accensione...");
          digitalWrite(8, HIGH);
          delay(1000);
          digitalWrite(12,HIGH);
          
             lcd.clear();
             lcd.print("Acceso.");
     
        // Serial.println("Acceso.");

             acceso = true;
             interrupts();
             delay(3000);
             litri= count / 630;
             sendSMS();
             delay(1000);
          
          break;
        }
       }
      //COMANDO DI SPEGNIMENTO
        else if (c == '0')
      { 
       //SPEGNIMENTO DEL SISTEMA
       if (acceso == true)
       { lcd.clear();
       lcd.print("Spegnimento.");
         //Serial.println("Spegnimento...");
          digitalWrite(12,LOW);
          delay(1000);
          digitalWrite(8, LOW);
          lcd.clear();
          lcd.print("Spento");
          //Serial.println("Spento.");
          acceso = false;
          sendSMS();
         
          break;
        }
       //IL SISTEMA è GIà SPENTO 
       else 
      {lcd.clear();
        lcd.print("Gia' spento");
        delay(2000);
        lcd.clear();
        lcd.print("In attesa di sms");
        
         //Serial.println("Gia spento.");
         //Serial.println("1->ON");
        }
      }
     
    
      //COMANDO NON VALIDO
      else if ( c != '0' && c != '1')  
      {lcd.clear();
      lcd.print("ERRORE");
      delay(2000);
        lcd.clear();
        lcd.print("In attesa di sms");
        
       //Serial.println("ERRORE");

        break;
      }
    }
      sms.flush();
  }
   sms.flush();
}  

void impulso(){
  
  count = count +1;
 
}

void sendSMS(){
   String a="Sono stati erogati ";
   char remoteNumbers[20]=" **********";
   Serial.print("Messaggio dal numero: ");
   Serial.println(remoteNumbers);
      if(count == 0){
        Serial.println("Ora invia questo messaggio: ");
        char txtMsg[200]="Laghe non è uscita aqua";
        Serial.println(txtMsg);
        sms.beginSMS(remoteNumbers);
        sms.print(txtMsg);
        sms.endSMS();
         Serial.println("Il messaggio è stato inviato!");
      }else if(litri> 0,5)
        {
           a= a + litri;
           Serial.println("Ora invia questo messaggio: ");
           sms.beginSMS(remoteNumbers);
           sms.print(a);
           sms.endSMS();
           Serial.println(a);
           Serial.println("Il messaggio è stato inviato!");
        }
}

  
  
 

