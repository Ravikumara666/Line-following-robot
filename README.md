<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

 <link rel="stylesheet" href="styles.css">
</head>

<body>
    <header>
        <h1>Line Following Robot with Arduino</h1>
        <p>An autonomous robot that follows a predefined path using IR sensors and detects obstacles using ultrasonic sensors.</p>
    </header>

   <section id="overview">
        <h2>Project Overview</h2>
        <p>This project demonstrates a line-following robot built using an Arduino Uno, IR sensors, DC motors, and an ultrasonic sensor. The robot can detect and follow a black line on a white surface and avoid obstacles in its path.</p>
    </section>

   <section id="problem-statement">
        <h2>Problem Statement</h2>
        <p>The need for automated systems in industries and educational environments is growing. Line-following robots are an ideal solution for repetitive tasks, increasing accuracy, and reducing manual intervention.</p>
    </section>

   <section id="working">
        <h2>How It Works</h2>
        <ul>
            <li>The robot uses **IR sensors** to detect the line.</li>
            <li>The **ultrasonic sensor** detects obstacles in its path, causing the robot to stop and activate a buzzer.</li>
            <li>**Motors** drive the robot, which can move forward, turn left, or turn right based on the sensor input.</li>
            <li>LED indicators show the current direction of movement.</li>
        </ul>
    </section>

   <section id="components">
        <h2>Components Used</h2>
        <ul>
            <li>Arduino Uno</li>
            <li>2 IR Sensors</li>
            <li>Ultrasonic Sensor (HC-SR04)</li>
            <li>2 DC Motors with Motor Driver</li>
            <li>LEDs and Buzzer</li>
            <li>Battery Pack</li>
        </ul>
    </section>

  <section id="industry-usage">
        <h2>Industry Usage</h2>
        <p>Line-following robots are used in various industries for:</p>
        <ul>
            <li>Automated material transportation in warehouses.</li>
            <li>Assembly line automation to reduce human error.</li>
            <li>Educational projects to teach robotics and automation concepts.</li>
            <li>Precision tasks that require accurate path following.</li>
        </ul>
    </section>

  <section id="code">
        <h2>Arduino Code</h2>
        <pre>
            <code>
               // Define motor control pins
const int motorLeftForward = 3;  // Left motor forward
const int motorLeftBackward = 4; // Left motor backward
const int motorRightForward = 5; // Right motor forward
const int motorRightBackward = 6; // Right motor backward

// Define motor speed pins
const int motorLeftSpeed = 9;    // PWM pin for left motor speed
const int motorRightSpeed = 10;  // PWM pin for right motor speed

// Define IR sensor pins
const int irSensorLeft = A0;     // Left IR sensor
const int irSensorRight = A1;    // Right IR sensor

// Define Ultrasonic sensor pins
const int trigPin = 7;           // Trigger pin for ultrasonic sensor
const int echoPin = 8;           // Echo pin for ultrasonic sensor

// Define LED and Buzzer pins
const int ledForward = 11;       // LED for forward
const int ledLeft = 12;          // LED for left turn
const int ledRight = 13;         // LED for right turn
const int buzzer = 2;            // Buzzer for alerts

void setup() {
  // Set motor pins as outputs
  pinMode(motorLeftForward, OUTPUT);
  pinMode(motorLeftBackward, OUTPUT);
  pinMode(motorRightForward, OUTPUT);
  pinMode(motorRightBackward, OUTPUT);
  pinMode(motorLeftSpeed, OUTPUT);
  pinMode(motorRightSpeed, OUTPUT);

  // Set IR sensor pins as inputs
  pinMode(irSensorLeft, INPUT);
  pinMode(irSensorRight, INPUT);

  // Set ultrasonic sensor pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Set LED and buzzer pins as outputs
  pinMode(ledForward, OUTPUT);
  pinMode(ledLeft, OUTPUT);
  pinMode(ledRight, OUTPUT);
  pinMode(buzzer, OUTPUT);

  // Start Serial Monitor for debugging
  Serial.begin(9600);
}

void loop() {
  // Read IR sensor values
  int leftSensorValue = digitalRead(irSensorLeft);
  int rightSensorValue = digitalRead(irSensorRight);

  // Check for obstacles
  long distance = measureDistance();

  if (distance < 15) { // If an obstacle is detected within 15 cm
    stopMotors();
    digitalWrite(buzzer, HIGH); // Turn on buzzer
    delay(500);
    digitalWrite(buzzer, LOW);  // Turn off buzzer
  } else {
    // Line-following logic
    if (leftSensorValue == HIGH && rightSensorValue == HIGH) {
      moveForward();
      indicateDirection("forward");
    } else if (leftSensorValue == HIGH && rightSensorValue == LOW) {
      turnLeft();
      indicateDirection("left");
    } else if (leftSensorValue == LOW && rightSensorValue == HIGH) {
      turnRight();
      indicateDirection("right");
    } else {
      stopMotors();
      indicateDirection("stop");
    }
  }

  delay(10); // Small delay for stability
}

// Function to measure distance using ultrasonic sensor
long measureDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  long distance = duration * 0.034 / 2; // Convert to cm
  return distance;
}

// Functions to control the motors
void moveForward() {
  analogWrite(motorLeftSpeed, 200);  // Set speed (0-255)
  analogWrite(motorRightSpeed, 200);
  digitalWrite(motorLeftForward, HIGH);
  digitalWrite(motorLeftBackward, LOW);
  digitalWrite(motorRightForward, HIGH);
  digitalWrite(motorRightBackward, LOW);
}

void turnLeft() {
  analogWrite(motorLeftSpeed, 150);  // Reduce left motor speed for a smoother turn
  analogWrite(motorRightSpeed, 200);
  digitalWrite(motorLeftForward, LOW);
  digitalWrite(motorLeftBackward, LOW);
  digitalWrite(motorRightForward, HIGH);
  digitalWrite(motorRightBackward, LOW);
}

void turnRight() {
  analogWrite(motorLeftSpeed, 200);
  analogWrite(motorRightSpeed, 150);  // Reduce right motor speed for a smoother turn
  digitalWrite(motorLeftForward, HIGH);
  digitalWrite(motorLeftBackward, LOW);
  digitalWrite(motorRightForward, LOW);
  digitalWrite(motorRightBackward, LOW);
}

void stopMotors() {
  analogWrite(motorLeftSpeed, 0);
  analogWrite(motorRightSpeed, 0);
  digitalWrite(motorLeftForward, LOW);
  digitalWrite(motorLeftBackward, LOW);
  digitalWrite(motorRightForward, LOW);
  digitalWrite(motorRightBackward, LOW);
}

// Function to indicate direction using LEDs
void indicateDirection(String direction) {
  if (direction == "forward") {
    digitalWrite(ledForward, HIGH);
    digitalWrite(ledLeft, LOW);
    digitalWrite(ledRight, LOW);
  } else if (direction == "left") {
    digitalWrite(ledForward, LOW);
    digitalWrite(ledLeft, HIGH);
    digitalWrite(ledRight, LOW);
  } else if (direction == "right") {
    digitalWrite(ledForward, LOW);
    digitalWrite(ledLeft, LOW);
    digitalWrite(ledRight, HIGH);
  } else { // Stop
    digitalWrite(ledForward, LOW);
    digitalWrite(ledLeft, LOW);
    digitalWrite(ledRight, LOW);
  }
}
            </code>
        </pre>
    </section>

  <footer>
        <p>&copy; 2025 Line Following Robot Project</p>
    </footer>
</body>

</html>
