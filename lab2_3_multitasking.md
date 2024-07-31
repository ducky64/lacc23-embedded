## Section 2, part 3: Multitasking

In adding the rainbow LED ring, we've also removed the button and blinky LED code.
Now, let's make it do both: rotate the LED ring, AND blink the LED when the button is pressed.

You might be templated to tack on the LED code at the end of the loop:  
```cpp
void loop() {
  // put your main code here, to run repeatedly:
  
  // your LED ring code here
  
  if (!digitalRead(kButtonPin)) { 
    digitalWrite(kLedPin, HIGH);
    delay(500);
    digitalWrite(kLedPin, LOW);
    delay(500);
  }
}
```

Give it a try and find out - note what happens to the LED ring animation when you press the button!
Why do you think this is happening?

<details><summary><b>Explanation</b> (think about it your own first!)</summary>

  The problem is that `loop()` runs as a unit between iterations, and `delay(...)`s block the entire loop and affects timing between loops.
  Without the button and LED code, we only would have a delay of 250ms between loops, but with the button pressed, we stack another 1000ms delay on top of that, which throws off the LED ring timing.

  With the button not pressed, each loop looks like this:
  ```cpp
  void loop() {
    // put your main code here, to run repeatedly:
    
    // your LED ring code here
    delay(250);
    
    // button not pressed, the if conditional doesn't run
  }
  ```
  and the LED ring animation runs at the expected timing.

  However, with the button pressed, each loop looks like:
  ```cpp
  void loop() {
    // put your main code here, to run repeatedly:
    
    // your LED ring code here
    delay(250);
    
    // button is pressed, the if conditional runs 
    digitalWrite(kLedPin, HIGH);
    delay(500);
    digitalWrite(kLedPin, LOW);
    delay(500);
  }
  ```
  and now there's a total 1250ms delay between loops.
  Since the LED ring update runs once per loop, having the button pressed changes the timing between loops and the ring animation speed.

</details>

### The Fix

There are a few ways to solve this (on a beefier processor, you might be able to use threading, which is not available on all microcontrollers).

One thing we might infer from the explanation is that `delay` is problematic.
And indeed, it is - it causes the CPU to wait, unable to do anything else even if there were other things we think it should be doing.
So here, we'll solve this by eliminating delays, instead using a clock function (`millis()`, which returns the number of milliseconds since the program started) to check when some time has passed.

```cpp
const int kLedPin = 2;
const int kButtonPin = 13;
const int kNeoPixelPin = 12;

#include <Adafruit_NeoPixel.h>
const int kNeoPixelCount = 16;
Adafruit_NeoPixel LedRing(kNeoPixelCount, kNeoPixelPin);

void setup() {
  // put your setup code here, to run once:
  pinMode(kLedPin, OUTPUT);
  pinMode(kButtonPin, INPUT_PULLUP);

  Serial.begin(115200);
  Serial.println("Hello, ESP32!");

  LedRing.begin();
}

int ledOffTime = 0;  // millis() at which to turn off the LED
int ledResetTime = 0;  // earliest millis() at which the LED can blink again

void loop() {
  // put your main code here, to run repeatedly:
  
  // your LED ring code here
  
  if (millis() >= ledOffTime) {
    digitalWrite(kLedPin, LOW);
  }
  if (millis() >= ledResetTime) {  
    if (!digitalRead(kButtonPin)) {
      digitalWrite(kLedPin, HIGH);
      ledOffTime = millis() + 500;
      ledResetTime = millis() + 1000;
    }
  }
}
```

One analogy to how this works might be baking a cake for 30 minutes.
`delay` would be just staring at the oven, waiting for 30 minutes.
There might be other things you should be doing, but you're just waiting.
In contrast, `millis()` is starting the oven, recording the done time, and checking the clock regularly.
While it's baking, you can go do other things, and when time is up, you can enjoy your cake.

In the code, we record the time the LED should turn off in `ledOffTime`, and the time to check for the button press in `ledResetTime`.
Instead of a delay, each loop iteration checks if the required time has elapsed, and if so, does the actions.
If you hold the button down, it should blink.

However, if you click the button, you may notice that the LED may drop clicks.
This is because there's still a 250ms delay in the LED ring code, and if the button is pressed during that time, it's never detected.

### Now you try!

Let's fix that: rewrite the LED ring code in the above style, and make it so that button presses are consistently detected.

<details><summary><span style="color:DimGrey"><b>🤔 Solution</b> (try it on your own first!)</span></summary>

  For this, we've added a ringUpdateTime, which is the next `millis()` at which the ring should update.
  On each iteration, this advances by 250ms.

  ```cpp
  const int kLedPin = 2;
  const int kButtonPin = 13;
  const int kNeoPixelPin = 12;
  
  #include <Adafruit_NeoPixel.h>
  const int kNeoPixelCount = 16;
  Adafruit_NeoPixel LedRing(kNeoPixelCount, kNeoPixelPin);
  
  void setup() {
    // put your setup code here, to run once:
    pinMode(kLedPin, OUTPUT);
    pinMode(kButtonPin, INPUT_PULLUP);
  
    Serial.begin(115200);
    Serial.println("Hello, ESP32!");
  
    LedRing.begin();
  }

  int offset = 0;
  int ringUpdateTime = 0;
  
  int ledOffTime = 0;  // millis() at which to turn off the LED
  int ledResetTime = 0;  // earliest millis() at which the LED can blink again
  
  void loop() {
    // put your main code here, to run repeatedly:
    if (millis() >= ringUpdateTime) {
      for (int i=0; i<kNeoPixelCount; i++) {
        int index = (i + offset) % 6;
        if (index % 6 == 0) {
          LedRing.setPixelColor(i, LedRing.Color(255, 0, 0));
        } else if (index == 1) {
          LedRing.setPixelColor(i, LedRing.Color(255, 255, 0));
        } else if (index == 2) {
          LedRing.setPixelColor(i, LedRing.Color(0, 255, 0));
        } else if (index == 3) {
          LedRing.setPixelColor(i, LedRing.Color(0, 255, 255));
        } else if (index == 4) {
          LedRing.setPixelColor(i, LedRing.Color(0, 0, 255));
        } else if (index == 5) {
          LedRing.setPixelColor(i, LedRing.Color(255, 0, 255));
        }
      }
      offset++;
      LedRing.show();
  
      ringUpdateTime = millis() + 250;
    }
    
    if (millis() >= ledOffTime) {
      digitalWrite(kLedPin, LOW);
    }
    if (millis() >= ledResetTime) {  
      if (!digitalRead(kButtonPin)) {
        digitalWrite(kLedPin, HIGH);
        ledOffTime = millis() + 500;
        ledResetTime = millis() + 1000;
      }
    }
    
    delay(5);  // needed so simulator doesn't lag
  }
  ```

  We've added a `delay(5)` in every loop so the simulator doesn't get overloaded with constantly processing.
  On a real device, this wouldn't be necessary
</details>

One thing that we didn't address here, but you should be aware of, is that `millis()` will eventually overflow.
This probably won't happen for many days of a system being constantly up, but if you are designing a long-lived system, you may need to take steps to address overflow.

And that's all, folks!
