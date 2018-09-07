# TSFoster/elm-md5

Compute MD5 message digests in Elm.

This is a fork of truqu/elm-md5 (the maintained fork of sanichi/elm-md5 focusing on performance), adding `hexInOctets`.

## Quick Start

This library exposes just two functions, `hex`, which takes a `String` input and returns the 128-bit MD5
digest as a `String` of 32 hexadecimal characters, and `hexInOctets`, which instead returns the 128-bit
MD5 digest as a `List` of 16 `Int`s between 0 and 15.

```elm
MD5.hex ""          == "d41d8cd98f00b204e9800998ecf8427e"
MD5.hex "foobarbaz" == "6df23dc03f9b54cc38a0fc1483df6e21"
MD5.hexInOctets "hello world" ==
    [ 0x5e , 0xb6 , 0x3b , 0xbb
    , 0xe0 , 0x1e , 0xee , 0xd0
    , 0x93 , 0xcb , 0x22 , 0xbb
    , 0x8f , 0x5a , 0xcd , 0xc3
    ]
```

Unlike the [JavaScript function](https://css-tricks.com/snippets/javascript/javascript-md5/) from which this
implementation has been ported, CRLF pairs in the input are not automatically replaced with LFs prior to computing
the digest. If you want that behaviour, adjust the input before evaluating the function. For example:

```elm
myHex : String -> String
myHex input =
    let
        myInput =
            Regex.replace Regex.All (Regex.regex "\x0D\n") (\_ -> "\n") input
    in
        MD5.hex myInput
```

## License

Licensed under BSD-3. See `LICENSE` file. (c) 2016-2018 Mark Orr, 2018-present TruQu, 2018-present Toby Foster
