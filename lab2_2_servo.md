## Day 2 (in-person), choose-your-own-adventure: Servo

_Let us know if you're doing this part, and we'll provide the servo._
_The servo should be connected brown wire to ⏚ (ground) on the board, and orange wire to ⎍ (signal) on the board._
_Don't worry if you accidentally plug it in backwards, it won't work but probably won't break anything_.

_A servo here is a motor that can be controlled to move to a specific angle._

Once everything is connected and plugged back in, run this code:

```cpp
#include <ESP32Servo.h>
const int kLeftServoPin = 47;
Servo LeftServo;


void setup() {
  // put your setup code here, to run once:
  LeftServo.attach(kLeftServoPin);
}

void loop() {
  // put your main code here, to run repeatedly:
  LeftServo.write(0);
  delay(1000);
  LeftServo.write(90);
  delay(1000);
  LeftServo.write(180);
  delay(1000);
}
```

Hopefully the overall structure of the code looks familiar now:
- We `#include` the ESP32Servo library and create a `Servo` object.
- The `Servo` object is `attach(...)`ed to a specific pin.
  This is like `begin(...)` in some other libraries.
- You can `write` the commanded angle to the servo.
  This is specified in degrees, between `0` and `180`.


## Try it yourself: Dial Light Sensor

Instead of displaying the light intensity on the OLED and LED ring (or in addition to those, if you want a challenge ??), try mapping the received light intensity to the angle of the servo, emulating an old-fashioned mechanical dial.

You may want to build on your light scaling code from the last lab.
But instead of scaling that to the number of pixels in the LED ring, scale it to between `0` to `180` degrees.

<details><summary><span style="color:DimGrey"><b>🤔 Solution</b> (try it on your own first!)</span></summary>

  ```cpp
  const int kLightSensorPin = 1;
  const int kMinLight = 1024;

  #include <ESP32Servo.h>
  const int kLeftServoPin = 47;
  Servo LeftServo;
  
  
  void setup() {
    // put your setup code here, to run once:
    LeftServo.attach(kLeftServoPin);
  }
  
  void loop() {
    // put your main code here, to run repeatedly:
    int lightValue = analogRead(kLightSensorPin);

    int reversedLight = kMinLight - lightValue;
    LeftServo.write(reversedLight * 180 / kMinLight);
    delay(100);
  }
  ```
</details>
