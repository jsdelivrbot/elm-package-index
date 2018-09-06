# elm-geometric-transformation
An elm package for performing geometric transformations on points

The following 2D transformations are supported:
* Identity
* Rotate
* Scale
* Shear
* Translate

API documentation can be found on [elm-packages](http://package.elm-lang.org/packages/benansell/elm-geometric-transformation/latest)

## Quick Start
To rotate a any shape (list of points) by an angle around the origin:

```elm
    rotateShape : List Point -> Float -> List Point
    rotateShape points angle =
        let
            transform =
                rotate Clockwise angle
                    |> apply
        in
            List.map transform points
```

More complicated transforms can be created by using combine as demonstrated in the working example.


## Working Example
This package was used to animate the Elm logo as part of the [elm-webpack-seed](https://github.com/benansell/elm-webpack-seed)
