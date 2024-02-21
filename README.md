# Lab Report 6 - Moving with Purpose: Sensing and Mobility
Eli Barrow & Isaac Stevens

Feb. 22, 2024

## Summary of Lab ##


### Lab Objectives: ###
1. Familiarize ourselves with the use of an ultrasonic sensor in a distance-measuring
application.   
2. Understand the use of the Motor Driver and program an algorithm to control the
movement of two motors.
3. Develop a program to prevent your robot from colliding with an object in front of it.   


## Lab Assignment Specific Items ##

•	A Computer running Arduino IDE

•	SparkFun Inventor’s kit

-	RedBoard

-	Ultrasonic sensor

-	Two motors

-	Motor Driver


## Methods and Testing ##
### Part 1 - Learn to Listen ###
We began this part of the lab by connecting our RedBoard to the computer and starting up the Arduino IDE application on our computer.  
After this step, we connected the ultrasonic sensor HC-SR04 to the circuit below.

<p align="center">
  <img src= https://github.com/elibarrow/BAE305-SP24-LAB6/blob/main/Screenshot%202024-02-19%20at%202.32.04%20PM.png width = 50%> 
</p> 

Figure 1. Connection of the HC-SR04 Ultrasonic Sensor to the RedBoard. ONLY USE THE UPPER HALF OF THIS IMAGE SHOWN IN THE LINK. We do not use the RGB LED shown in the image.         
https://cdn.sparkfun.com/assets/learn_tutorials/6/3/6/SIK_Circuit_3B.png?_gl=1*1jhoxae*_ga*MjAzMzk5NTE4NS4xNzA0NzUyMjY0*_ga_T369JS7J9N*MTcwODM3NTA5OS43LjEuMTcwODM3NTE4OC40MS4wLjA.




We then programmed an algorithm to read the distance from the sensor to a clear box, and send the value to the serial communications display in the Arduino IDE application. The code for that step is shown below.

```c++
const int trigPin = 2;  
const int echoPin = 3; 

float duration, distance;


void setup() {
  // put your setup code here, to run once:

	pinMode(trigPin, OUTPUT);  
	pinMode(echoPin, INPUT);  
	Serial.begin(9600);  
}

void loop() {
  // put your main code here, to run repeatedly:

digitalWrite(trigPin, LOW);  
	delayMicroseconds(2);  
	digitalWrite(trigPin, HIGH);  
	delayMicroseconds(10);  
	digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);

  distance = (duration*.0343)/2;


  Serial.print("Distance: ");  
	Serial.println(distance);  
	delay(100); 

}
```
NOTE: This code was previously written by someone else and edited by our group during the lab. The link to the website, where the code can be found is shown below.   
https://projecthub.arduino.cc/Isaac100/getting-started-with-the-hc-sr04-ultrasonic-sensor-7cabe1




### Part 2 ###


To begin part 2 we moved the ultrasonic sensor connections on the RedBoard to pins 6 & 7. We then connected the RedBoard, the Motor Driver, and the Motors. The circuit that we constructed is shown below.
NOTE: we did not include the sliding switch circuit in the diagram below.

<p align="center">
  <img src= https://github.com/elibarrow/BAE305-SP24-LAB6/blob/main/Screenshot%202024-02-19%20at%202.56.18%20PM.png width = 50%> 
</p> 

Figure 2. Two Motor Connection   
(https://learn.sparkfun.com/tutorials/sparkfun-inventors-kit-experiment-guide---v40/circuit-5b-remote-controlled-robot)

The next step was to insert code that had previously been written to control both of the motors. This code was written to have the user insert which way to move the robot; left, right, forwards or backwards, then followed by the distance for it to travel. The code that was given is shown below.   
The proper citations for the code is shown in the first few lines.


```c++
/*
  SparkFun Inventor’s Kit
  Circuit 5B - Remote Control Robot

  Control a two wheeled robot by sending direction commands through the serial monitor.
  This sketch was adapted from one of the activities in the SparkFun Guide to Arduino.
  Check out the rest of the book at
  https://www.sparkfun.com/products/14326

  This sketch was written by SparkFun Electronics, with lots of help from the Arduino community.
  This code is completely free for any use.

  View circuit diagram and instructions at: https://learn.sparkfun.com/tutorials/sparkfun-inventors-kit-experiment-guide---v40
  Download drawings and code at: https://github.com/sparkfun/SIK-Guide-Code
*/


//the right motor will be controlled by the motor A pins on the motor driver
const int AIN1 = 13;           //control pin 1 on the motor driver for the right motor
const int AIN2 = 12;            //control pin 2 on the motor driver for the right motor
const int PWMA = 11;            //speed control pin on the motor driver for the right motor

//the left motor will be controlled by the motor B pins on the motor driver
const int PWMB = 10;           //speed control pin on the motor driver for the left motor
const int BIN2 = 9;           //control pin 2 on the motor driver for the left motor
const int BIN1 = 8;           //control pin 1 on the motor driver for the left motor

int switchPin = 7;             //switch to turn the robot on and off

const int driveTime = 20;      //this is the number of milliseconds that it takes the robot to drive 1 inch
                               //it is set so that if you tell the robot to drive forward 25 units, the robot drives about 25 inches

const int turnTime = 8;        //this is the number of milliseconds that it takes to turn the robot 1 degree
                               //it is set so that if you tell the robot to turn right 90 units, the robot turns about 90 degrees

                               //Note: these numbers will vary a little bit based on how you mount your motors, the friction of the
                               //surface that your driving on, and fluctuations in the power to the motors.
                               //You can change the driveTime and turnTime to make them more accurate

String botDirection;           //the direction that the robot will drive in (this change which direction the two motors spin in)
String distance;               //the distance to travel in each direction

/********************************************************************************/
void setup()
{
  pinMode(switchPin, INPUT_PULLUP);   //set this as a pullup to sense whether the switch is flipped

  //set the motor control pins as outputs
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);
  pinMode(PWMA, OUTPUT);

  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);
  pinMode(PWMB, OUTPUT);

  Serial.begin(9600);           //begin serial communication with the computer

  //prompt the user to enter a command
  Serial.println("Enter a direction followed by a distance.");
  Serial.println("f = forward, b = backward, r = turn right, l = turn left");
  Serial.println("Example command: f 50");
}

/********************************************************************************/
void loop()
{
  if (digitalRead(7) == LOW)
  {                                                     //if the switch is in the ON position
    if (Serial.available() > 0)                         //if the user has sent a command to the RedBoard
    {
      botDirection = Serial.readStringUntil(' ');       //read the characters in the command until you reach the first space
      distance = Serial.readStringUntil(' ');           //read the characters in the command until you reach the second space

      //print the command that was just received in the serial monitor
      Serial.print(botDirection);
      Serial.print(" ");
      Serial.println(distance.toInt());

      if (botDirection == "f")                         //if the entered direction is forward
      {
        rightMotor(200);                                //drive the right wheel forward
        leftMotor(200);                                 //drive the left wheel forward
        delay(driveTime * distance.toInt());            //drive the motors long enough travel the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "b")                    //if the entered direction is backward
      {
        rightMotor(-200);                               //drive the right wheel forward
        leftMotor(-200);                                //drive the left wheel forward
        delay(driveTime * distance.toInt());            //drive the motors long enough travel the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "r")                     //if the entered direction is right
      {
        rightMotor(-200);                               //drive the right wheel forward
        leftMotor(255);                                 //drive the left wheel forward
        delay(turnTime * distance.toInt());             //drive the motors long enough turn the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "l")                   //if the entered direction is left
      {
        rightMotor(255);                                //drive the right wheel forward
        leftMotor(-200);                                //drive the left wheel forward
        delay(turnTime * distance.toInt());             //drive the motors long enough turn the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
    }
  }
  else
  {
    rightMotor(0);                                  //turn the right motor off
    leftMotor(0);                                   //turn the left motor off
  }
}
/********************************************************************************/
void rightMotor(int motorSpeed)                       //function for driving the right motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(AIN1, HIGH);                         //set pin 1 to high
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMA, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}

/********************************************************************************/
void leftMotor(int motorSpeed)                        //function for driving the left motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(BIN1, HIGH);                         //set pin 1 to high
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMB, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}
```

After assuring the code that we inserted from online worked, we then edited that code to be able to insert commands into the serial port to move both motors at 3 different speeds; slow, medium and fast.     
NOTE:  The code shown for this step will be shown a few lines below to reduce the amount of space taken up in our report, it has this step as well as the following steps included, and it is based on the code above but has been edited to perfrom a new task.

We then verified that it would perform as expected by moving the robot forwards, backwards, left and right at those 3 different levels of speed.

The next and final step was to insert the code from part 1 to make the motors stop when the distance measured is less than 10cm. The code shown for this step and the step above is shown below. The link to the original code can be found in the code snippet in the above images.

```c++


//the right motor will be controlled by the motor A pins on the motor driver
const int AIN1 = 13;           //control pin 1 on the motor driver for the right motor
const int AIN2 = 12;            //control pin 2 on the motor driver for the right motor
const int PWMA = 11;            //speed control pin on the motor driver for the right motor

//the left motor will be controlled by the motor B pins on the motor driver
const int PWMB = 10;           //speed control pin on the motor driver for the left motor
const int BIN2 = 9;           //control pin 2 on the motor driver for the left motor
const int BIN1 = 8;           //control pin 1 on the motor driver for the left motor

int switchPin = 7;             //switch to turn the robot on and off

const int driveTime = 20;      //this is the number of milliseconds that it takes the robot to drive 1 inch
                               //it is set so that if you tell the robot to drive forward 25 units, the robot drives about 25 inches

const int turnTime = 8;        //this is the number of milliseconds that it takes to turn the robot 1 degree
                               //it is set so that if you tell the robot to turn right 90 units, the robot turns about 90 degrees

                               //Note: these numbers will vary a little bit based on how you mount your motors, the friction of the
                               //surface that your driving on, and fluctuations in the power to the motors.
                               //You can change the driveTime and turnTime to make them more accurate

String botDirection;           //the direction that the robot will drive in (this change which direction the two motors spin in)
String speed;               //the speed to travel in each direction
int motorSpeed = 0;
int stop = 0;

const int trigPin = 2;  
const int echoPin = 3; 

float duration, distance; 

/********************************************************************************/
void setup()
{
  pinMode(switchPin, INPUT_PULLUP);   //set this as a pullup to sense whether the switch is flipped

  //set the motor control pins as outputs
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);
  pinMode(PWMA, OUTPUT);

  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);
  pinMode(PWMB, OUTPUT);

  pinMode(trigPin, OUTPUT);  
	pinMode(echoPin, INPUT);  

  Serial.begin(9600);           //begin serial communication with the computer

  //prompt the user to enter a command
  Serial.println("Enter a direction followed by a speed.");
  Serial.println("f = forward, b = backward, r = turn right, l = turn left");
  Serial.println("Example command: f 50");
}

/********************************************************************************/
void loop()
{

digitalWrite(trigPin, LOW);  
	delayMicroseconds(2);  
	digitalWrite(trigPin, HIGH);  
	delayMicroseconds(10);  
	digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);

  distance = (duration*.0343)/2;


  //Serial.print("Distance: ");  
	//Serial.println(distance);  
	delay(1000); 

  if (distance >= 10)
  {                                                     //if the switch is in the ON position
    if (Serial.available() > 0)                         //if the user has sent a command to the RedBoard
    {
      botDirection = Serial.readStringUntil(' ');       //read the characters in the command until you reach the first space
      speed = Serial.readStringUntil(' ');           //read the characters in the command until you reach the second space
      motorSpeed = speed.toInt();

      //print the command that was just received in the serial monitor
      Serial.print(botDirection);
      Serial.print(" ");
  
      Serial.println(motorSpeed);

      if (botDirection == "f")                         //if the entered direction is forward
      {
        rightMotor(motorSpeed);                                //drive the right wheel forward
        leftMotor(motorSpeed);                                 //drive the left wheel forward
        //delay( 100000);            //drive the motors long enough travel the entered speed
        //rightMotor(0);                                  //turn the right motor off
        //leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "b")                    //if the entered direction is backward
      {
        rightMotor(-motorSpeed);                               //drive the right wheel forward
        leftMotor(-motorSpeed);                                //drive the left wheel forward
       // delay(driveTime * speed.toInt());            //drive the motors long enough travel the entered speed
        //rightMotor(0);                                  //turn the right motor off
        //leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "r")                     //if the entered direction is right
      {
        rightMotor(-motorSpeed);                               //drive the right wheel forward
        leftMotor(255);                                 //drive the left wheel forward
        //delay(turnTime * speed.toInt());             //drive the motors long enough turn the entered speed
       //rightMotor(0);                                  //turn the right motor off
       // leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "l")                   //if the entered direction is left
      {
        rightMotor(255);                                //drive the right wheel forward
        leftMotor(-motorSpeed);                                //drive the left wheel forward
        //delay(turnTime * speed.toInt());             //drive the motors long enough turn the entered speed
       // rightMotor(0);                                  //turn the right motor off
       // leftMotor(0);                                   //turn the left motor off
      }
      
      else if (botDirection == "s")                   //if the entered direction is left
      {
        rightMotor(0);                                //drive the right wheel forward
        leftMotor(0);                                //drive the left wheel forward
        //delay(turnTime * speed.toInt());             //drive the motors long enough turn the entered speed
       // rightMotor(0);                                  //turn the right motor off
       // leftMotor(0);                                   //turn the left motor off
      }
    }
  }
  else
  {
    rightMotor(0);                                  //turn the right motor off
    leftMotor(0);                                   //turn the left motor off
  }
}
/********************************************************************************/
void rightMotor(int motorSpeed)                       //function for driving the right motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(AIN1, HIGH);                         //set pin 1 to high
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMA, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}

/********************************************************************************/
void leftMotor(int motorSpeed)                        //function for driving the left motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(BIN1, HIGH);                         //set pin 1 to high
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMB, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}

```
 After completing this part of the lab we then assured the code ran the robot as expected and proceeded to demostrate this to the instructor.


## Results ##


**Part 1**
In part 1 of the lab, when we were using the HC-SR04 sensor, we used the ruler to test the algorithm that we wrote to answer the following questions.

1. What is the resolution of this sensing system?    
   The resolution of this sensing system is 0.1mm.


2. Try to move your obstacle by a millimeter and determine qualitatively how precise it is.    
Actual precision is given by the following equation:  340 m/s * (12 &mu;s) = 4.08mm precision     
The system is precise when the object is not moving but may have random jumps from time to time. The sensor's accuracy is finicky and should be verified with a ruler.


**Part 2**


$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$


## Discussion Questions ##

**Part 2**

1. What is the minimum speed number for the motors to move forward?     
The minimum speed number for the motors to move forward is one, however, due to traction on surfaces, to physically move an object the speed would need to be higher.



## Conclusion of Lab 4 ##

$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$


