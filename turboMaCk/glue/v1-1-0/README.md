# Glue

[![Build Status](https://travis-ci.org/turboMaCk/glue.svg?branch=master)](https://travis-ci.org/turboMaCk/glue)

This package helps you to reduce boilerplate while composing TEA-based (The Elm Architecture) applications using
[`Cmd.map`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Cmd#map),
[`Sub.map`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Sub#map)
and [`Html.map`](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#map).
`Glue` is just thin abstraction over these functions so it's easy to plug it in and out.
It's fair to say that `Glue` is an alternative to [elm-parts](http://package.elm-lang.org/packages/debois/elm-parts/latest),
but uses a different approach (no better or worse) for composing isolated pieces/modules together.

**This package is highly experimental and might change a lot over time.**

Feedback and contributions to both code and documentation are very welcome.

## Important Note!

This package is not necessary designed for either code splitting or reuse but rather for **state separation**.
State separation might or might not be important for reusing certain parts of application.
Not everything is necessary stateful. For instance many UI parts can be express just by using `view` function
to which you pass `msg` constructors (`view : msg -> Model -> Html msg` for instance) and let consumer to manage its state.
On the other hand some things like larger parts of applications or parts containing a lot of self-maintainable stateful logic
can benefit from state isolation since it reduces state handling imposed on consumer of that module.
Generally it's good rule to always choose simpler approach (And using stateless abstraction is usually simpler) -
*If you aren't sure if you can benefit from extra isolation don't use it.* Always try to define as much logic as you can
using just simple functions and data. Then you can think about possible state separation in places where too much of it is exposed.
**First rule is to avoid breaking of [single source of truth principle](https://en.wikipedia.org/wiki/Single_source_of_truth)**.
If you find yourself synchronizing some state from one place to another than that state shouldn't be probably isolated in first place.


## tl;dr

This package is a result of my experience with building larger application in Elm where some modules lives in isolation from others.
The goals and features of this package are:

- Reduce boilerplate in `update` and `init` functions.
- Reduce [indirection](https://en.wikipedia.org/wiki/Indirection) in glueing between parent and child module.
- Define glueing logic on consumer level.
- Enforce common interface in `init` `update` `subscribe` and `view`.
- *You should read whole README anyway.*

## Downsides

There are certain down-sides to choosing glue over manual usage of `Cmd.map`, `Html.map` and `Sub.map`.

- I'm still thinking about how to work with views which takes multiple arguments where some are neither constant nor
part of parent's model. For instance you can use routing and have `Route` stored in very top model.
Then you can easily pass this route to child's view (since you can read it from parent's model).
However if that child module has another nested module whose view takes `Route` you have a problem.
It doesn't make sense to store route in every model in chain but `view` part of `Glue` has `model -> Html msg` type signature.
This means you can't chain this argument without storing it to every model in chain.
There are workarounds but it would be nice to have build-in solution for this.
- In 2.x.x it would be nice to have function `updateWith : Glue model subModel msg subMsg -> (subModel -> subModel) -> ( model, Cmd msg ) -> ( model, Cmd msg )`.
Unfortunetelly this will require breaking change in `Glue` and it's `glue` constructor. This idea is not verified and tested yet.


## Install

Is as you would expect...

```
$ elm-package install turboMaCk/glue
```

## Examples

The best place to start is probably to have a look at [examples](https://github.com/turboMaCk/glue/tree/master/examples).

In particular, you can find:

### [Transforming Isolated Elm Apps together using Glue](https://github.com/turboMaCk/glue/tree/master/examples/Counter)

### [Composing Modules with Subscriptions](https://github.com/turboMaCk/glue/tree/master/examples/Subscriptions)

### [Action Bubbling (Sending Actions from Child to Parent)](https://github.com/turboMaCk/glue/tree/master/examples/Bubbling)

## Why?

TEA is an awesome way to write Html-based apps in Elm. However, not every application can be defined just in terms of single `Model` and `Msg`.
Basic separation of [`Html.program`](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#program) is really nice
but in some cases these functions and `Model` and `Msg` thend to grow pretty quickly in an unmanageable way so you need to start breaking things.

There are [many ways](https://www.reddit.com/r/elm/comments/5jd2xn/how_to_structure_elm_with_multiple_models/dbkpgbd/)
you can start. In particular rest of this document will focus just on [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).
This technique is useful for isolating parts that really don't need know too much about each other. It helps to reduce number of things particular module is touching and
limit number of things programmer has to reason about while adding or changing behaviour of such isolated part of system.
In tea this is especially touching `Msg` type and `update` function.

**It's important to understand that `init` `update` `view` and `subscriptions` are all isolated functions connected via `Html.program`.
In pure functional programming we're "never" really managing state ourselves but are rather composing functions that takes state as a data and produce new version of it (`update` function in TEA).**

Now lets have a look on how we can use [`Cmd.map`](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#map),
[`Sub.map`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Sub#map)
and [`Html.map`](http://package.elm-lang.org/packages/debois/elm-parts/latest) for separation in TEA based app.
We will nest `init`, `update`, `subscriptions` and `view` one into another and map them from child to parents types.
Higher level module is then using these units to manage just a subset of its overall state (`Model`).
Here is how `Model` and `Msg` types of a parent application might look like:

```elm
import SubModule

type alias Model =
    { ...
    , subModuleModel : SubModule.Model
    , ...
    }

type Msg
    = ...
    | SubModuleMsg SubModule.Msg
    | ...
```

Basically, the top-level module only holds the `Model` of a child module (`SubModule`) as a single value, and wraps its `Msg` inside one of its `Msg` constructors (`SubModuleMsg`).
Of course, `init`, `update`, and `subscriptions` also have to know how to work with this part of `Model`, and there you need
[`Cmd.map`](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#map),
[`Html.map`](http://package.elm-lang.org/packages/debois/elm-parts/latest) and
[`Sub.map`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Sub#map).
For instance, this is how simple delegation of `Msg` in `update` might look:

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
    case msg of
        ...
        SubModuleMsg subMsg ->
            let
                ( subModel, subCmd ) =
                    SubModule.update subMsg model.subModuleModel
            in
                ( { model | subModuleModel = subModel }, Cmd.map SubModuleMsg subCmd )
        ...
```

As you can see, this is quite neat even though it requires some boiler-plate code.
Let's take a look at `view` and [`Html.map`](http://package.elm-lang.org/packages/debois/elm-parts/latest) in action:

```elm
view : Model -> Html Msg
view model =
   Html.div
       []
       [ ...
       , Html.map SubModuleMsg <| SubModule.view model.subModuleModel
       , ... ]
```

You can use [`Cmd.map`](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#map) inside `init` as well and
[`Sub.map`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Sub#map) which is fairly similar to
[`Html.map`](http://package.elm-lang.org/packages/debois/elm-parts/latest) in `subscriptions` to finish wiring of a child module (`SubModule`).

And this is as far as pure TEA goes. This may possibly be good fit for your needs, and that's OK. Why might you still want to use this package?

- It helps you to keep `update`, `init`, `view` `subscriptions` clean from wiring logic.
- It enforces very abstract interface of mapping between functions with just a little implementation overhead.
- It uses record to keep wiring in single name-space which reduces indirection in interface definition.

## How?

**The most important type that TEA is built around is `( Model, Cmd Msg )`. All we're missing is just a tiny abstraction that will
make working with this pair easier. This is really the core idea of the whole `Glue` package.**

To simplify glueing of things together, the `Gue` type is introduced by this package.
This is simply just a name-space for pure functions that defines interface between modules to which you can then refer by single name.
Other functions within the `Glue` package use the `Glue.Glue` type as proxy to access these functions.

### Glueing independent TEA App

This is how we can construct the `Glue` type for [counter example](https://guide.elm-lang.org/architecture/user_input/buttons.html):

```elm
import Glue exposing (Glue)

-- Counter module
import Counter

counter : Glue Model Counter.Model Msg Counter.Msg
counter =
    Glue.simple
        { msg = CounterMsg
        , get = .counterModel
        , set = \subModel model -> { model | counterModel = subModel }
        , init = Counter.init
        , update = Counter.update
        , view = Counter.view
        , subscriptions = \_ -> Sub.none
        }
```
All mappings from one types to another (`Model` and `Msg` of parent/child) happens in here.
Definition of this interface depends on API of child module (`Counter` in this case).

With `Glue` defined, we can go and integrate it with the parent.
Based on the `Glue` type definition, we know we're expecting `Model` and `Msg` to be (at least) as follows:

```elm
type alias Model =
    { counterModel : Counter.Model }

type Msg
    = CounterMsg Counter.Msg
```

And this is our `init`, `update` and `view` for this example:

```elm

init : ( Model, Cmd Msg )
init =
    ( Model, Cmd.none )
        |> Glue.init counter

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        CounterMsg counterMsg ->
            ( model, Cmd.none )
                |> Glue.update counter counterMsg

view : Model -> Html Msg
view =
    Glue.view counter
```

As you can see we're using just `Glue.init`, `Glue.update` and `Glue.view` in these functions to wire child module.

### Wrap Polymorphic Module

A "polymorphic module" is what I call TEA modules that have to be integrated into some other app.
Such a module has usually API like `update : Config msg -> Model -> ( Model, Cmd msg )`.
These types of modules often performs [child to parent communication](#action-bubbling)
but let's leave this detail for now.
Basically these modules are using `Cmd.map`, `Html.map`, and `Sub.map` internally
so you don't need to map these types in parent module or `Glue` type definition.

To make `Counter` "polymorphic" we can start by adding one extra argument to its `view` function
and use `Html.map` internally. Then we need to change type annotation of `init` and `update`
to generic `Cmd msg`. Since both function are using just `Cmd.none`
we don't need to change anything else but that.

```elm
init : ( Model, Cmd msg )

update : Msg -> Model -> ( Model, Cmd msg )

view : (Msg -> msg) -> Model -> Html msg
view msg model =
    Html.map msg <|
        Html.div
            []
            [ Html.button [ Html.Events.onClick Decrement ] [ Html.text "-" ]
            , Html.text <| toString model
            , Html.button [ Html.Events.onClick Increment ] [ Html.text "+" ]
            ]
```

**Note:**
*As you can see `view` is now taking extra argument - function from `Msg` to parent's `msg`.
In practice I usually recommend to use record with functions called `Config msg` which is much more extensible.*

Now we need to change `Glue` type definition in parent module to reflect the new API of `Counter`:

```elm
counter : Glue Model Counter.Model Msg Counter.Msg
counter =
    Glue.poly
        { get = .counterModel
        , set = \subModel model -> { model | counterModel = subModel }
        , init = Counter.init
        , update = Counter.update
        , view = Counter.view CounterMsg
        , subscriptions = \_ -> Sub.none
        }
```

As you can see we've switch from `Glue.simple` constructor to `Glue.poly` one.
This means we no longer need to supply `msg` since `Glue.poly` doesn't need it.
Instead we pass `CounterMsg` to `view` as argument (remember - we've changed view definition previously!).

### Child Parent Communication

If your module is [polymorphic](#wrap-polymorphic-module) it can easily send `Cmd`s to its parent.
Please check [cmd-extra](http://package.elm-lang.org/packages/GlobalWebIndex/cmd-extra/latest) package
which helps you construct `Cmd Msg` from `Msg`.

**It's important to understand that this might not be the best technique for managing all communication between parent and child.
You can always expose `Msg` constructor from child (`exposing(Msg(..))`) and match it in parent. Anyway if you need to do such a thing
you maybe made a mistake in separation design of state. Do these states really need to be separated?**

Using `Cmd` for communication with upper module works like this:

```text
    +------------------------------------+
    |                                    |
    v                                    |
+-----------------------------------+    |
|                                   |    |
| Parent Module                     |    |
|                                   |    +
|   +                               |  Cmd Msg
|   |                               |    |
|  Model                            |    |
|   |                               |    |
|   |   +------------------------+  |    |
|   |   |                        |  |    |
|   |   | Child Module           |  |    |
|   |   |                        |  |    |
|   +-> |                        +-------+
|       +------------------------+  |
|                                   |
+-----------------------------------+

```

As an example, we can use the (polymorphic) `Counter.elm` again. Let's say we want to send some action to the parent when the counter's value is even.

**Note:**
*This Example works as demonstration of such a communication and do not really
reflect real world use-case of this practice. Clearly if parent module is interested
in whole model of child module (Even/Odd is really tightly related to child's model)
it should really be part of its Model and passed to child's view as argument rather than other way around.*

For this we need to define a helper function in `Counter.elm`:

```elm
isEven : Int -> Bool
isEven num =
    num % 2 == 0


notifyEven : msg -> Model -> Cmd msg
notifyEven msg model =
    if isEven model then
        Cmd.Extra.perform msg
    else
        Cmd.none
```

`isEven` is pretty straightforward. It just returns `True` or `False` for a given `Int`.
`notifyEven` takes the parent's `Msg` constructor and either [`perform`](http://package.elm-lang.org/packages/GlobalWebIndex/cmd-extra/1.0.0/Cmd-Extra#perform)s
it as `Cmd`, or returns `Cmd.none`.

Now we need to change `init` and `update` so they're emitting this new `Cmd`.
The simplest way is just to make them both accept a `msg` constructor similarly to how `view` does:

```elm
init : msg -> ( Model, Cmd msg )
init msg =
    let
        model = 0
    in
        ( model, notifyEven msg model )


update : msg -> Msg -> Model -> ( Model, Cmd msg )
update notify msg model =
    let
        newModel =
            case msg of
                Increment ->
                    model + 1

                Decrement ->
                    model - 1
    in
        ( newModel, notifyEven notify newModel )
```

Now both `init` and `update` should send `Cmd` when `Model` is an even number.
This is a breaking change to `Counter`'s API so we need to change its integration as well.
Since we want to actually use this message and do something with it let me first update
the parent's `Model` and `Msg`:

```elm
type alias Model =
    { isEven : Bool
    , counter : Counter.Model
    }

type Msg
    = CounterMsg Counter.Msg
    | Even
```

Because we've changed `Model` (added `isEven : Bool`) we should change `init` and `view` of parent to:

```elm
init : ( Model, Cmd Msg )
init =
    ( Model False, Cmd.none )
        |> Glue.init counter

view : Model -> Html Msg
view model =
    Html.div []
        [ Glue.view counter model
        , Html.text <|
            if model.isEven then
                "is even"
            else
                "is odd"
        ]
```

This completes the changes to `Model`. Now we need to change update `update` function
so it can handle the `Even` message.

```elm
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        CounterMsg counterMsg ->
            ( { model | isEven = False }, Cmd.none )
                |> Glue.update counter counterMsg

        Even ->
            ( { model | isEven = True }, Cmd.none )
```

As you can see we're setting `even` to `False` on every `CounterMsg`.
This is because `Counter` is just emitting `Cmd` when its `Model` is Even.
*Yes this is just example of what is possible.*

To handle the `Even` action itself. This simply sets `isEven = True` in the model.

Since the parent is ready to handle actions from `Counter` our last step is simply
to update the `Glue` construction for the new APIs:

```elm
counter : Glue Model Counter.Model Msg Counter.Msg
counter =
    Glue.poly
        { get = .counterModel
        , set = \subModel model -> { model | counterModel = subModel }
        , init = Counter.init Even
        , update = Counter.update Even
        , view = Counter.view CounterMsg
        , subscriptions = \_ -> Sub.none
        }
```

There we simply pass the parent's `Even` constructor to the `update` and `init` functions of the child here.
This is enough to update whole wiring.

See this [complete example](https://github.com/turboMaCk/glue/tree/master/examples/Bubbling) to learn more.

## License

BSD-3-Clause

Copyright 2017 Marek Fajkus
