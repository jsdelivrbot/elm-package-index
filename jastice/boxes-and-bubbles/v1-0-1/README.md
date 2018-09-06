Boxes and Bubbles
=================

A simple-as-possible 2D physics rigid-body physics engine for Elm.
Supports only bubbles (circles) and boxes (axis-aligned rectangles).

Here's [an example](http://jastice.github.io/boxes-and-bubbles/) ([source](https://github.com/jastice/boxes-and-bubbles/blob/master/src/Example.elm)) of the engine in action.

It does this:

* resolve collisions between bodies of different mass and bounciness.
* gravity (ignores mass) / global time-varying forces (mass-dependent)

It doesn't do:

* arbitrary polygons
* friction / drag
* rotation
* time-integrated movement
* graphics
* colliding unstoppable forces with immovable objects (infinite masses will be glitchy)
