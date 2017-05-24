## Overview

[![steering a car wioth PID](http://img.youtube.com/vi/HIDspIf4IXY/0.jpg)](https://www.youtube.com/watch?v=HIDspIf4IXY)

This project uses PID controller to steer a car in simulator. Program always knows how far the car from the center of the road. And goal is to keep car in the center of the road.

## PID controller

PID is a very simple controller that uses 3 parameters to keep your degree of freedom at specified value. For our car it means to keep
cross track error (cte) as small as possible.

```
cte = center_of_road_position - car_position
 # calculate control
steering_value = - (P*cte + I*sum\_of\_all\_cte + D*time\_derivative\_of\_cte)
```

As you can see PID controller consist of three terms:

"P*cte" is known as proportional term. It applyes control to the opposite direction of appearing error. If we will control only with this term our car will operate like this

[![steering with only P term](http://img.youtube.com/vi/U_g_2mFFd-4/0.jpg)](https://www.youtube.com/watch?v=U_g_2mFFd-4)

So, car oscillates around center of the road. That's why we need "D*time\_derivative\_of\_cte" called differential term which will smooth oscillations and car can drive succesfully.

[![steering with P and D term](http://img.youtube.com/vi/YLinnu6SzOI/0.jpg)](https://www.youtube.com/watch?v=YLinnu6SzOI)

You might also need "I*sum\_of\_all\_cte" - integral term if you have some constant deviation force in your environment. For car it may be some copnstant wind while driving on the straight lanes.
We have not wind and straight lanes in our simulator, so we don't need integral term.

## Parameters optimization

You also need to know how to choose PID parameters for your task. So there are different possible ways:

* Optimization by hand. It is the most easiest way. For higher car speed you need to choose parameters more precisely.
* [Some classical methods](https://en.wikipedia.org/wiki/PID_controller#Overview_of_methods)
* [Twiddle algorithm](https://martin-thoma.com/twiddle/)
* [Gradient descent](https://en.wikipedia.org/wiki/Gradient_descent)
* Your method :)

## My solution

I have optimized my PID parameters by hand. Also for handling higher car velocity I use couple of tricks:

* Speed factor. PID tuned for 30 Mph shows less performance on 60-70 Mph. So I used very basic velocity adoptaion parameter.

```
double speed_factor = 31.0 / (speed + 1.0);
steer_value = speed_factor * pid_value;
```

* Car speed PID controller. If CTE becomes big I slow down the car with second PID controller. It lets the car slows down on strongly curved turns.

```
pid_speed.Init(0.4, 0, 3.5);
double throttle = 0.9 + pid\_speed\_value;
```

This allows car speed up to 60-70 Mph at some parts of the track.

PID controller also have disadvantages. It has no information about device it controls. Need very precise tuning and higher update rates on higher speeds.
Also it have no information about future environment states. Human can observe future curved turn and can adapt behaviour with respect to it. PID controller
always deals with present errors and cannot control with respect to future circumstances.

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets) == 0.13, but the master branch will probably work just fine
  * Follow the instructions in the [uWebSockets README](https://github.com/uWebSockets/uWebSockets/blob/master/README.md) to get setup for your platform. You can download the zip of the appropriate version from the [releases page](https://github.com/uWebSockets/uWebSockets/releases). Here's a link to the [v0.13 zip](https://github.com/uWebSockets/uWebSockets/archive/v0.13.0.zip).
  * If you run OSX and have homebrew installed you can just run the ./install-mac.sh script to install this
* Simulator. You can download these from the [project intro page](https://github.com/udacity/CarND-PID-Control-Project/releases) in the classroom.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`

