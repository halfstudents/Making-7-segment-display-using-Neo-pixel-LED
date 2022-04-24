# Making-7-segment-display-using-Neo-pixel-LED
A RGB effect panels which come with addressable led options, controlled using arduino for Project like clock and temp. measurement
Hello guys, I made want to make a 7-segment display. But this time with bigger digits and RGB effects. Because this can be used to display some readings like Clock with Nodemcu, temperature monitoring using Arduino. Also, with an option of color changing after each second or next reading. I also have the similar one, which I designed a while back. That design has some Led orientation problems.
show.gif
caption (optional)
Because last time I used 2 led’s to display per segment, our eye can’t detect it too fast and accurate. Means we need a focus; I am happy with the design but now it’s time for update. JLCPCB is always with me to provide good PCB prototype and SMT assembly service solutions.
mini_20220424_115400.jpg
caption (optional)
LED WS2812B 3030:
I used ws2812 Neo pixel led, which comes with integrated ic so that we can address each segment separately. Not only the LEDs are addressable but also the color can be changed per pixel changing the digital value between 0-255(8-bit value).
ice_screenshot_20220424-201345.png
caption (optional)
This led comes with 4 pins, the configuration of pins can be seen in the picture mentioned above. Also, these Led’s come with Data IN and Data OUT function, which make them more interesting and through this we can join them to show text or data.
Specs: Small, 3.3volts-5volts, 20mA and addressable.
7 segment display:
To make the panel, first we have to take a closer look on actual lcd. So that we can copy the arrangement of segments and design a code for that.
ice_screenshot_20220424-201129.png
caption (optional)
So here, segments are named as A, B, C…… to G, so to connect all the segments we use series data and parallel power method. All the power lines, GND and VCC are joined in parallel to all the led’s. Data OUT is supplied to Data IN of next Led in series.
Circuit diagram:
ice_screenshot_20220424-201116.png
caption (optional)
I designed the circuit in EasyEDA for each segment here I am using 3 LED’s. So, a total of 21 led’s per panel is required to make this configuration. I made the connection pins at bottom layer so that the connection and wiring is not visible to second person.
ice_screenshot_20220424-200359.png
caption (optional)
Keeping the formfactor, Layers and track data in mind I designed the PCB and generate the CPL, BOM along with Gerber files. If you want to use same designs as mine Download them from here. And for more information you can join the community on Instagram Sagar_saini_7294.
PCB and Gerbers:
Here is the designed panel, I displayed my name with JLCPCB as a sponsor and good friend.
qvef.png
caption (optional)
White color PCB, 1.6mm thickness and HASL finish is looking pretty damn good with JLCPCB SMT Assembly service. These Boards are fully assembled by JLCPCB, means all the components and soldering work is done by them. Why not to try this offer, JLCPCB SMT assembly service is staring from $8 and if you sign-up using this link, you will get free coupons of worth $30 and newcomer rewards.
Is JLCPCB SMT assembly worth:
unbocx.gif
caption (optional)
JLCPCB is the only PCB manufacturer providing SMT service in very cheap, yet the quality of boards is very good. I tested my all the Boards and they are working fine, all the components are okay. The soldering work is outstanding and cost is very less.
red.gif
caption (optional)
I ordered 15pcs of these assembled boards just in $48, the cost of components is $27 + cost of PCB ($6 for 15pcs) + Cost of assembly+ stencil+ soldering ($15). Which turns out to $3 per Piece, but if you order the boards in quantity say 30pcs the cost will not be 2x, that will be arranged and the total cost of panel would be near $2.30.
Connection with Arduino:
These are the connection of testing purpose only; I paired the panel with Arduino using this schematic here. You can use ESP8266 or Esp32 for bigger projects.
ice_screenshot_20220424-104452.png
caption (optional)
I am using cirkit designer software for along time, I think this is the best way to express and present the circuits. You can use this wire diagram, breadboard circuit along with custom component creating in school and college presentations. Also, Code compilation, BOM and some new components are recently added.
ice_screenshot_20220424-104133.png
caption (optional)
Download cirkit designer from Here
Cirkit Designer Is a one-stop-shop desktop application for designing and documenting circuits and electronics projects. With Cirkit Designer, you can lay out realistic circuit diagrams that are linked to a bill-of-materials so that you can seamlessly order the parts to your circuit.
show op.gif
caption (optional)
Code:
#include <Adafruit_NeoPixel.h>

#define PIXELS_PER_SEGMENT  3     // Number of LEDs in each Segment
#define PIXELS_DIGITS       1     // Number of connected Digits 
#define PIXELS_PIN          2     // GPIO Pin

Adafruit_NeoPixel strip = Adafruit_NeoPixel(PIXELS_PER_SEGMENT * 7 * PIXELS_DIGITS, PIXELS_PIN, NEO_GRB + NEO_KHZ800);

//Pixel Arrangement
/*
          a
        f   b
          g
        e   c
          d
*/

// Segment array
byte segments[7] = {
  //abcdefg
  0b0000001,     // Segment g
  0b0000100,     // Segment e
  0b0001000,     // Segment d
  0b0010000,     // Segment c
  0b0100000,     // Segment b
  0b1000000,     // Segment a
  0b0000010     // Segment f
};

//Digits array
byte digits[10] = {
  //abcdefg
  0b1111110,     // 0
  0b0110000,     // 1
  0b1101101,     // 2
  0b1111001,     // 3
  0b0110011,     // 4
  0b1011011,     // 5
  0b1011111,     // 6
  0b1110000,     // 7
  0b1111111,     // 8
  0b1110011      // 9
};

//Clear all the Pixels
void clearDisplay() {
  for (int i = 0; i < strip.numPixels(); i++) {
    strip.setPixelColor(i, strip.Color(0, 0, 0));
  }
  strip.show();
}

void setup() {
  strip.begin();
}

void loop() {
  //disp_Seg(200);                // Cycle through all segments        (DelayTime)
  disp_Digits(1000);            // Show digits from 0-9              (DelayTime)
  //disp_Animation();             // Show some Animations with the segments
 //disp_CountUP(500, 450);       // Count numbers in Ascending order  (NUMBER, DelayTime)
//  disp_CountDOWN(500, 250);     // Count numbers in Descending order (NUMBER, DelayTime)
}

void disp_Seg(int wait) {
  clearDisplay();
  for (int d = 0; d < 5; d++) {
    for (int i = 6; i > 0; i--) {
      for (int n = 0; n < PIXELS_DIGITS; n++) {
        writeSegment(n, i);
      }
      strip.show();
      delay(wait);
    }
  }
}

void disp_Digits(int wait) {
  clearDisplay();
  for (int i = 0; i < 10; i++) {
    for (int n = 0; n < PIXELS_DIGITS; n++) {
      writeDigit(n, i);
    }
    strip.show();
    delay(wait);
  }
}

void disp_CountUP(int num, int wait) {
  clearDisplay();
  for (int i = 0; i <= num; i++) {
    writeDigit(0, (i / 100) % 10);
    writeDigit(1, (i / 10) % 10);
    writeDigit(2, (i / 1) % 10);
    strip.show();
    delay(wait);
  }
}

void disp_CountDOWN(int num, int wait) {
  clearDisplay();
  for (int i = num; i >= 0; i--) {
    writeDigit(0, (i / 100) % 10);
    writeDigit(1, (i / 10) % 10);
    writeDigit(2, (i / 1) % 10);
    strip.show();
    delay(wait);
  }
}

void disp_Animation() {
  clearDisplay();
  //UP-DOWN
  for (int i = 0; i < 7; i++) {
    for (int n = 0; n < PIXELS_DIGITS; n++) writeSegment(n, 5);
    strip.show();
    delay(100);
    for (int n = 0; n < PIXELS_DIGITS; n++) writeSegment(n, 0);
    strip.show();
    delay(100);
    for (int n = 0; n < PIXELS_DIGITS; n++) writeSegment(n, 2);
    strip.show();
    delay(100);
    for (int n = 0; n < PIXELS_DIGITS; n++) writeSegment(n, 0);
    strip.show();
    delay(100);
    for (int n = 0; n < PIXELS_DIGITS; n++) writeSegment(n, 5);
    strip.show();
    delay(100);
  }
  //LEFT-RIGHT
  for (int i = 0; i < 5; i++) {
    for (int n = 0; n < PIXELS_DIGITS; n++) {
      writeSegment(n, 6);
      strip.show();
      delay(150);
    }
    for (int n = PIXELS_DIGITS - 1; n >= 0; n--) {
      writeSegment(n, 3);
      strip.show();
      delay(150);
    }
    clearDisplay();
    for (int n = 0; n < PIXELS_DIGITS; n++) {
      writeSegment(n, 1);
      strip.show();
      delay(150);
    }
    for (int n = PIXELS_DIGITS - 1; n >= 0; n--) {
      writeSegment(n, 4);
      strip.show();
      delay(150);
    }
    clearDisplay();
  }
  //ZIG-ZAG
  for (int i = 0; i < 5; i++) {
    for (int n = 0; n < PIXELS_DIGITS; n++) {
      writeSegment(n, 6);
      strip.show();
      delay(125);
      clearDisplay();
      writeSegment(n, 1);
      strip.show();
      delay(125);
      clearDisplay();
      writeSegment(n, 4);
      strip.show();
      delay(125);
      clearDisplay();
      writeSegment(n, 3);
      strip.show();
      delay(125);
      clearDisplay();
    }
  }
}

void writeDigit(int index, int val) {
  byte digit = digits[val];
  for (int i = 6; i >= 0; i--) {
    int offset = index * (PIXELS_PER_SEGMENT * 7) + i * PIXELS_PER_SEGMENT;
    uint32_t color;
    if (digit & 0x01 != 0) {
      if (val == 1) color = strip.Color(50, 0,  0);
      if (val == 2) color = strip.Color(50, 50, 0);
      if (val == 3) color = strip.Color(50, 0, 50);
      if (val == 4) color = strip.Color(0, 50,  0);
      if (val == 5) color = strip.Color(0, 50, 50);
      if (val == 6) color = strip.Color(0,  0, 50);
      if (val == 7) color = strip.Color(50, 25, 0);
      if (val == 8) color = strip.Color(25, 5, 75);
      if (val == 9) color = strip.Color(75, 25, 5);
      if (val == 0) color = strip.Color(5, 75, 25);
    }
    else
      color = strip.Color(0, 0, 0);

    for (int j = offset; j < offset + PIXELS_PER_SEGMENT; j++) {
      strip.setPixelColor(j, color);
    }
    digit = digit >> 1;
  }
}

void writeSegment(int index, int val) {
  byte seg = segments[val];
  for (int i = 6; i >= 0; i--) {
    int offset = index * (PIXELS_PER_SEGMENT * 7) + i * PIXELS_PER_SEGMENT;
    uint32_t color;
    if (seg & 0x01 != 0) {
      if (val == 0) color = strip.Color(50, 0, 0);
      if (val == 1) color = strip.Color(0, 50, 50);
      if (val == 2) color = strip.Color(0, 50, 0);
      if (val == 3) color = strip.Color(50, 0, 50);
      if (val == 4) color = strip.Color(50, 50, 50);
      if (val == 5) color = strip.Color(0, 0, 50);
      if (val == 6) color = strip.Color(50, 50, 0);
    }
    else
      color = strip.Color(0, 0, 0);

    for (int j = offset; j < offset + PIXELS_PER_SEGMENT; j++) {
      strip.setPixelColor(j, color);
    }
    seg = seg >> 1;
  }
}
This is the code for testing the panel working, Uncomment the below given lines one by one and test the code accordingly.
//disp_Seg(200);                // Cycle through all segments        (DelayTime)
  disp_Digits(1000);            // Show digits from 0-9              (DelayTime)
  //disp_Animation();             // Show some Animations with the segments
 //disp_CountUP(500, 450);       // Count numbers in Ascending order  (NUMBER, DelayTime)
//  disp_CountDOWN(500, 250);     // Count numbers in Descending order (NUMBER, DelayTime)
Final tests:
dg.gif
caption (optional)
