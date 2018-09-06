# joakin/elm-canvas

This library exposes a low level API that mirrors most of the DOM canvas API to
be used with Elm in a declarative way.

<p align="center">
  <a href="https://joakin.github.io/elm-canvas">Live examples</a> (<a href="https://github.com/joakin/elm-canvas/tree/master/examples">sources</a>)
</p>

<p align="center">
  <img width="200" src="https://joakin.github.io/elm-canvas/particles.png" />
  <img width="200" src="https://joakin.github.io/elm-canvas/animated-grid.png" />
  <img width="200" src="https://joakin.github.io/elm-canvas/dynamic-particles.png" />
  <img width="200" src="https://joakin.github.io/elm-canvas/circle-packing.png" />
  <img width="200" src="https://joakin.github.io/elm-canvas/trees.png" />
</p>

---

**WARNING**: This library is in development and right now it only exposes a
low-level API that mirrors the DOM API, providing a bit of extra type safety
where it makes sense. The DOM API is highly stateful and side-effectful, so be
careful. To understand how to use this library for now, please familiarize
yourself with the
[MDN Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial).
We will be iterating on the design of the library to make it nicer and provide
better defaults as time goes by, and make specific tutorials with Elm.

## Usage

To use it, remember to include the `elm-canvas` custom element script in your
page before you initialize your Elm application.

- <http://unpkg.com/elm-canvas/elm-canvas.js>
  - CDN link. Visit it and copy the redirected URL with the version number into
    your HTML.
- <http://npmjs.com/package/elm-canvas>
  - See docs for version compatibility, in general, latest npm package should
    work fine with the latest elm package.

Then, you can add your HTML element like this:

```elm
module Main exposing (main)

import Html exposing (Html)
import Canvas

view : Html msg
view =
    let
        width = 500
        height = 300
    in
        Canvas.element
            width
            height
            [ style [ ( "border", "1px solid black" ) ] ]
            ( Canvas.empty
                |> Canvas.clearRect 0 0 width height
                |> renderSquare
            )

renderSquare cmds =
    cmds
        |> Canvas.fillStyle (Color.rgba 0 0 0 1)
        |> Canvas.fillRect 0 0 100 50

main = view
```

## Examples

You can see many examples in the
[examples/](https://github.com/joakin/elm-canvas/tree/master/examples) folder,
and experience them [live](https://joakin.github.io/elm-canvas).

```

```
