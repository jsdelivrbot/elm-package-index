# timezone-data

This Elm package contains time zone data from the [IANA Time Zone Database][tzdb] for using with [`elm/time`][elmtime].

The `elm/time` library provides a `Posix` type for representing an instant in time. Extracting human-readable parts from a `Posix` time requires a `Time.Zone`. This library provides `Time.Zone` values for all named zones in the database, covering changes between 1970 and 2037.


## Installation

```sh
elm install justinmimbs/timezone-data
```


## Examples

### Get the local time zone

The `TimeZone.getZone` task gets the client's local `Time.Zone` along with its zone name.

```elm
import Time
import TimeZone

getZone : Task TimeZone.Error ( String, Time.Zone )
getZone =
    TimeZone.getZone
```

See [this page][getzone] for a full example.

### Get a specific time zone

Data is contained in the `TimeZone.Data` module, where each zone is packaged as a `TimeZone.Data.Pack` type. A `Pack` must be unpacked to a `Time.Zone` with the `TimeZone.unpack` function.

```elm
import Time
import TimeZone
import TimeZone.Data

zone : Time.Zone
zone =
    TimeZone.unpack TimeZone.Data.america__new_york
```

Each `Pack` in the `TimeZone.Data` module is named after its zone name (e.g. `America/New_York`), where slashes are replaced by `__`, dashes are replaced by `_`, and the name is lowercased. For example, `America/Port-au-Prince` becomes `america__port_au_prince`.

You can look up a `Pack` by its zone name in the `TimeZone.Data.packs` dictionary.

```elm
import Dict
import TimeZone.Data

Dict.get "America/New_York" TimeZone.Data.packs

-- Just TimeZone.Data.america__new_york
```


## Alternatives

Using this library to include all time zones in your compiled asset would increase its minified and gzipped size by about 18 KB. For a more lightweight approach to getting time zones into Elm, you may consider [fetching only the data you need at runtime][timezone-json]. And if your use case is taking UTC timestamps and displaying them nicely formatted in the client's local time, then you may look into custom elements, like Github's [`time-elements`][time-elements].


[tzdb]: https://www.iana.org/time-zones
[elmtime]: https://package.elm-lang.org/packages/elm/time/latest/
[getzone]: https://github.com/justinmimbs/timezone-data/blob/master/examples/GetZone.elm
[timezone-json]: https://github.com/justinmimbs/timezone-json
[time-elements]: https://github.com/github/time-elements
