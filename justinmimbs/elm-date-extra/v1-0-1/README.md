# elm-date-extra

Extra functions for working with `Date` values in Elm.

This package is organized into the following modules. Only examples of common
uses are given below; see the docs for the full API.

## Create

Create dates from parts.

```elm
import Date exposing (Month(..))
import Date.Create as Date

Date.fromParts 1999 Dec 31 23 59 0 0
-- <31 December 1999, 23:59, local time>
```

Create dates from strings in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)
format.

```elm
Date.fromIsoString "2000-01-01"
-- Just <1 January 2000, local time>

Date.fromIsoString "2009-W01-1T00Z"
-- Just <29 December 2008, UTC>

Date.fromIsoString "2016-218T20:00-03:00"
-- Just <5 August 2016, 23:00, UTC>

Date.fromIsoString "1/1/2001"
-- Nothing
```

## Convert

Convert dates to formatted strings, using templates based on Date Format
Patterns in [Unicode Technical Standard #35](http://www.unicode.org/reports/
tr35/tr35-43/tr35-dates.html#Date_Format_Patterns).

```elm
import Date exposing (Month(..))
import Date.Create as Date
import Date.Convert as Date

date = Date.fromParts 2007 Mar 15 13 45 56 67

Date.toFormattedString "EEEE, MMMM d, y 'at' h:mm a" date
-- "Thursday, March 15, 2007 at 1:45 PM"

Date.toUtcIsoString date
-- "2007-03-15T17:45:56.067Z"
-- (example has a local offset of UTC-04:00)
```

## Math

Operate on the numeric properties of dates.

```elm
import Date exposing (Month(..))
import Date.Create as Date
import Date.Math as Date exposing (Interval(..))

date =
  Date.fromParts 1999 Dec 31 23 59 59 999

Date.floor Hour date
-- <31 December 1999, 23:00>

Date.ceiling Monday date
-- <3 January 2000, 00:00>

Date.add Week -2 date
-- <17 December 1999, 23:59:59.999>

Date.diff Day date (Date.add Week 2 date)
-- 14

{- List every Monday in the month of `date`. -}
Date.range Monday 1
  (Date.floor Month date)   -- <1 December 1999>
  (Date.ceiling Month date) -- <1 January 2000>
-- [<6 December 1999>, <13 December 1999>, <20 December 1999>, <27 December 1999>]
```

## Extract

This module provides additional extractions to those available in the
core `Date` library.

## Facts

This module contains basic date information, like functions to convert between
`Date.Month` values and month numbers as integers.
