# AutomaticRainShelter
“Automatic Rain Shelter for Crops using 8051 and Arduino”
#define RAIN_SENSOR_PIN 2
#define MOTOR_A_IN1 3
#define MOTOR_A_IN2 4
#define MOTOR_B_IN3 5
#define MOTOR_B_IN4 6

enum RoofState { OPEN, CLOSED, MOVING_TO_CLOSE, MOVING_TO_OPEN };
RoofState roofState = OPEN; 

const unsigned long ROOF_MOVEMENT_TIME = 5000; 
unsigned long movementStartTime = 0;

void setup() {
  pinMode(RAIN_SENSOR_PIN, INPUT);
  pinMode(MOTOR_A_IN1, OUTPUT);
  pinMode(MOTOR_A_IN2, OUTPUT);
  pinMode(MOTOR_B_IN3, OUTPUT);
  pinMode(MOTOR_B_IN4, OUTPUT);

  Serial.begin(9600);
}

void loop() {
  int rainDetected = digitalRead(RAIN_SENSOR_PIN);

  Serial.print("Rain detected: ");
  Serial.println(rainDetected);

  unsigned long currentTime = millis();

  switch (roofState) {
    case OPEN:
      if (rainDetected == HIGH) {
        roofState = MOVING_TO_CLOSE;
        movementStartTime = currentTime;
        closeRoof();
      }
      break;

    case CLOSED:
      if (rainDetected == LOW) {
        // Start opening the roof
        roofState = MOVING_TO_OPEN;
        movementStartTime = currentTime;
        openRoof();
      }
      break;

    case MOVING_TO_CLOSE:
      if (currentTime - movementStartTime >= ROOF_MOVEMENT_TIME) {
        // Simulate roof fully closed
        stopMotors();
        roofState = CLOSED;
        Serial.println("Roof is now closed.");
      }
      break;

    case MOVING_TO_OPEN:
      if (currentTime - movementStartTime >= ROOF_MOVEMENT_TIME) {
        // Simulate roof fully opened
        stopMotors();
        roofState = OPEN;
        Serial.println("Roof is now open.");
      }
      break;
  }

  delay(100); // Short delay for stability
}

void closeRoof() {
  Serial.println("Closing roof...");
  digitalWrite(MOTOR_A_IN1, HIGH);
  digitalWrite(MOTOR_A_IN2, LOW);
  digitalWrite(MOTOR_B_IN3, HIGH);
  digitalWrite(MOTOR_B_IN4, LOW);
}

void openRoof() {
  Serial.println("Opening roof...");
  digitalWrite(MOTOR_A_IN1, LOW);
  digitalWrite(MOTOR_A_IN2, HIGH);
  digitalWrite(MOTOR_B_IN3, LOW);
  digitalWrite(MOTOR_B_IN4, HIGH);
}

void stopMotors() {
  Serial.println("Stopping motors...");
  digitalWrite(MOTOR_A_IN1, LOW);
  digitalWrite(MOTOR_A_IN2, LOW);
  digitalWrite(MOTOR_B_IN3, LOW);
  digitalWrite(MOTOR_B_IN4, LOW);
}
