# elm-porter

Port message manager that emulate a request-response style communication, a'la `Http.send Response request`.

## How it works

```elm
port module Main exposing (..)

import Porter
import Html exposing (text)
import Json.Encode as Encode
import Json.Decode as Decode

-- Configure Porter

port outgoing : Encode.Value -> Cmd msg
port incoming : (Decode.Value -> msg) -> Sub msg

porterConfig =
    { outgoingPort = outgoing
    , incomingPort = incoming
    -- Porter works with a single Request and Response data types. They can both be anything, as long as you supply decoders :)
    , encodeRequest = Encode.string
    , decodeResponse = Decode.string
    }

-- Application model

init =
  ( { porter = Porter.init
    , response = ""
    }
  -- Send a request through porter, specifying the response handler directly
  , Porter.send Receive "Reverse me!"
  )

-- Message includes Porter's message

type Msg
  = PorterMsg Porter.Msg
  | Receive String

-- Update porter accordingly

update msg model =
  case msg of
    PorterMsg porterMsg ->
      { model | porter = Porter.update porterConfig porterMsg model.porter }

    Receive response ->
      { model | response = response }

-- Set up porter's subscriptions

subscriptions model =
  Porter.subscriptions porterConfig

-- Any view you like

view model =
  text (toString model)

-- 
main =
  program
    { init = init
    , update = update
    , subscriptions = subscriptions
    , view = view
    }
```

## Handling messages in JS

Outgoing messages into JavaScript as JSON, with an id that Porter generates and uses to match up with response handlers.

```js
ports.outgoing.subscribe(msgWithId => {
  const id = msgWithId.id
  const request = msgWithId.msg
  const response = request.reverse()
  // Simply include the same `id` and the response under the `msg` key.
  ports.incoming.send({
    id: id,
    msg: resonse
  })
})
```