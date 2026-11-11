# MLR Exercise 8 — Leaf classifier on the phone

**Live page: <https://klaraseli.github.io/mlr-ex08/>**

A single-page web app that classifies leaves (apple, cherry, chestnut, maple) live
from the rear camera of a phone. The MobileNetV3 model runs entirely in the
browser with [ONNX Runtime Web](https://onnxruntime.ai/docs/tutorials/web/) and
WebAssembly — no server, nothing to install.

## Using it

1. Open the page on a phone: <https://klaraseli.github.io/mlr-ex08/>
2. Tap **Start camera** and allow camera access.
3. Point the rear camera at a leaf. The probabilities of all four classes
   update live.
4. No camera (or camera permission denied)? Use **Upload photo** instead.
5. **Test with check_image.jpg** runs the bundled validation image through the
   same pipeline and compares the result with `check_expected.json`.

The page works on iPhone Safari and Android Chrome. Camera access requires
HTTPS, which GitHub Pages provides.

## Preprocessing (matches `model_info.json` and the notebook)

For every frame:

1. crop the central square of the frame;
2. resize it to `input_size` = 96 × 96;
3. read RGB pixels, divide by 255 to get 0–1;
4. standardize each channel: `(pixel − mean[channel]) / std[channel]`
   with mean `[0.485, 0.456, 0.406]` and std `[0.229, 0.224, 0.225]`;
5. pack as NCHW, input shape `1 × 3 × 96 × 96`, channel order R, G, B.

The ONNX model outputs raw logits (no softmax), so the page applies
`softmax(logits)` to display probabilities, just as the notebook does.

## Verification

`check_image.jpg` (96 × 96) is classified through the same code path as camera
frames. Measured in Chromium and WebKit:

| class | expected | measured |
| --- | --- | --- |
| apple | 0.00000227020 | 0.00000227023 |
| cherry | 0.999988 | 0.999988 |
| chestnut | 0.00000856604 | 0.00000856620 |
| maple | 0.00000142129 | 0.00000142130 |

Maximum difference 2.08 × 10⁻⁸ — agreement to well beyond three decimals.

## Privacy

All inference happens on the device. Captured frames and uploaded photos are
never sent anywhere. The only network requests are the page's own files and
the ONNX Runtime Web script and WebAssembly binary from the jsDelivr CDN.

## Files

- `index.html` — the whole app (plain JavaScript; QR generator inlined)
- `leaf_model.onnx` — exported model
- `model_info.json` — class names, input size, mean and std
- `check_image.jpg`, `check_expected.json` — bundled verification pair

## QR code

At the bottom of the page a QR code encodes the public GitHub Pages URL above;
a clickable text link sits next to it. The code is generated in the browser by
the inlined MIT-licensed [`qrcode-generator`](https://github.com/kazuhikoarase/qrcode-generator)
library — no external QR-image service is involved. The rendered QR code was
decoded and checked against the live page URL.
