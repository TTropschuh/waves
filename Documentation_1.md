# __[IN]VISIBLE__
Make the invisible, visible.
#### Pyhsical Prototyping with Phillip Stearns @ Parsons

## Midterm/FINAL Project 2019
New York City is from a sensual perspective overwhelming. Everywhere we are perceiving something. We see adds, people crossing our way and smell the city in sometimes a good but mostly in a bad way. In this project I want to focus on humans’ sensual experiences that are not visible and hard to grasp. To narrow this a little bit down I set myself the topic “Pollution” which is and will always be in a city like New York a hot topic. The main aim in this midterm project is to make the data perceivable in an optical way. The collected data should not get dusty in an exel sheet but rather be caught in its context: The streets of New York.  

![immaterials](http://www.elasticspace.com/wp-content/uploads/2013/03/Immaterials-Wifi-21.jpg)

# Wave Pollution in urban spaces

![Tadeus](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fdata.whicdn.com%2Fimages%2F33678463%2Flarge.gif&f=1&nofb=1)

### Components
1. AGC Electret Microphone Amplifier - MAX9814 [Data sheet](https://cdn-shop.adafruit.com/datasheets/MAX9814.pdf)
2. Arduino Uno
3. LED Stripe
4. Resistors
5. Capacitors
6. Button and LED for testing

### Step one: Get the sound values.

So what does this amplifier do? Well, basically nothing more than this.

![amplifier_old_fashion](https://cdn4.explainthatstuff.com/ear-trumpet-amplifiers.jpg)

source: https://www.explainthatstuff.com/amplifiers.html

William Shockley had a pretty good way of describing an amplifier:

_"If you take a bale of hay and tie it to the tail of a mule and then strike a match and set the bale of hay on fire, and if you then compare the energy expended shortly thereafter by the mule with the energy expended by yourself in the striking of the match, you will understand the concept of amplification._


__What is the gain and why do I need it?__

The gain of an amplifier Verhältnis zwischen Output und Input Signal --> called Gain. An amplifier that doubles the size of the original signal has a gain of 2. In terms of Audio this gain is also expressed in decibels.
The ‘max gain’ of the MAX9814 is 60dB.

__const int__ // Konstante
const stand for constant. Pi could be used there.

__unsigend int__ // vorzeichenlose Ganzezahlen

__long__ // save 32 bits

What we learned about Analog Inputs: Compared to the digital Input analog inputs are not just LOW and HIGH. An Analog input measures a range of voltage levels.
_"They are analogous to some other property"_

![Pressure](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia1.tenor.com%2Fimages%2Ff0e4350c6d65022806875f51100754ae%2Ftenor.gif%3Fitemid%3D3715232&f=1&nofb=1)

!!Dont read more than 5 voltage!!

#### Circut Diagram for the amplifier

![](https://raw.githubusercontent.com/TTropschuh/waves/master/Amplifier_Circuit.PNG)

#### Code

As it is very compicated and  timeconsuming to build an amplifier with a bunch of capacitors from scratch I decieded to go with the Electret Microphone Amplifier MAX9814. I also has a build in grain controll wich my be helpful later.

First question: How do I get the sound value in the serial monitor? Lucky me somewhere someone already did that for me. I found this code here.



```java

const int sampleWindow = 50; // Sample window width in mS (50 mS = 20Hz)
unsigned int sample;



void setup()
{
  pinMode(6, OUTPUT);
   Serial.begin(9600);
}


void loop()
{
   unsigned long startMillis= millis();  // Start of sample window
   unsigned int peakToPeak = 0;   // peak-to-peak level

   unsigned int signalMax = 0;
   unsigned int signalMin = 1024;

   // collect data for 50 mS
   while (millis() - startMillis < sampleWindow)
   {
      sample = analogRead(0);
      if (sample < 1024)  // toss out spurious readings
      {
         if (sample > signalMax)
         {
            signalMax = sample;  // save just the max levels
         }
         else if (sample < signalMin)
         {
            signalMin = sample;  // save just the min levels
         }
      }
   }
   peakToPeak = signalMax - signalMin;  // max - min = peak-peak amplitude
   double volts = (peakToPeak * 5.0) / 1024;  // convert to volts

   if (volts > 2.0) {

    digitalWrite(6, HIGH);

    }

    else {digitalWrite(6, LOW);}

   Serial.println(volts);
}

```

![Test_amplifier](https://raw.githubusercontent.com/TTropschuh/waves/master/TEST1_amplifier.gif)

Well, that was easy.

### Step two: Make the LED Stipe blink

I will use a LED Stripe from [Adafruit](https://www.adafruit.com/product/1461?length=1). They have a collection of funky and diverse of LEDs named NeoPixels. They are overpriced, I know. But!.. I already bought this a few months ago. Any other LED stripe could do the same job, I guess.

After I fried my USB Port a week ago, I decided to not __just__ try it out without a litte math before hand.

#### Technical specs:
- 5v DC Power or 3.7 Volt lithium-polymer battery _(Why less when I use a lipo battery?)_
- 18 Watts per meter or 60 LEDs
- ~ 3.5 Amps per meter or 60 LEDs

As my USB 2.0 port can only provide a max of 0.5 Amps I will need an external power supply. Yikes.

#### External Power supply

Options: Lipo battery, Plug;

I found this plug with an output of 5 Voltage and 2 amps. My strip will need 5V and 3.5 amps. Guess I just try it out and see if that is enough current.

![Plug](https://raw.githubusercontent.com/TTropschuh/waves/master/photo_2019-10-23_14-19-25.jpg)

I simply connected positive and negativ to the LED strip and DI and CI to the digital Pins 3 and 13. But nothing happend. This means I need more amps, I guess?
No, I am wrong. At the Adafruit site they used the same thing to get light a stip this size.

![two_hours_later](https://i.ytimg.com/vi/MayqOgrHo9I/maxresdefault.jpg)

WOW! Turned out that the power supply is broken. I simply connected the LED strip to the arduino without any external power supply. Poor build-in DC regulator... Another "problem" with this solution is that I won't be able to get the maximum of brightness out of the LEDs. But for now it works.

I also found out that these are not Neopixels. Neopixels have only 3 Pins (5V, GND, DIN). The LEDs I am using are called Apa102 LEDs and have 4 pins (5V, GND, CI, DI).

![](https://raw.githubusercontent.com/TTropschuh/waves/master/IMG_7552.gif)



### Step three: Use Analog INput to controll Digital OUTput.

Here I used a potentiometer to control the LED strip. Therefore I had to map the values from the potentiometer on the LED number.

![poti_input](https://raw.githubusercontent.com/TTropschuh/waves/master/poti_input.gif)


```java
//Library
#include <FastLED.h>

// Variables
#define NUM_LEDS 60
#define DATA_PIN 3
#define CLOCK_PIN 13


// Define an array of leds
CRGB leds[NUM_LEDS];

void setup() {
//Defines what kind of LEDs are on the strip and the pins
  FastLED.addLeds<APA102, DATA_PIN, CLOCK_PIN, RGB>(leds, NUM_LEDS);

}


    void loop() {
      // Read from Pin A2
        int val = analogRead(A2);
        // Map the values from the potentiometer with the number of the LEDs on the strip.
        int numLedsToLight = map(val, 0, 1023, 0, NUM_LEDS);

        // First, clear the existing led values

        FastLED.clear();
        // for loop to add or remove leds depending on the potentiometer value
        for(int led = 0; led < numLedsToLight; led++) {
            leds[led] = CRGB::Blue;
        }
        FastLED.show();
    }

```

### Step four: Use Microphone to controll the LED strip.

![final_circut](https://raw.githubusercontent.com/TTropschuh/waves/master/final_circut.PNG)


WTF is happening?

![trouble_uploading](https://raw.githubusercontent.com/TTropschuh/waves/master/trouble_shooting_upload.PNG)


### Step five: Make Button Input for the LED Strip

![](https://github.com/TTropschuh/waves/blob/master/video_2019-12-18_18-38-06.gif)


```java

// TryOut to Map sound on a LED Strip.
// What I am trying to achieve:
// 1) Map Sound on the strip
// 2) Add Button Input to only map the sound value when the button is pressed.



#include <FastLED.h>

#define LED_PIN     5
#define NUM_LEDS    14
#define BRIGHTNESS  64
#define LED_TYPE    WS2811
#define COLOR_ORDER GRB
CRGB leds[NUM_LEDS];

const int sampleWindow = 50; // Sample window width in mS (50 mS = 20Hz)
unsigned int sample;


void setup() {

  delay( 1000 ); // power-up safety delay
  FastLED.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(BRIGHTNESS);
  Serial.begin(9600);

}


void loop() {

  unsigned long startMillis = millis(); // Start of sample window
  unsigned int peakToPeak = 0;   // peak-to-peak level

  unsigned int signalMax = 0;
  unsigned int signalMin = 1023;

  while (millis() - startMillis < sampleWindow)
  {
    sample = analogRead(0);
    if (sample < 1024)  // toss out spurious readings
    {
      if (sample > signalMax)
      {
        signalMax = sample;  // save just the max levels
      }
      else if (sample < signalMin)
      {
        signalMin = sample;  // save just the min levels
      }
    }
  }

  peakToPeak = signalMax - signalMin;  // max - min = peak-peak amplitude
  double volts = (peakToPeak * 5.0) / 1024;  // convert to volts

  int numLedsToLight = map(sample, signalMax, signalMin, 0, NUM_LEDS);

  for (int dot = 0; dot < NUM_LEDS; dot++) {  // turning all LEDs off
    leds[dot] = CRGB::Black;
  }
  for (int dot = 0; dot < numLedsToLight; dot++) {    //turning on active LEDs
    leds[dot] = CRGB::Blue;
  }
  FastLED.show();
  Serial.println(volts); // Just checkin..
  delay(1000);
}

```

![s](https://github.com/TTropschuh/waves/blob/master/shemantic_1.jpg)


![finalthing](https://github.com/TTropschuh/waves/blob/master/final_item.jpg)



Helpful links:

https://f00l.de/blog/programmable-rgb-led-strip-microphone/

https://github.com/FastLED/FastLED/wiki/Basic-usage

https://learn.adafruit.com/digital-led-strip/code

https://www.adafruit.com/product/1461?length=1

https://www.electronics-tutorials.ws/filter/filter_7.html

https://learn.adafruit.com/adafruit-microphone-amplifier-breakout/assembly-and-wiring
