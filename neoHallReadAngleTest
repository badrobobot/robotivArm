 #include <Wire.h>
 int ADXLAddress = 0x36; // Device address in which is also included the 8th bit for selecting the mode, read in this case.
 #define Angle_Register_DATAX0 0x0F // Hexadecima address for the DATAX0 internal register.
 #define Angle_Register_DATAX1 0x0E // Hexadecima address for the DATAX1 internal register.
 #define Raw_Angle_Register_DATAX0 0x0C
 #define Raw_Angle_Register_DATAX1 0x0D
 #define Power_Register 0x2D // Power Control Register
 
 int ANGLE0,ANGLE1,ANGLE;
 float DEG;
 volatile int origin = 0;
 
 // Defines pins numbers for Driver
const int stepPin = 3;
const int dirPin = 4; 
volatile int turn = true; 

// Variables for Serial communication
String inputString = "";         // a String to hold incoming data
boolean stringComplete = false; // whether the string is complete
String numberString = ""; // a String to hold incoming numbers

 void setup() {
      Wire.begin(); // Initiate the Wire library
      Serial.begin(9600);
      delay(100);
      // Enable measurement
      //Wire.beginTransmission(ADXLAddress);
      //Wire.write(Power_Register);
      // Bit D3 High for measuring enable (0000 1000)
      //Wire.write(8);  
      //Wire.endTransmission();
      pinMode(stepPin,OUTPUT); 
      pinMode(dirPin,OUTPUT);
      // Set origin
      setOrigin();
} 

void loop() {
  if(stringComplete){
    if(inputString=="Speed "){
      
    }else if(inputString=="MV "){
      int pasos = numberString.toInt();
      moveStepper(pasos,turn);
    } else if(inputString=="TURN "){
      turn = numberString.toInt();   
    } else {
      Serial.print("Command Not Found ");
    }
  // Clear serial communication varibles
  inputString = "";
  numberString = "";
  stringComplete = false;
  }
  
  
  readAngle();
  printAll();
}


void setOrigin(){
  Wire.beginTransmission(ADXLAddress); // Begin transmission to the Sensor 
      //Ask the particular registers for data
      //Wire.write(Angle_Register_DATAX0);
      //Wire.write(Angle_Register_DATAX1);     
      Wire.write(Raw_Angle_Register_DATAX0);      
      Wire.endTransmission(false); // Ends the transmission and transmits the data from the two registers
      
      Wire.requestFrom(ADXLAddress,2); // Request the transmitted two bytes from the two registers
      if(Wire.available()<=2) {  // 
        ANGLE0 = Wire.read() & 0x0F;
        ANGLE1 = Wire.read();
         ANGLE = (((ANGLE0<<8)| ANGLE1 ) - origin + 4095)%4095; // Reads the data from the register
        origin = ANGLE;
      }     
}

float readAngle(){
    Wire.beginTransmission(ADXLAddress); // Begin transmission to the Sensor 
      //Ask the particular registers for data  
      Wire.write(Raw_Angle_Register_DATAX0);      
      Wire.endTransmission(false); // Ends the transmission and transmits the data from the two registers
      
      Wire.requestFrom(ADXLAddress,2); // Request the transmitted two bytes from the two registers
      if(Wire.available()<=2) {  // 
        ANGLE0 = Wire.read() & 0x0F;
        ANGLE1 = Wire.read();
        ANGLE = (((ANGLE0<<8)| ANGLE1 ) - origin + 4095)%4095; // Reads the data from the register
        //ANGLE = ((ANGLE - origin + 4095) % 4095) ;
        DEG = (float)ANGLE/4095.0*360.0;
      }
  return DEG;
}
void moveStepper(int pasos, boolean sentido){
    // clear the string:
    if(sentido==1){
      digitalWrite(dirPin,HIGH);
    }else{
      digitalWrite(dirPin,LOW);
    }
    // Move steps 
    for(int x = 0; x < pasos; x++) {
      digitalWrite(stepPin,HIGH);  
      delayMicroseconds(500); 
      digitalWrite(stepPin,LOW); 
      delayMicroseconds(500); 
    }
  delay(1000); // One second delay
}

void printAll(){
      Serial.print("ANGLE0= ");
      Serial.print(ANGLE0);
      Serial.print("   ANGLE1= ");
      Serial.print(ANGLE1);
      Serial.print("   ANGLE= ");
      Serial.print(ANGLE);
      Serial.print("        DEG ");
      Serial.println(DEG);  
}


void serialEvent() {
  while (Serial.available()) {
    // get the new byte:
    char inChar = (char)Serial.read();
    // if the incoming character is a newline, set a flag so the main loop can
    // do something about it:
    if (inChar == '\n') {
      stringComplete = true;
    }else{
      if(isDigit(inChar) || inChar=='-'){
        numberString += inChar; 
      }else{
        inputString += inChar;
        if(inChar==' '){
          //espacios++;
        } 
      }
    }
  }
}

