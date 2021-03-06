# elm-parse-html

An HTML parser in Elm using the [elm-tools/parser](http://package.elm-lang.org/packages/elm-tools/parser/2.0.1) library. This library takes some ideas from [jinjor/elm-html-parser](http://package.elm-lang.org/packages/jinjor/elm-html-parser), and implements something similar, but with a focus on getting more specific information when parsing fails, and without the querying capabilities that library provides.

    import ParseHtml exposing (parseDocument)

    parseDocument "<!DOCTYPE html><html></html>"
      == Ok (Element "html" [] [])

    parseDocument "<!DOCTYPE html><html><head></head><body><h1>I am a document!</h1><!--I'm a comment!--></body></html>"
      == Ok
        (Element "html"
            []
            [ Element "head" [] []
            , Element "body"
                []
                [ Element "h1" [] [ TextNode "I am a document!" ]
                , Comment "I'm a comment!"
                ]
            ]
        )


### TODO
- Slashless self-closing tags like \<br\>, \<hr\>, etc.
- Escape characters?

### Note
For any who may have read [my reasons for not using GitHub](https://github.com/neurodynamic/Switching-to-GitLab), this project is hosted on GitHub because the Elm package management system currently only integrates with GitHub. If integrations are implemented for other code hosting sites, I plan to relocate this project somewhere else.