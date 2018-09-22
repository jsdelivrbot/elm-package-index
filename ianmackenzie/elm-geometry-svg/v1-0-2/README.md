## elm-geometry-svg

_Release notes for version 1.0 (relative to `opensolid/svg` 3.0) are
[here](https://github.com/ianmackenzie/elm-geometry-svg/releases/tag/1.0.0)._

This [Elm](http://elm-lang.org) package provides functions to create and
manipulate SVG elements using the [`elm-geometry`](http://package.elm-lang.org/packages/ianmackenzie/elm-geometry/latest)
data types. You can:

  - Draw 2D `elm-geometry` objects as SVG
  - Apply `elm-geometry`-based 2D transformations to arbitrary SVG elements
  - Convert SVG between different coordinate systems

## Drawing

The `lineSegment2d`, `triangle2d`, `polyline2d`, `polygon2d`, `circle2d`,
`ellipse2d`, `arc2d`, `ellipticalArc2d`, `quadraticSpline2d`, `cubicSpline2d`,
and `boundingBox2d` functions all produce standard [`Svg msg`](http://package.elm-lang.org/packages/elm-lang/svg/latest/Svg#Svg)
values that can be included in any SVG diagram:

![lineSegment2d](https://opensolid.github.io/images/svg/1.0/lineSegment2d.svg)
![triangle2d](https://opensolid.github.io/images/svg/1.0/triangle2d.svg)
![polyline2d](https://opensolid.github.io/images/svg/1.0/polyline2d.svg)
![polygon2d](https://opensolid.github.io/images/svg/1.0/polygon2d.svg)
![circle2d](https://opensolid.github.io/images/svg/1.0/circle2d.svg)

The appearance of the resulting elements can be customized by adding SVG
attributes such as `fill` and `stroke`.

## Transformation

The `scaleAbout`, `rotateAround`, `translateBy` and `mirrorAcross` functions
behave just like their standard `elm-geometry` counterparts. You can use them to do
things that would be difficult to do using just SVG, such as mirror a fragment
of SVG across an arbitrary axis:

![scaleAbout](https://opensolid.github.io/images/svg/1.0.2/scaleAbout.svg)
![rotateAround](https://opensolid.github.io/images/svg/1.0.2/rotateAround.svg)
![translateBy](https://opensolid.github.io/images/svg/1.0/translateBy.svg)
![mirrorAcross](https://opensolid.github.io/images/svg/1.0/mirrorAcross.svg)

Note that these functions will work on *any* `Svg msg`, value, not just ones
that happen to have been produced with this package! So you can use them as a
convenient way to transform SVG that you've produced in some other way.

## Coordinate conversion

The `relativeTo` and `placeIn` functions allow you to take SVG defined in one
coordinate system and convert it to another. For example, you can take SVG
defined in a model coordinate system where (0,0) is the center and positive Y is
up, and use `relativeTo` to convert it into SVG in window coordinates for
display, where (0,0) is the top left corner and positive Y is down.

`placeIn` is useful for 'instancing' or 'stamping' a fragment of SVG in many
different positions with different orientations:

![placeIn](https://opensolid.github.io/images/svg/1.0/placeIn.svg)

## Installation

Assuming you have [installed Elm](https://guide.elm-lang.org/install.html) and
started a new project, you can install `elm-geometry-svg` by running

```
elm install ianmackenzie/elm-geometry-svg
```

in a command prompt inside your project directory.

## Documentation

[Full API documentation](http://package.elm-lang.org/packages/ianmackenzie/elm-geometry-svg/1.0.0/Geometry-Svg)
is available.

## Questions? Comments?

Please [open a new issue](https://github.com/ianmackenzie/elm-geometry-svg/issues)
if you run into a bug, if any documentation is missing/incorrect/confusing, or
if there's a new feature that you would find useful (although note that this
package is not meant to be general-purpose full-blown SVG package, more just a
convenient way to render `elm-geometry` values). For general questions about
using this package, try:

  - Joining the **#geometry** or **#svg** channels on the [Elm Slack](http://elmlang.herokuapp.com/),
    or sending me (**@ianmackenzie**) a message - even if you don't have any
    particular questions right now, it would be great to know what you're hoping
    to do with the package!
  - Posting to the [Elm Discourse](https://discourse.elm-lang.org/) forums
  - Or if you happen to be in the New York area, come on out to the
    [Elm NYC meetup](https://www.meetup.com/Elm-NYC/) =)

You can also find me on Twitter ([@ianemackenzie](https://twitter.com/ianemackenzie)),
where I occasionally post `elm-geometry`-related stuff like demos or new
releases. Have fun, and don't be afraid to ask for help!
