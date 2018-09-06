# sha256 and sha224 cryptographic hash functions for Elm

A simple wrapper around some JavaScript.

Thank you to Yi-Cyuan Chen for the JavaScript ([github.com/emn178/js-sha256](https://github.com/emn178/js-sha256)).

It's as simple as:

    import Sha256 exposing (sha256)

    hash = sha256("foo")

`example.elm` contains a simple example. To run it:

    cd .../elm-sha256
    elm-reactor

Then aim your browser at [localhost:8000/example.elm](http://localhost:8000/example.elm).
