## 1. It runs on your phone

> **Requirement:** the Pages URL opens on your phone and classifies the live camera. → A screenshot from your phone with a leaf (or a photo of a leaf on another screen) and the probabilities.

<img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/00be8314-3e02-46b8-a3bb-0d2335e1ae59" />


---

## 2. The same numbers as the notebook

> **Requirement:**  Upload check_image.jpg on the page and write its probabilities next to check_expected.json. Do they agree to three decimals?

### Probability Comparison Table

| Class Name | Web App Probability (`check_image.jpg`) [%] | Expected Probability (`check_expected.json`) [%] | Matches to 3 Decimals? (Y/N) |
| :--- | :---: | :---: | :---: | :---: |
| [apple] | 2.27e-04 | 2.27e-04 | [Yes] |
| [cherry] | 99.998 | 99.998 | [Yes] |
| [chestnut] | 8.57e-04 | 8.57e-04 | [Yes] |
| [maple] | 1.42e-04 | 1.42e-04 | [Yes] |

---

## 3. The preprocessing matches your model

> **Requirement:** Find in index.html where the frame is cropped, resized to 96 × 96, divided by 255 and standardized with the mean and std, and in which order the channels go into the tensor. Does it match model_info.json and the transform of the notebook?

* In function `infer()` in `index.html` (lines `2686` to `2719`), the images is firstly resized to exactly 96x96 pixels (dimension from `model_info.json`). Afterwards, the color values are divided by 255 to scale to 0.0-1.0, subtracts the color meand and divides by the color standard deviation. The channels go in the order `Red -> Green -> Blue`.

* Preprocessing pipeline strictly matches notebook configuration.

---

## 4. Nothing leaves the phone

> **Requirement:** Is the image sent anywhere? Which URLs does the page load, and why?

* No. All image cropping, tensor formatting, and ONNX inference (`session.run`) execute entirely client-side within the browser using local memory.

* Upon loading `index.html`, the network tab shows the following requests:

1. **ONNX Runtime Web Library:**
   * **URL:** `[https://cdn.jsdelivr.net/npm/onnxruntime-web/dist/ort.min.js](https://cdn.jsdelivr.net/npm/onnxruntime-web@1.22.0/dist/ort.min.js)`
   * **Why it loads:** Required to run the AI model locally in the browser.

2. **Model Configuration:**
   * **URL:** `./model_info.json` (Local fetch)
   * **Why it loads:** Provides the mean/std parameters, image size, and class labels required to preprocess the image and translate the output.

3. **ONNX Model Weights:**
   * **URL:** `./leaf_model.onnx` (Local fetch)
   * **Why it loads:** This is the actual neural network file that performs the prediction.

* **Telemetric Data Sent Externally:** [ ] NONE  |  [ ] DETECTED (Fail)

**Verdict:** [ ] PASS (All processing is strictly client-side; zero image data leaves the device)  |  [ ] FAIL
