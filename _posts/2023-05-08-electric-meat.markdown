---
layout: post
title:  "Electric Meat"
date:   2023-05-08 22:40:30 -0500
categories: general
---

# Electric Meat
Mutual capactive touch silicone skin for the ESP-32 and Arduino

<iframe width="560" height="315" src="https://www.youtube.com/embed/4seEMUTgprs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

![DSCF9061.JPG](/MeMakey/assets/final/DSCF9061.JPG)
Layout

![DSCF9067.JPG](/MeMakey/assets/final/DSCF9067.JPG)
Close up of silicone skin

## Creative Vision
What is skin and why do we need it? This piece explores the themes of contact and what it means to exist. We're all just walking and talking bags of skin after all. Upon contact, the silicone skin will cause the machine to scream depending on how large the contact area and how big the pressure at which you touch the skin. Actions such as slapping, punching, stroking, clawing, atc. are all welcome.

This piece is rather lighthearted and silly, but has the potential to be much more. The silicone skin, once built, is basically just a touch screen that feels like flesh and could have many potential applications in human-computer interaction, interactive design, accessible technology etc. I built the skin and programmed it to scream, but you can take it and do much more.

## Overview
I created an artificial silicone skin that could be used for interfacing and interacting. It operates using mutual capactive touch, where there are two layers of criss-crossing conductive threads that hold a charge. The charge changes upon the presence of human touch. The silicone skin is able to measure the location of the touch as well as the size. The pressure can also be inferred from the relative change in charge, with more pressure leading to a larger disturbance of the electric field.

Having built the skin, I ran computer vision and blob detection to find instances of where the skin has been touched. I decided to make the machine scream depending on how much force the skin has been subject to; a greater pressure often also leads to greater area. There are different screams that can be triggered depending on which part of the skin has been touched and the pressure corresponds to the volume of the screams. 

I have built two versions of this project, one on an ESP-32 and another on the Arduino. As the ESP-32 only has 7 capactive touch sensors, I was limited by this number and could only create a simple 3*3 grid for 9 different sensing points on the ESP-32. This led to an image like this: (see below)

![IMG_5492.jpg](/MeMakey/assets/final/IMG_5492.jpg)
3*3 sensing grid on the ESP-32

Through random luck, I did happen to find a break-out board that supported up to 12 capacitive touch sensors lying around, so I decided to use that instead. Due to unknown reasons, I could not get this breakout board (linked [here](https://www.adafruit.com/product/1982) to work (I think the problem had to do with the ESP-32's I2C protocol being a little wonky), so I decided to use an Arduino (which did work) instead.

With 12 capactive touch sensors, I could have a 6*6 grid with 36 distinct points to sense from. This increase allowed much greater flexibility and sensitivity in getting the skin to function as a touch screen and pinpoint touch locations and pressure.

## Setup
A lot of this heavily referenced the tutorial [here](https://marcteyssier.com/projects/skin-on/). 
### Materials
**Skin** 
- Dragon Skin™ 10 VERY FAST Platinum Cure Silicone 
- Conductive thread (I used the Kookye's silver thread, but Adafruit's 2 ply conductive thread should also work)
- Silc Pig - Pigments for Tin & Platinum Silicone Rubbers - 9-Pack Color Sampler
- Flocking Kit for Special FX Makeup Gelatin & Silicone

**Hardware** 
- Arduino Uno 
- Adafruit 12-Key Capacitive Touch Sensor Breakout - MPR121
 or just
- ESP-32


## How to make the skin
The silicone pigment and flocking kit were used to make the silicone skin loook flesh-like, they can be optional if realism is not your end goal.

First, I mixed the silicone and added pigment and flocking to get it to resemble human skin. There are a lot of tutorials for this online, the key is to add tiny bits of green and brown paint to tint the flesh colored base and add red, blue, green flocking to get the undertones of human skin. I then spread this very thinly on a layer of cardboard to make it matte. I had discovered from my previous trials that silicone doesn't really stick to cardboard and can be easily peeled off once cured. The same cannot be said for paper. This first layer is done without any conductive threads embedded within it so that the threads will not show through.

![IMG_5415.jpg](/MeMakey/assets/final/IMG_5415.jpg)
First layer of silicone

Then I measured out even distances on the cardboard and used a needle to thread conductive thread through to create lines for one axis of the grid. I made 6 rows because the sensor I had had 12 sensors, so a 6*6 grid would work well.

![IMG_5415.jpg](/MeMakey/assets/final/IMG_5428.jpg)
Conductive thread layout 1

I poured silicone thinly again to cover the threads and then layed on the second layer of threads that were perpendicular to the threads before.

![IMG_5431.jpg](/MeMakey/assets/final/IMG_5431.jpg)
Conductive thread layout 2

Finally, I used a final layer of silcone to seal the threads in. After curing, the back of the silicone skin mat looks like this:

![DSCF9064.JPG](/MeMakey/assets/final/DSCF9064.JPG)
Back of silicone skin

## Hardware and technology
After making the silicone skin, I soldered the 12 conductive threads to the capacitive touch pins on the breakout board. [These](https://learn.adafruit.com/adafruit-mpr121-12-key-capacitive-touch-sensor-breakout-tutorial/wiring) instructions are pretty helpful for setting up the 12-key breakout board.

![DSCF9072.JPG](/MeMakey/assets/final/DSCF9072.JPG)
Hardware setup

The breakout board sends the level of each sensor back to the Arduino. Note that because I chose to use mutual capactance sensing here, I did not choose to use touch interupts because the sensors would constantly have a signal as they being used as charged capacitors. Instead, I made the breakout board send all the values of all the sensors at once, and through comparing the differences in charge, I would be able to figure out which parts on the siliicone skin had been touched. 

The Arduino code sends the values of each of the 12 sensors through the serial protocol back to my computer, where I use Processing to process the values. I multiply the x values and y values respectively with each other to get 36 values for 36 points. This is stored in an integer array of size 36. This calculation is done in the getRawPoints() function.

`
int[] getRawPoints(int[] vals){
  int[] xvalues = new int[xvals];
    int[] yvalues = new int[yvals];
    for (int i=0; i<yvals; i++) {
      yvalues[i] = vals[5-i]/4;
    }
    for (int i=0; i<xvals; i++) {
      xvalues[i] = vals[6+i]/4;
    }
    int k = 0;
    for (int i = 0; i < yvals; i++) {
      for (int j = 0; j < xvals; j++) {
        rawPoints[k] = yvalues[i]*xvalues[j];
        k++;
      }
    }
    return rawPoints;
}
`

I also have another array that stores the average of 20 reads for calibration. Through comparing the raw points with the averaged points, I can find out which sensors have changed in value. I also have variables MIN_THRESHOLD and MAX_THRESHOLD that can be changed by the user depending on the sensitivity of the sensors. These thresholds determine how big of a change in charge will be detected as a touch.
`
  for (int i = 0; i < squares; i++) {
    int temp = avgPoints[i]-rawPoints[i];
    temp = constrain(temp, MIN_THRESHOLD, MAX_THRESHOLD);
    adjPoints[i] = int(map(temp, MIN_THRESHOLD, MAX_THRESHOLD, 0, 255)); //map to grayscale
  }
`

For the visualization, I have a drawSquares function that visualizes the grid by drawing squares corresponding to the sensing grid. The brightness of the square is determined by the amount of change in the values sensed at that location.

`
void drawSquares() {
  skinGraphic.beginDraw();
  skinGraphic.noStroke();
  int k = 0;
  for (int i = 0; i < xvals*squareSize; i+=squareSize) {
    for (int j = 0; j < yvals*squareSize; j+=squareSize) {
      //println(adjPoints[k]);
      skinGraphic.fill(adjPoints[k]);
      k++;
      skinGraphic.rect(j, i, squareSize, squareSize);
    }
  }
  skinGraphic.endDraw();
}
`

Finally, I ran the image produced through [computer vision](https://github.com/atduskgreg/opencv-processing) and [blob detection](http://www.v3ga.net/processing/BlobDetection/) to find shapes and then use the size of the shapes to determine how loudly the computer will scream.

## Further thoughts and reflections
The breakout board that I used for the skin used I2C to communicate with the microcontroller (the Arduino in this case). I could connect multiple

For my purposes, the computer vision and blob detection might have been a little unnecessary because I could have just used the area of the bright squares to calculate how loudly the computer should scream. However, I still chose to implement it because 

## Technology
My complete arduino code can be found on my Github [here](https://github.com/SamIAm2000/CS-3930-Creative-Embedded-Systems/tree/main/Module_1).

The basic structure of the program is 
1. The Intro Sequence, where the program wakes up and comes into being from an infinitude of stars
2. The Main Sequence, where the program randomly flashes 20 different states of being, "I am ..."
3. The Ending Sequence, where the program falls back asleep and says farewell.

The randomly 20 States of Being are stored in a struct `msg_numbers` where each number corresponds to a State. See below:

{% highlight ruby %}
static struct {
  int num; 
  char *msg;
} msg_numbers[] = {
	{ 1, "floating" },
	{ 2, "drifting" },
	{ 3, "hanging" },
	{ 4, "spinning" },
	{ 5, "trying" },
	{ 6, "existing" },
	{ 7, "wondering" },
	{ 8, "wandering" },
	{ 9, "doing my best" },
	{ 10, "thinking" },
	{ 11, "not thinking" },
	{ 12, "playing" },
	{ 13, "happy" },
	{ 14, "ecstatic" },
	{ 15, "in pain" },
  { 16, "hungry" },
  { 17, "coding" },
  { 18, "struggling" },
  { 19, "weary" },
  { 20, "tired" },
};
{% endhighlight %}

The randomly generated states of being come from an array that has the numbers 1-20 randomly shuffled using the random number generator. I chose to use an array instead of just randomly generated numbers form 1-20 so that there would be no repetition. See below:


{% highlight ruby %}
//shuffle array to get 20 non repetitive random numbers
int array[20];
for (int i = 0; i < 20; i++) {     // fill array
  array[i] = i+1;
}
for (int i = 0; i < 20; i++) {    // shuffle array
  int temp = array[i];
  Serial.println(array[i]);
  int randomIndex = rand() % 20;
  array[i] = array[randomIndex];
  array[randomIndex] = temp;
}

//loops 20 times to print different states of being
for (int i = 0; i < 20; i++){
  randNum = array[i];
  msg = get_msg(randNum);
  sprite_print(msg);
  do_motion(randNum);
  delay(500);

  //clear text
  stext.drawString("                                           ", WIDTH/2, HEIGHT/2, 4);
  stext.pushSprite(0, 0);
}
{% endhighlight %}

Some of the states also had subtext that came along with them such as `beep boop 00101011010` that would be returned whenever the screen displayed "I am coding." This I put into another function called do motion. I was originally going to include motions such as rotating the screen etc. that triggered when a certain number was called, but I did not end up implementing it. If one were to wish to do so, they could just alter the code in the do_motion function:

{% highlight ruby %}
void do_motion(int n){
  delay(500);
  char * msg = NULL;
  if (n==0){
    msg = "hello world :0";
    draw_mini_text(msg); 
  } else if (n == 3){
    msg = "please don't let me fall";
    draw_mini_text(msg); 
  } else if (n == 4){
    msg = "weeeeeeeeee";
    draw_mini_text(msg); 
  } else if (n == 7){
    msg = "hmmmmmmm";
    draw_mini_text(msg); 
  } else if (n == 8){
    msg = "what a beautiful world";
    draw_mini_text(msg); 
  } else if (n == 13){
    msg = ":)";
    draw_mini_text(msg); 
  } else if (n == 15){
    msg = ":(";
    draw_mini_text(msg); 
  } else if (n == 17){
    msg = "beep boop 00101011010";
    draw_mini_text(msg); 
  }  
}
{% endhighlight %}

## Further Notes to Consider
Orientation: the orientation of the board can be adjusted using the rotate function, but because of last minute changes in the plan (i.e. boards frying and batteries smoking), my board had to be displayed vertically. Use the function rotate(#) to rotate the board.

