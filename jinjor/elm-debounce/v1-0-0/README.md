# elm-debounce

Yet another debouncer for Elm.

## How to use

This library follows the Elm Architecture. See the full example [here](https://github.com/jinjor/elm-debounce/blob/master/examples/Main.elm).

```elm
init : ( Model, Cmd Msg )
init =
  { value = ""
  -- Initialize the debouncer.
  -- Choose the strategy for your use case.
  , debounce =
      Debounce.init
        { strategy = Debounce.later (1 * second)
        , transform = DebounceMsg
        }
  , report = []
  } ! []
```

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Input s ->
      let
        -- Push your values here.
        (debounce, cmd) =
          Debounce.push s model.debounce
      in
        { model
        | value = s
        , debounce = debounce
        } ! [ cmd ]

    -- This is where commands are actually sent.
    -- The logic can be dependent on the current model.
    -- You can also use all the accumulated values.
    DebounceMsg msg ->
      let
        (debounce, cmd) =
          Debounce.update
            (Debounce.takeLast save)
            msg
            model.debounce
      in
        { model | debounce = debounce } ! [ cmd ]

    ...
```

### LICENSE

BSD3
