## Day 2 (in-person), choose-your-own-adventure: ESPHome and low-code / no-code

_Arduino made writing code for embedded systems easy, but you still had to write code._
_Code gives you a lot of flexibility and is great when you need to do something special, but often you don't._
_ESPHome is a code generator platform, that given a configuration file describing the hardware, will generate all the code._
_It has a library of components, including NeoPixels, servos, and many, many common sensors that are accessible to hobby builders._

We've included a config file for the ring board including the NeoPixels and light sensor.
You can either copy the description below to `ring_ap.yml`, or download the included file, [ring_ap](esphome/ring_ap.yml).

```yaml
esphome:
  name: ring_ap
  name_add_mac_suffix: true
  platform: esp32
  board: esp32-s3-devkitc-1

wifi:
  ap:
    ssid: "Ring Board"

logger:  # Print Updates

web_server:  # WebServer control interface
  port: 80
  local: true  # support running without an internet connection

light:
  - platform: neopixelbus
    id: led
    variant: WS2812
    pin: GPIO48
    num_leds: 12
    name: "NeoPixels"
    effects:
      - addressable_rainbow:
          name: Rainbow

sensor:
  - platform: adc
    pin: GPIO1
    name: "Brightness Raw"
    update_interval: 1s
```

⚠️ Make sure to change the SSID (WiFi name) to something unique, otherwise you're going to have trouble finding your particular Ring Board.

If you've already set up ESPHome (as directed in the [setup instructions](lab2_0.md)), start a terminal in the directory containing `ring_ap.yml`, then run:
```commandline
esphome run ring_ap.yml
```

This will compile and download the firmware to the connected board.
It should start running immediately.

On either your phone or laptop, look for the WiFi network named the SSID you chose above, and connect to it.
Then, go to http://192.168.4.1, and you should see the ESPHome web interface:  
![ESPHome web interface](esphome_android.png)

This will update the voltage measured on the light sensor analog pin, and allows you to control the NeoPixel ring.
By default, you can adjust the brightness and select a rainbow pattern.
