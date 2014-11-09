/* OpenProcessing Tweak of *@*http://www.openprocessing.org/sketch/47629*@* */
/* !do not delete the line above, required for linking your tweak if you upload again */
// Manuela Donoso
// NYU ITP class: ICM fall 2011
// Final Project: Fish Tank
// Based on Daniel Shiffman <http://www.shiffman.net > Flocking code

// Mouse pressed to call fish to mouse

import processing.serial.*;


// The serial port:
Serial myPort;     

boolean screenSet=true;

ArrayList circles = new ArrayList();

int byteCounter = 0;

//simpleColor

import processing.video.*;

// XY coordinate of closest color
int closestX = 0;
int closestY = 0;

// Variable for capture device
Capture video;
PImage camMirror;

// A variable for the color we are searching for.
color trackColor; 

float trackAccuracy = 10;



Flock flock;

void setup() {
  size(1280, 720);
  //simpleColor
  video = new Capture(this, width, height);
  camMirror = new PImage(video.width, video.height);
  video.start();

  // Start off tracking for red
  trackColor = color(255, 0, 0);

  flock = new Flock();
  // Add an initial set of boids into the system
  for (int i = 0; i < 50; i++) {
    Boid b = new Boid(random(width), random(height), random(30, 100));
    flock.addBoid(b);
  }
  smooth();

  // List all the available serial ports:
  println(Serial.list()[5]);

  // Open the port you are using at the rate you want:
  myPort = new Serial(this, Serial.list()[5], 9600);
}

void captureEvent(Capture video) {
  // Read image from the camera
  video.read();
}


void draw() {
  background(#3B84F5);

  video.loadPixels();


  for (int x = 0; x < video.width; x++) {
    for (int y = 0; y < video.height; y++) {
      camMirror.pixels[x+y*video.width] = video.pixels[(video.width-(x+1))+y*video.width];
    }
  }
  camMirror.updatePixels();

  if (screenSet) {

    image(camMirror, 0, 0);
  }

  while (myPort.available () > 0) {
    int inByte = myPort.read();
    byteCounter++;

    if (byteCounter>100)
    {
      circles.add(new ExpandingCircle(closestX, closestY, true));
      byteCounter=0;
    }
  }

  flock.run();

  // Before we begin searching, the "world record" for closest color is set to a high number that is easy for the first pixel to beat.
  float worldRecord = 500; 



  // Begin loop to walk through every pixel
  for (int x = 0; x < camMirror.width; x ++ ) {
    for (int y = 0; y < camMirror.height; y ++ ) {
      int loc = x + y*camMirror.width;
      // What is current color
      color currentColor = camMirror.pixels[loc];
      float r1 = red(currentColor);
      float g1 = green(currentColor);
      float b1 = blue(currentColor);
      float r2 = red(trackColor);
      float g2 = green(trackColor);
      float b2 = blue(trackColor);

      // Using euclidean distance to compare colors
      float d = dist(r1, g1, b1, r2, g2, b2); // We are using the dist( ) function to compare the current color with the color we are tracking.

      // If current color is more similar to tracked color than
      // closest color, save current location and current difference
      if (d < worldRecord) {
        worldRecord = d;
        closestX = x;
        closestY = y;
      }
    }
  }

  // We only consider the color found if its color distance is less than 10. 
  // This threshold of 10 is arbitrary and you can adjust this number depending on how accurate you require the tracking to be.
  if (worldRecord < trackAccuracy) { 
    // Draw a circle at the tracked pixel
    fill(trackColor);
    strokeWeight(4.0);
    stroke(0);
    ellipse(closestX, closestY, 16, 16);
  }

  for (int i=0; i<circles.size (); i++) {
    ExpandingCircle ec = (ExpandingCircle) circles.get(i);
    ec.update();
    ec.display();
    if (ec.transparency <= 0) { 
      circles.remove(i);
    } // remove invisible circles
  }
}
// Add a new boid into the System
//void mousePressed() {
//  flock.addBoid(new Boid(mouseX,mouseY));
//}
void mousePressed() {
  // Save color where the mouse is clicked in trackColor variable
  int loc = mouseX + mouseY*camMirror.width;
  trackColor = camMirror.pixels[loc];
}

void keyPressed () {
  if (key == 'z' || key == 'Z') {
    screenSet=!screenSet;
  }
}


class ExpandingCircle {
  int x, y;
  float radius;
  color c;
  boolean transparencyOn;
  int transparency;

  ExpandingCircle(int x, int y, boolean transparencyOn) {
    this.x = x;
    this.y = y;
    this.transparencyOn = transparencyOn;
    c = color(random(255), random(255), random(255));
    transparency = 100;
  }

  void update() {
    radius++;
    if (transparencyOn && radius >= 50 && transparency > 0) { 
      transparency--;
    }
  }

  void display() {
    stroke(200, 220, 255, transparency);
    strokeWeight(10); 
    noFill();
    ellipse(x, y, radius, radius);
  }
}
