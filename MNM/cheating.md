Here are all 10 sketches inline so you can copy them directly.

## 1. Keypad → Servo (Set E, SET-A Q1)
```cpp
#include <Keypad.h>
#include <Servo.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};

Keypad kpd = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

Servo myServo;
const int servoPin = 10;

void setup() {
  Serial.begin(9600);
  myServo.attach(servoPin);
  myServo.write(0);
}

void loop() {
  char key = kpd.getKey();
  if (key) {
    switch (key) {
      case '1':
        myServo.write(90);
        Serial.println("Servo rotated 90 degrees");
        break;
      case '2':
        myServo.write(180);
        Serial.println("Servo rotated 180 degrees");
        break;
      case '3':
        myServo.write(0);
        Serial.println("Servo reset");
        break;
    }
  }
}
```

## 2. Keypad → 3 LEDs (Set E, SET-B Q2)
```cpp
#include <Keypad.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};

Keypad kpd = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

const int led1 = 10;
const int led2 = 11;
const int led3 = 12;

void setup() {
  Serial.begin(9600);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
}

void loop() {
  char key = kpd.getKey();
  if (key) {
    switch (key) {
      case '1': digitalWrite(led1, HIGH); Serial.println("LED1 ON"); break;
      case '4': digitalWrite(led1, LOW);  Serial.println("LED1 OFF"); break;
      case '2': digitalWrite(led2, HIGH); Serial.println("LED2 ON"); break;
      case '5': digitalWrite(led2, LOW);  Serial.println("LED2 OFF"); break;
      case '3': digitalWrite(led3, HIGH); Serial.println("LED3 ON"); break;
      case '6': digitalWrite(led3, LOW);  Serial.println("LED3 OFF"); break;
    }
  }
}
```

## 3. Servo sweep — inspection gate (SET-A Q1)
```cpp
#include <Servo.h>

Servo myServo;
const int servoPin = 9;

void setup() {
  Serial.begin(9600);
  myServo.attach(servoPin);
}

void loop() {
  for (int angle = 20; angle <= 180; angle++) {
    myServo.write(angle);
    Serial.print("Rotating clockwise: ");
    Serial.println(angle);
    delay(15);
  }
  for (int angle = 180; angle >= 20; angle--) {
    myServo.write(angle);
    Serial.print("Rotating anticlockwise: ");
    Serial.println(angle);
    delay(15);
  }
}
```

## 4. Ultrasonic (3-pin) → LED (SET-B Q2, parking)
```cpp
const int sigPin = 9;
const int ledPin = 8;

long duration;
int distance;

void setup() {
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  pinMode(sigPin, OUTPUT);
  digitalWrite(sigPin, LOW);
  delay(2);
  digitalWrite(sigPin, HIGH);
  delay(10);
  digitalWrite(sigPin, LOW);

  pinMode(sigPin, INPUT);
  duration = pulseIn(sigPin, HIGH);
  distance = (duration * 0.034) / 2;

  Serial.print("Distance(cm): ");
  Serial.println(distance);

  if (distance < 40) {
    digitalWrite(ledPin, HIGH);
  } else {
    digitalWrite(ledPin, LOW);
  }

  delay(500);
}
```

## 5. Servo gate open/close (online notes, Set 1)
```cpp
#include <Servo.h>

Servo myServo;
const int servoPin = 9;
const int gatePin = 8;   // LED representing the entry gate

void setup() {
  Serial.begin(9600);
  myServo.attach(servoPin);
  pinMode(gatePin, OUTPUT);
}

void loop() {
  for (int angle = 90; angle <= 180; angle++) {
    myServo.write(angle);

    if (angle >= 170) {
      digitalWrite(gatePin, HIGH);
      Serial.print("Entry OPEN, angle: ");
      Serial.println(angle);
    } else {
      digitalWrite(gatePin, LOW);
      Serial.print("Entry CLOSE, angle: ");
      Serial.println(angle);
    }

    if (angle == 180) {
      delay(2000);
    }
    delay(15);
  }
}
```

## 6. PIR + servo "Welcome" (online notes, Set 2)
```cpp
#include <Servo.h>

const int pirPin = 2;
Servo myServo;
const int servoPin = 9;

int pirStat = 0;

void setup() {
  Serial.begin(9600);
  pinMode(pirPin, INPUT);
  myServo.attach(servoPin);
  myServo.write(0);
}

void loop() {
  pirStat = digitalRead(pirPin);

  if (pirStat == HIGH) {
    Serial.println("Welcome");
    myServo.write(180);
  } else {
    myServo.write(0);
  }

  delay(1000);
}
```

## 7. Keypad sequence capture (Set A chat note)
```cpp
#include <Keypad.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};

Keypad kpd = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

String sequence = "";
const char specialChar = '#';

void setup() {
  Serial.begin(9600);
}

void loop() {
  char key = kpd.getKey();
  if (key) {
    if (key == specialChar) {
      Serial.print("Sequence entered: ");
      Serial.println(sequence);
      sequence = "";
    } else {
      sequence += key;
    }
  }
}
```

## 8. Keypad security code "1234" (Set D, SET-B Q2)
```cpp
#include <Keypad.h>

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};

Keypad kpd = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

const String correctCode = "1234";
String enteredCode = "";

void setup() {
  Serial.begin(9600);
}

void loop() {
  char key = kpd.getKey();
  if (key) {
    if (key == '#') {
      if (enteredCode == correctCode) {
        Serial.println("Input matched");
      } else {
        Serial.println("Wrong Input");
      }
      enteredCode = "";
    } else if (key == '*') {
      enteredCode = "";
    } else {
      enteredCode += key;
    }
  }
}
```

## 9. Buzzer alert 3kHz (Set A, SET-A Q1)
```cpp
const int buzzer = 9;

void setup() {
  Serial.begin(9600);
  pinMode(buzzer, OUTPUT);
}

void loop() {
  tone(buzzer, 3000);
  Serial.println("Generating sound");
  delay(3000);

  noTone(buzzer);
  Serial.println("No sound generated");
  delay(2000);
}
```

## 10. PIR + buzzer alarm (Set B, SET-B Q2)
```cpp
const int pirPin = 2;
const int buzzer = 9;

int pirStat = 0;

void setup() {
  Serial.begin(9600);
  pinMode(pirPin, INPUT);
  pinMode(buzzer, OUTPUT);
}

void loop() {
  pirStat = digitalRead(pirPin);

  if (pirStat == HIGH) {
    digitalWrite(buzzer, HIGH);
    Serial.println("Motion Detected");
  } else {
    digitalWrite(buzzer, LOW);
  }

  delay(1000);
}
```

The `.ino` files are also available above if you'd rather download and open them directly in Arduino IDE / Proteus.