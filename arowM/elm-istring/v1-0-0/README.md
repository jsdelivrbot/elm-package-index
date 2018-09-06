# elm-istring

[![Build Status](https://travis-ci.org/arowM/elm-istring.svg?branch=master)](https://travis-ci.org/arowM/elm-istring)

## Summary

A module to handle difficulty of using `lazy` with `onInput` attribute.

## When should we use this?

If you use `Html.Lazy.lazy` (or `lazy2`, `lazy3`, etc...) to a view function it fires `Html.Events.onInput`,
you should give a thought to use `IString`.

Let's see the particular situation that have to use `IString`.
Here is a excerpt of the [example code](./example/src/Strict.elm) in [`/example` directory](./example).

```
type alias Model =
    { ...
    , value : String
    }


type Msg
    = ...
    | UpdateValue String


update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        ...

        UpdateValue str ->
            ( { model
                | value = normalize str
              }
            , Cmd.none
            )


view : Model -> Html Msg
view model =
    div
        []
        [ button
            [ onClick IncDummy
            ]
            [ text "dummy event"
            ]
        , digitInput model.value
        ]


digitInput : String -> Html Msg
digitInput str =
    Debug.log "digitInput was called." <|
        input
            [ onInput UpdateValue
            , value str
            ]
            []
```

This code do just update `model.value` when `input` event fires on `input` tag.
The only thing, that is special, is it do some sort of "normalization" before updates.

```
{-| Filter only digit characters.

    normalize "s"
    --> ""

    normalize "9"
    --> "9"

    normalize "asl20la2"
    --> "202"

-}
normalize : String -> String
normalize =
    String.fromList << List.filter Char.isDigit << String.toList
```

As a result, only digit characters are shown on the `input` element.
All inputs except digit characters are discarded.

Let's assume that you pushed keyboard keys "s" "3" "a" sequentially.
The first "s" you entered does not affect at all because it is not a digit number.
After pushing second key "3", the input field shows "3" as it is the only digit character you pushed.
And finally, when you input "a", it still shows "3".

So, how about use `Html.Lazy.lazy` to reduce useless rerendering of `digitInput`.
I'll pick up a snippet from [`example/src/Lazy.elm`](example/src/Lazy.elm).

```
view : Model -> Html Msg
view model =
    div
        []
        [ button
            [ onClick IncDummy
            ]
            [ text "dummy event"
            ]
        , lazy digitInput model.value
        ]
```

It only changes to use `lazy` on `view` function.

Though it seems to successfully reduce some useless rerendering events, it changes the behavior of input field!
To ensure, enter "s" "3" "a", as previously we did.
If you enter "s", it shows "s" on input field and the "s" remains till you push "3".
Furthermore, if you input "a" after that, "3a" is shown on the input field...

This is because `input` tags have some sort of their own state.
Initially, `model.value` is `""`, and the input `tag` has state `""`.
How it goes after entering "s"?
The `normalize` function convert it to `""`, it is as same as initial value, and `lazy` decides not to rerender `digitInput`.
On the other hand, the `input` tag changes its state `""` to `"s"`, and it is not overwritten by Elm because `digitInput` does not fire.
This causes the strange behavior mentioned above.

The `IString` solves this problem.
You only have to replace the type of `model.value` `String` with `IString`.
See [example](example/src/Lazy2.elm) for details.
