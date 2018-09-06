# Elm CDN

# About

Provides quick, convenient access to the frameworks you want to grab from a CDN.

### Pros

* Easy to use.
* Works with elm-reactor.
* Great for fast jobs and experiments.

### Cons

* Not loaded optimally. By the time you're writing your own HTML file,
  instead of letting `elm-make` generate one for you, it's time to
  move on.

## Installing

``` sh
$ elm package install krisajenkins/elm-cdn
```

## Using

Just put the stylesheet you want in your top-level view function, like so:

```
rootView : Model -> Html Msg
rootView model =
    div []
        [ CDN.bootstrap.css
        , ...
        ]
```

## Building

Just call `make`.

## License

Copyright © 2016 Kris Jenkins

Distributed under the MIT license.
