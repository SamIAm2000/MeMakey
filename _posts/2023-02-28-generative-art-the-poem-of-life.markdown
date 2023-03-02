---
layout: post
title:  "Generative Art: The Poem of Life"
date:   2023-02-28 21:03:34 -0500
categories: general
---

# The Poem of Life
Generative art created on an ESP-32.

<iframe width="560" height="315" src="https://www.youtube.com/embed/4seEMUTgprs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

![esps_in_the_dark](/MeMakey/assets/esps_in_the_dark.jpeg)
ESPs in the dark

![i_am_struggling](/MeMakey/assets/i_am_struggling.jpeg)
The one above that says "I am struggling" is mine.

## Creative Vision
I wanted to envision my ESP-32 as a living being that experienced emotions and random happenings in life. It would start up, experience things, rest, and then repeat again. The waking and sleeping of the machine also serves as a metaphor for life where we are born, get involved in a series of random, spontaneous happenings throughout our lives, and then we die.

The installation space would have other ESP-32s in the same space, so I thought that making my board imitate a living being would be a good way to interact with the other boards and invite viewers to regard these small specks that light up as individual lives flashing in front of them. Each board is merely a speck amongst the multitude, just as in the ending sequence of my program, there are stars to show that's where come from and what we'll eventually return to.

## Setup
Since our batteries were malfunctioning and we could not set up the ESP-32s independently without a battery, our professor very generously dedicated his time to building a board that could connect up to 15 ESP-32s to a power source. They are connected via USB-C connectors. It looks like this:

![empty_board](/MeMakey/assets/empty_board.jpg)

## Tech and Codey Part
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

