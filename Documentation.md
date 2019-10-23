# __[IN]VISIBLE__
Make the invisible, visible.
#### Pyhsical Prototyping with Phillip Stearns @ Parsons

## Midterm Project 2019
New York City is from a sensual perspective overwhelming. Everywhere we are perceiving something. We see adds, people crossing our way and smell the city in sometimes a good but mostly in a bad way. In this project I want to focus on humans’ sensual experiences that are not visible and hard to grasp. To narrow this a little bit down I set myself the topic “Pollution” which is and will always be in a city like New York a hot topic. The main aim in this midterm project is to make the data perceivable in an optical way. The collected data should not get dusty in an exel sheet but rather be caught in its context: The streets of New York.  

# Focus Point
## __Wave Pollution in urban spaces__

![Tadeus](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fdata.whicdn.com%2Fimages%2F33678463%2Flarge.gif&f=1&nofb=1)

### Components
1. AGC Electret Microphone Amplifier - MAX9814
2. Arduino Uno
3. LED Stripe
4. Resistors
5. Capacitors
6. Button and LED for testing

### Step one: Get the sound values.

#### Circut Diagram for the amplifier

![](https://raw.githubusercontent.com/TTropschuh/waves/master/Amplifier_Circuit.PNG)


#### Code

As it is very compicated and  timeconsuming to build an amplifier with a bunch of capacitors from scratch I decieded to go with the Electret Microphone Amplifier MAX9814. I also has a build in grain controll wich my be helpful later.

First question: How do I get the sound value in the serial monitor? Lucky me somewhere someone already did that for me. I found this code here.



```java
/****************************************
Example Sound Level Sketch for the
Adafruit Microphone Amplifier
****************************************/

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

### Make the LED Stipe blink

I will use a LED Stripe from [Adafruit](https://www.adafruit.com/product/1461?length=1). They have a collection of funky and diverse of LEDs named NeoPixels. They are overpriced, I know. But!.. I already bought this a few months ago. Any other LED stripe could do the same job, I guess.

After I fried my USB Port a week ago, I decided to not __just__ try it out without a litte math before hand.

#### Technical specs:
- 5v DC Power or 3.7 Volt lithium-polymer battery _(Why less when I use a lipo battery?)_
- 18 Watts per meter or 60 LEDs
- ~ 3.5 Amps per meter or 60 LEDs

As my USB 2.0 port can only provide a max of 0.5 Amps I will need an external power supply.
