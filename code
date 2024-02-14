const byte LED_PIN = 13;
const byte METER_PIN = A4;

// Variable to store the timer frequency
double timerFrequency = 1; // Default value

void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(METER_PIN, INPUT);
      
  Serial.begin(9600);
  
  // Read initial timer frequency from potentiometer
  timerFrequency = readPotentiometer();
  
  // Start the timer with the initial frequency
  startTimer(timerFrequency);
}

void loop() { 
  // Check if the timer frequency needs to be updated
  double newFrequency = readPotentiometer();
  if (newFrequency != timerFrequency) {
    timerFrequency = newFrequency;
    startTimer(timerFrequency);
  }
}

// Function to start the timer with a given frequency
void startTimer(double timerFrequency) {
  noInterrupts();
  
  // Calculate the appropriate timer values based on the given frequency
  double timerPeriod = 1.0 / timerFrequency;
  unsigned long timerCompareValue = (timerPeriod * F_CPU / 1024) - 1; // Assuming prescaler 1024 for Timer1
  
  // Set timer1 in CTC mode with 1024 prescaler
  TCCR1A = 0; // Set entire TCCR1A register to 0
  TCCR1B = 0; // Same for TCCR1B
  TCNT1 = 0;  // Initialize counter value to 0
  // Set compare match register for the desired frequency (250ms = 0.25s)
  OCR1A = timerCompareValue; // must be <65536
  // Turn on CTC mode
  TCCR1B |= (1 << WGM12);
  // Set CS12, CS10 bits for 1024 prescaler
  TCCR1B |= (1 << CS12) | (1 << CS10);
  // Enable timer compare interrupt
  TIMSK1 |= (1 << OCIE1A);
  
  interrupts();
}

// Function to read potentiometer value and calculate timer frequency
double readPotentiometer() {
  int sensorValue = analogRead(METER_PIN);
  // Map the analog reading (0-1023) to a frequency range (e.g., 0.1 to 10 Hz)
  double frequency = map(sensorValue, 0, 1023, 1, 100) / 10.0;
  return frequency;
}

// Interrupt Service Routine for Timer1 Compare Match A
ISR(TIMER1_COMPA_vect) {
   digitalWrite(LED_PIN, !digitalRead(LED_PIN)); // Toggle LED state
}
