#include <Arduino.h>

// LED pins
#define LED_RED     7
#define LED_GREEN   11
#define LED_BLUE    5
#define LED_YELLOW  9

// Button pins
#define BUTTON_RED    6
#define BUTTON_GREEN  10
#define BUTTON_BLUE   4
#define BUTTON_YELLOW 8

// Buzzer pin
#define BUZZER_PIN 12

// Game variables
byte pattern[12];  // Stores the sequence (limited to 12 steps)
int currentLevel = 1; // Starts with 1 step, increases each level
bool gameStarted = false;

// ===== Setup =====
void setup() {
  Serial.begin(9600);

  // Initialize buttons (internal pull-up)
  pinMode(BUTTON_RED, INPUT_PULLUP);
  pinMode(BUTTON_GREEN, INPUT_PULLUP);
  pinMode(BUTTON_BLUE, INPUT_PULLUP);
  pinMode(BUTTON_YELLOW, INPUT_PULLUP);

  // Initialize LEDs
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);
  pinMode(LED_BLUE, OUTPUT);
  pinMode(LED_YELLOW, OUTPUT);

  // Initialize buzzer
  pinMode(BUZZER_PIN, OUTPUT);

  // Seed random generator
  randomSeed(analogRead(0));
  
  // Wait for red button press to start
  waitForStart();
}

// ===== Main Loop =====
void loop() {
  if (gameStarted) {
    // 1. Add a new random step to the pattern
    addToPattern();

    // 2. Play the current pattern
    playPattern();

    // 3. Wait for the player to repeat it
    bool correct = checkPlayerInput();

    // 4. Give feedback & proceed to next level or restart
    if (correct) {
      if (currentLevel < 12) {
        playMelody(); // Correct melody
        currentLevel++; // Increase difficulty
      } else {
        // Player won the game (completed all 12 levels)
        victorySequence();
        resetGame();
      }
    } else {
      playBuzzer(); // Wrong buzzer
      resetGame();
    }

    delay(1000); // Short pause before next level
  }
}

// ===== Game Functions =====

void waitForStart() {
  while (true) {
    digitalWrite(LED_RED, HIGH);
    delay(300);
    digitalWrite(LED_RED, LOW);
    delay(300);
    
    int buttonState = digitalRead(BUTTON_RED);
    Serial.print("Button State: ");
    Serial.println(buttonState);  // Should be 1 (HIGH) normally, 0 (LOW) when pressed
    
    if (buttonState == LOW) {
      Serial.println("Button pressed!");
      gameStarted = true;
      digitalWrite(LED_RED, HIGH);
      tone(BUZZER_PIN, 1000, 200);
      delay(300);
      digitalWrite(LED_RED, LOW);
      while (digitalRead(BUTTON_RED) == LOW); // Wait for release
      break;
    }
  }
}

// Add a new random step to the pattern
void addToPattern() {
  byte newStep = random(0, 4); // 0=Red, 1=Green, 2=Blue, 3=Yellow
  pattern[currentLevel - 1] = newStep; // Store in array
}

// Play the current pattern
void playPattern() {
  for (int i = 0; i < currentLevel; i++) {
    byte step = pattern[i];
    // Light up the corresponding LED
    switch (step) {
      case 0: digitalWrite(LED_RED, HIGH); break;
      case 1: digitalWrite(LED_GREEN, HIGH); break;
      case 2: digitalWrite(LED_BLUE, HIGH); break;
      case 3: digitalWrite(LED_YELLOW, HIGH); break;
    }
    tone(BUZZER_PIN, 1000, 200); // Play a tone
    delay(300); // LED stays on for 300ms
    // Turn off all LEDs
    digitalWrite(LED_RED, LOW);
    digitalWrite(LED_GREEN, LOW);
    digitalWrite(LED_BLUE, LOW);
    digitalWrite(LED_YELLOW, LOW);
    delay(200); // Short pause between steps
  }
}

// Check if the player repeats the pattern correctly
bool checkPlayerInput() {
  for (int i = 0; i < currentLevel; i++) {
    byte expectedButton = pattern[i];
    byte pressedButton = waitForButton();
    
    if (pressedButton != expectedButton) {
      return false; // Wrong button pressed
    }
  }
  return true; // All correct
}

// Wait for any button press (no time limit)
byte waitForButton() {
  while (true) {
    if (digitalRead(BUTTON_RED) == LOW) {
      digitalWrite(LED_RED, HIGH);
      tone(BUZZER_PIN, 500, 100);
      delay(200);
      digitalWrite(LED_RED, LOW);
      while (digitalRead(BUTTON_RED) == LOW); // Wait for release
      return 0; // Red = 0
    }
    if (digitalRead(BUTTON_GREEN) == LOW) {
      digitalWrite(LED_GREEN, HIGH);
      tone(BUZZER_PIN, 600, 100);
      delay(200);
      digitalWrite(LED_GREEN, LOW);
      while (digitalRead(BUTTON_GREEN) == LOW);
      return 1; // Green = 1
    }
    if (digitalRead(BUTTON_BLUE) == LOW) {
      digitalWrite(LED_BLUE, HIGH);
      tone(BUZZER_PIN, 700, 100);
      delay(200);
      digitalWrite(LED_BLUE, LOW);
      while (digitalRead(BUTTON_BLUE) == LOW);
      return 2; // Blue = 2
    }
    if (digitalRead(BUTTON_YELLOW) == LOW) {
      digitalWrite(LED_YELLOW, HIGH);
      tone(BUZZER_PIN, 800, 100);
      delay(200);
      digitalWrite(LED_YELLOW, LOW);
      while (digitalRead(BUTTON_YELLOW) == LOW);
      return 3; // Yellow = 3
    }
  }
}

// Play a happy melody (correct)
void playMelody() {
  tone(BUZZER_PIN, 1318, 200); delay(200); // E6
  tone(BUZZER_PIN, 1567, 200); delay(200); // G6
  tone(BUZZER_PIN, 1760, 400); delay(400); // A6
  noTone(BUZZER_PIN);
}

// Play a buzzer sound (wrong)
void playBuzzer() {
  tone(BUZZER_PIN, 300, 500);
  delay(500);
  noTone(BUZZER_PIN);
}

// Victory sequence when player completes all 12 levels
void victorySequence() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(LED_RED, HIGH);
    digitalWrite(LED_GREEN, HIGH);
    digitalWrite(LED_BLUE, HIGH);
    digitalWrite(LED_YELLOW, HIGH);
    tone(BUZZER_PIN, 2000, 200);
    delay(300);
    digitalWrite(LED_RED, LOW);
    digitalWrite(LED_GREEN, LOW);
    digitalWrite(LED_BLUE, LOW);
    digitalWrite(LED_YELLOW, LOW);
    delay(200);
  }
}

// Reset the game to start over
void resetGame() {
  currentLevel = 1;
  gameStarted = false;
  waitForStart();
}
