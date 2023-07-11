## Day 1 (remote), section 2: intro to (simulator) Arduino

_In the previous section, we learned the very basics of C++ to write simple PC programs._
_Now, we'll use that to write embedded code in a simulator._ 

_We'll write code using [Arduino](https://www.arduino.cc/), a popular platform that makes it easy to get started writing embedded programs by providing intuitive functions like `digitalWrite()` that wrap the magic happening under the hood._


### Simulator: Wokwi

Embedded code generally needs to be deployed on a physical microcontroller.
Since we're remote today, we'll use a simulator, [Wokwi](https://wokwi.com/).

You don't need to sign up for an account for what we'll be doing today.


## Activity 1.1: Hello ~~world~~ blinky

While the first example for C++ on a PC was printing "Hello, world!" to the console, embedded devices often lack a screen.
Instead, the first program is commonly to blink an LED.

Like with Hello, world!, we'll start you off with example code: [start with this Wokwi project](https://wokwi.com/projects/369909925978652673), which includes a circuit and example code.
TODO: EXAMPLE CIRCUIT

Let's go over the circuit first:
- At the heart is a ESP32, which is a popular microcontroller that includes WiFi and Bluetooth capabilities.
  - Think of a microcontroller as a low-end computer on a chip: it has a CPU, memory, and storage.
  - In addition to a processor, it has pins which are controlled by the processor.
    These have a variety of capabilities, but the most basic is digital GPIO (general purpose input / output).
    - On the output side, you can write a 0 or 1 to the pin, which will set the voltage on the pin to ground (0v) or the positive supply (3.3v).
    - On the input side, you can read the digital voltage on the pin, approximated to either 0 (if it's much closer to 0v) or 1 (if it's much closer to 3.3v).
- One of these GPIO pins (D2 in this case) is connected to an LED and resistor.
  - LEDs light up when there is a voltage across them. 
  - When the GPIO is set to 0, 0v will be on the pin, and there will be a 0v difference across the LED, and it will not light up.
  - When the GPIO is set to 1, 3.3v will be on the pin, and there will be a 3.3v difference across the LED, and it will light up.
  - The resistor is there to protect the LED.
    LEDs can be damaged by excessive current (typically above 20mA for low-power LEDs), and the resistor limits the current to a reasonable amount.
  - This is a very simplified, math-free explanation that's good enough for programming embedded systems.
    Feel free to ask us if you want more details on the circuits side!


```cpp
const uint8_t kLedPin = 2;


void setup() {
  // put your setup code here, to run once:
  pinMode(kLedPin, OUTPUT);

  Serial.begin(115200);
  Serial.println("Hello, world");
}

void loop() {
  // put your main code here, to run repeatedly:
  digitalWrite(kLedPin, HIGH);
  delay(500);
  digitalWrite(kLedPin, LOW);
  delay(500);
}

```

While this is still C++ code, this differs from the prior examples significantly.
- There is no `main()` function. Instead, there are two functions: `setup()` and `loop()`.
