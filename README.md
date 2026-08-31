# Encoders

We use Encoders to give us a feedback about our motion to know the Distance we Moved. And the Dierction we move is it Forward or Backward.

![](https://dfimg.dfrobot.com/enshop/image/data/SEN0230/SEN0230_260529%20(1)_1200x630.jpg.webp)

## Types of Encoders 

there are many types of Encoders Like : 

1- Incremental

2- Absolute

## Connection :

as We see the Encoder have **four Wires** : 

1- Red >>>>>>>>>>> **Vcc** 5volt

2- Black >>>>>>>>> **GND**

3- Green >>>>>>>>> **phase A**

4- White >>>>>>>>> **Phase b**

There is a Grey Wire but we dont use it called the Shield 

![](https://electricdiylab.com/wp-content/uploads/2019/07/Untitled-3-1024x696.jpg)


if we connect one instead of the other in phases we can flip them , we will know if we rotate the Wheel Forward and it decreases the Count 

![](https://europe1.discourse-cdn.com/arduino/original/4X/9/4/3/94394bff4c59697e6903056d61ce86fdc41885ce.jpeg)


## After you Connected it Open **Arduino IDE** and choose the Board Arduino uno and change it if it is not uno

![](arduino.png)


## Here is A sample code to test the Encoders :

```
// Encoder total tick counter — Arduino Uno

#define ENCODER_PIN_A  2   // INT0
#define ENCODER_PIN_B  3   // INT1

volatile long total_ticks = 0;

void setup() {
  Serial.begin(115200);
  pinMode(ENCODER_PIN_A, INPUT_PULLUP);
  pinMode(ENCODER_PIN_B, INPUT_PULLUP);

  attachInterrupt(digitalPinToInterrupt(ENCODER_PIN_A), encoderISR_A, CHANGE);
  attachInterrupt(digitalPinToInterrupt(ENCODER_PIN_B), encoderISR_B, CHANGE);
}

void loop() {
  noInterrupts();
  long ticks = total_ticks;
  interrupts();

  Serial.print("Total ticks: ");
  Serial.println(ticks);

  delay(100);
}

void encoderISR_A() {
  bool a = digitalRead(ENCODER_PIN_A);
  bool b = digitalRead(ENCODER_PIN_B);
  total_ticks += (a == b) ? 1 : -1;
}

void encoderISR_B() {
  bool a = digitalRead(ENCODER_PIN_A);
  bool b = digitalRead(ENCODER_PIN_B);
  total_ticks += (a != b) ? 1 : -1;
}
```
## How it Works :

The encoder disk has a series of evenly spaced slots cut into it. On one side of the disk sits an LED; on the other side sits a light receiver (phototransistor). As the encoder shaft turns, each slot alternately lets light through to the receiver and then blocks it, producing a square-wave HIGH/LOW electrical signal.

![](https://instrumentationtools.com/wp-content/uploads/2016/07/instrumentationtools.com_encoder-working-animation.gif)

The encoder has two such LED/receiver pairs — channel A and channel B — positioned slightly offset from one another around the disk (a quarter of a slot-pitch apart). This physical offset between A and B is what makes direction detection possible; a single channel alone can only tell you that the shaft moved, not which way.

![](Encoder_diagram.png)


### Signal behavior

As a slot passes a channel's sensor:

Light gets through → the signal reads 1
The slot moves on and the disk blocks the light again → the signal reads 0

Because A and B are offset, they don't flip at the same instant — one of them always changes slightly before the other, and which one leads depends on the direction of rotation.

How direction is detected

Two interrupts are used, one per channel, each set to fire on any change (CHANGE — both 0→1 and 1→0 edges). Each interrupt handler reads both channels' current states and applies a comparison rule:

**When channel A changes:**

A vs B	Meaning	Tick count

Equal (A == B)	Moving forward	+1

Not equal (A != B)	Moving backward	−1

**When channel B changes:**

A vs B	Meaning	Tick count

Not equal (A != B)	Moving forward	+1

Equal (A == B)	Moving backward	−1


![](https://fb00d14c.delivery.rocketcdn.me/wp-content/uploads/2016/07/Rotary-Encoder-Output-Signal-Working-Principle-Photo-.png)

## when we use it : 

**Flat, firm, high-traction surfaces — pavement, tile, lab/warehouse floors** >>>> where a wheel turning by a certain amount reliably corresponds to the robot moving that same amount.

Short-term, relative motion tracking — encoders are excellent at answering "how far did I just move since a moment ago," especially when combined with an IMU for smoother short-term estimates.
Indoor or controlled environments where wheel slip is minimal and consistent.

## When We dont use it :

**Loose or slippery terrain — sand, gravel, mud, wet or icy floors** >>>> The wheel can spin (or skid) without the robot actually moving that distance, so the tick count no longer reflects true travel. This is called wheel slip, and it's the single biggest source of encoder-odometry error.

**Rough or uneven terrain — bumps and obstacles** can cause a wheel to briefly lose contact with the ground or skid sideways, again breaking the simple "ticks = distance" assumption.

**Skid-steer / tank-style turning** >>>> wheels scrub sideways against the ground when turning in place, which can throw off tick-based distance and heading estimates unless carefully calibrated.

**Long-duration navigation — even under good conditions** >>>> small errors in encoder odometry accumulate over time (this is called drift). Encoders alone are not reliable for long-term absolute positioning; they need to be fused with other sensors (IMU, GPS, LIDAR/SLAM) — commonly via a filter such as an Extended Kalman Filter — to correct for accumulated drift.

The most Famous Localization Algorthim that uses Encoders is the **Dead Reckoning**

## Some note :

As we know we use the interrupt pins in the **Ardunio Uno (2,3)** and **on mega 2,3,18,19,20,21** , **ESP32 GPIO 4 ,GPIO 13 ,GPIO 14 ,GPIO 16 ,GPIO 17 ,GPIO 18,GPIO 19 ,GPIO 21 ,GPIO 22 ,GPIO 23,GPIO 25 ,GPIO 26 ,GPIO 27 ,GPIO 32 ,GPIO 33**

Why not just poll a regular digital pin instead?

You could read a normal digital pin with digitalRead() inside loop(), but that brings back the exact problem we covered earlier: polling only checks at fixed moments. An encoder can spin fast enough to flip states between your checks, and you'd silently miss ticks — your count would drift and become inaccurate. That's precisely why encoders are almost always wired to interrupt-capable pins: you need the "wakes up the instant it changes" behavior, not "checks periodically and hopes it caught it.



**All CopyRights For MindCloud Team, Alexandria Universty 🇪🇬** 
