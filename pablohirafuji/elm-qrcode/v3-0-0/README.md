# QR Code

QR Code encoding and rendering. [Demo](https://pablohirafuji.github.io/elm-qrcode/).

## Basic Usage

```elm
import QRCode

qrCodeView : String -> Html msg
qrCodeView message =
    QRCode.encode message
        |> Result.map QRCode.toSvg
        |> Result.withDefault
            (Html.text "Error while encoding to QRCode.")

```

Waiting for a `Canvas` package in 0.19 to add this render possibility.

If you would like more functionalities, please open an issue on [GitHub](https://github.com/pablohirafuji/elm-qrcode/issues).


## Thanks

Thank you Carolyn Eby, for creating [this great tutorial](http://www.thonky.com/qr-code-tutorial/) on how QR Code works.

Thank you Evan for bringing joy to the frontend.
