## Day 2 (in-person), section 1: working with hardware in Arduino

_Today, we'll get to program actual hardware, and including using real-world versions of the parts you used in the simulator._

_Circuit boards may contain toxic materials._
_They are safe to touch, but wash your hands prior to eating._
_Not lick-safe._

Since our focus is on the embedded programming aspect, we will be using this pre-built custom circuit board:
![OwlBot pinning](owlbot-pinning.png)

It has an ESP32-S3 microcontroller in a socketed daughterboard with onboard switch and LED.
The carrier board contains the LED ring (which you'd be familiar with from the simulator), a light sensor, and OLED display.
There's a few more devices that won't be part of the core lab, but if you have time you can play with then in the extra section.


### Activity 2.1: Hardware Bring-up

When bringing up new hardware, it's generally a good idea to start small and build up, instead of building everything at once and hoping it works on the first try.
While we have full code including the LED ring from yesterday and validated in the simulator, let's still work in steps.

Similarly to the last lab, the first thing we'll do is blink the LED, since this is the simplest thing that tests basic functionality
Copy this code into Arduino, then hit Upload to the board.

TODO: upload button screenshot

If all worked correctly, the LED on the daughterboard should blink about once a second.

```cpp
const int kLedPin = 2;


void setup() {
  // put your setup code here, to run once:
  pinMode(kLedPin, OUTPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  digitalWrite(kLedPin, HIGH);
  delay(500);
  digitalWrite(kLedPin, LOW);
  delay(500);
}
```


### Now you try!

Re-implement the switch code from the last lab, and use it to gate the blinking.
When the switch is pressed, the LED should blink once every second.
When the switch is not pressed, the LED should be off.

<details><summary><span style="color:DimGrey"><b>Solution</b> (try it on your own first!)</span></summary>

  ```cpp
  const int kLedPin = 2;
  const int kButtonPin = 0;
  
    
  void setup() {
    // put your setup code here, to run once:
    pinMode(kLedPin, OUTPUT);
    pinMode(kButtonPin, INPUT_PULLUP);
  }
    
  void loop() {
    // put your main code here, to run repeatedly:
    if (!digitalRead(kButtonPin)) {  // just gate the blinking with an if conditional on the button state 
      digitalWrite(kLedPin, HIGH);
      delay(500);
      digitalWrite(kLedPin, LOW);
      delay(500);
    }
  }
  ```
</details>


### Activity 2.2: Printf on Hardware

While a single LED used creatively can be surprisingly useful as a debugging tool, it's also pretty limiting.
And although we don't have a screen on this board (yet) like we might print on a PC, we can still send print data to a connected PC.
This is typically done using a Serial port, and is a common way to debug embedded systems.

> ⚠️ Printing data to Serial is NOT instantaneous and can take time!
> A short sentence might take a few milliseconds, but if milliseconds matter for your application (which it might!), this can throw off timing.

> <details><summary>⚡ More details about Serial...</summary>
>
>   Serial refers to a serial port, in which data is transferred one bit at a time.
>   In embedded systems, this often refers to a UART (universal asynchronous receiver-transmitter), a signalling standard for transferring bytes.
>   This is commonly used to send debugging information to a connected PC, where it can then be displayed.
> 
>   UART communications are defined in terms of baud rate, which is the number of bits per second.
>   115200 baud (a common faster speed) means 115200 bits per second.
>   At 8 bits per character, one start bit, and one stop bit, this means 11520 characters per second, or about 0.086 milliseconds per character.
> 
>   In simpler frameworks like Arduino, Serial `print`s are blocking (like `delay`) until the data transfer completes.
>   On some more advanced processors, it is possible to send serial data in the background, with `print` returning immediately while the data transfer continues.
>
>   On modern boards, a separate chip commonly adapts UART signaling to USB, which can then be directly plugged into a PC.
>   However, the concept is still the same: a stream of characters from the microcontroller to the screen, which can be displayed as text. 
> </details>

Copy this code into Arduino, but before uploading, open the Serial Monitor (main menu -> Tools -> Serial Monitor).
Then, hit Upload to load the code onto the board.
By starting serial monitor beforehand, you can see the output from the board as soon as it starts running, including prints in setup().
TODO serial monitor screen, upload screen

```cpp
const int kLedPin = 2;


void setup() {
  // put your setup code here, to run once:
  pinMode(kLedPin, OUTPUT);

  Serial.begin(115200);
  Serial.println("Hello, real ESP32!");
}

void loop() {
  // put your main code here, to run repeatedly:
  Serial.println("Blink");
  digitalWrite(kLedPin, HIGH);
  delay(500);
  digitalWrite(kLedPin, LOW);
  delay(500);
}
```


### Activity 2.3: Reading the Light Sensor

Nlogoutput the board led




### Activity 2.2: Porting over the LED Ring


### Activity 2.4: OLED Display

_Let us know when you get to this part, and we'll provide the OLED display and right-angle USB connector._


### Activity 2.5: Putting it all together: Ring Light Sensor with Numeric Display


### Extra for Experts I: Servos
