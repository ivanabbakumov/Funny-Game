# Funny-Game
## Забавная игра с использованием Arduino

По условиям игры пользователь за 30 секунд должен набрать минимум 30 очков, в таком случае он получает награду (мандаринку). В случае же поражение насос накачивает воду в трубку, и система выпускает струю воды в лицо игроку. 

Игра написана на языке java script (ссылка прикреплена в репозитории), а техническая часть реализована с помощью arduino uno и написана на c++

Код для arduino
```c++
#include <Servo.h>

#define DIR_1 4
#define SPEED_1 5
#define DIR_2 7
#define SPEED_2 6
#define BUZZER 10
#define FACECHECK 2
#define WATERCHECK A2

Servo ser1, ser2;

unsigned long startingMillis = 0;
unsigned long rotationTime = 400; 
const int servoSpeed = 120;        
const int stopValue = 90;          

bool runServo = false;

int melodyWin[5] = {262, 196, 220, 247, 262}; 
int melodyLose[4] = {294, 277, 262, 247}; 

void setup() {
  for (int i = 4; i < 8; i++) {
    pinMode(i, OUTPUT);
  }
  ser1.attach(9);
  ser1.write(stopValue);
  ser2.attach(12);
  pinMode(BUZZER, OUTPUT);
  pinMode(FACECHECK, INPUT);
  pinMode(WATERCHECK, INPUT);

  Serial.begin(9600);
}

void loop() {
  int face = digitalRead(FACECHECK);
  int water = analogRead(WATERCHECK);
  Serial.println(String(face) + "; " + String(water));

  if (runServo) {
    if (startingMillis == 0) {
      startingMillis = millis();
      ser1.write(servoSpeed);
      Serial.println("Servo started");
    }

    if (millis() - startingMillis >= rotationTime) {
      ser1.write(stopValue);
      runServo = false;
      startingMillis = 0;
      Serial.println("Servo stopped after one rotation");
    }
  }

  if (Serial.available() > 0) {
    String data = Serial.readStringUntil('\n');

    if (data == "Win") {
      for (int i = 0; i < sizeof(melodyWin) / sizeof(melodyWin[0]); i++) {
        tone(BUZZER, melodyWin[i], 500);
        delay(100);
      }
      runServo = true;
    }
    else {
      for (int i = 0; i < sizeof(melodyLose) / sizeof(melodyLose[0]); i++) {
        tone(BUZZER, melodyLose[i], 500);
        delay(100);
      }
      digitalWrite(DIR_1, HIGH);
      analogWrite(SPEED_1, 255);
      delay(5000);
      analogWrite(SPEED_1, 0);
      ser2.write(90);
      delay(5000);
      ser2.write(180);
      delay(10000);
      ser2.write(90);
    }
  }
}
```

## Фотографии готовой установки
![IMG_1957](https://github.com/user-attachments/assets/00f87bd5-4abe-48fe-b4dd-88fc8fc1fa12)
![IMG_1958](https://github.com/user-attachments/assets/36bab641-40a0-4ff8-94af-7829c6dd1f6b)
![IMG_1959](https://github.com/user-attachments/assets/f399fc85-7bc5-4f29-ab0c-fdeacd4ba4a1)
![IMG_1960](https://github.com/user-attachments/assets/2881fb95-2b00-4ec8-85a3-60433a1934d0)
