## 1. It runs on your phone

> **Requirement:** the Pages URL opens on your phone and classifies the live camera. → A screenshot from your phone with a leaf (or a photo of a leaf on another screen) and the probabilities.

<img width="828" height="1792" alt="image" src="https://github.com/user-attachments/assets/00be8314-3e02-46b8-a3bb-0d2335e1ae59" />


---

## 2. Notebook Parity & Numerical Accuracy Check

> **Requirement:** Upload `check_image.jpg` on the web app and compare output probabilities against `check_expected.json`. Must match to 3 decimal places ($|p_{\text{app}} - p_{\text{notebook}}| \le 0.001$).

### Probability Comparison Table

| Class Name | Web App Probability (`check_image.jpg`) | Expected Probability (`check_expected.json`) | Absolute Difference | Matches to 3 Decimals? (Y/N) |
| :--- | :---: | :---: | :---: | :---: |
| [Class 1] | 0.XXX | 0.XXX | 0.000 | [Y/N] |
| [Class 2] | 0.XXX | 0.XXX | 0.000 | [Y/N] |
| [Class 3] | 0.XXX | 0.XXX | 0.000 | [Y/N] |
| [Class 4] | 0.XXX | 0.XXX | 0.000 | [Y/N] |
| [Class 5] | 0.XXX | 0.XXX | 0.000 | [Y/N] |

**Verdict:** [ ] PASS (All classes match to 3 decimal places)  |  [ ] FAIL

---

## 3. Preprocessing Verification

> **Requirement:** Verify in `index.html` (or associated JS scripts) that image preprocessing matches `model_info.json` and the notebook transform pipeline.

### Preprocessing Checklist & Code Locations

* **Cropping / Aspect Ratio Handling:**
  * **Location in Code:** `[index.html / script.js line XX]`
  * **Implementation Note:** `[e.g., Center cropped / scaled to square]`

* **Resizing (96 × 96):**
  * **Location in Code:** `[index.html / script.js line XX]`
  * **Verified Target Resolution:** 96 × 96 px

* **Normalization ($\div 255$):**
  * **Location in Code:** `[index.html / script.js line XX]`
  * **Values Scaled:** Pixel range $[0, 255] \rightarrow [0.0, 1.0]$

* **Standardization (Mean & Std):**
  * **Location in Code:** `[index.html / script.js line XX]`
  * **Mean Used:** `[R, G, B]` (Matches `model_info.json`: [ ] Yes | [ ] No)
  * **Std Used:** `[R, G, B]` (Matches `model_info.json`: [ ] Yes | [ ] No)

* **Tensor Layout / Channel Order:**
  * **Location in Code:** `[index.html / script.js line XX]`
  * **Format:** `[e.g., NCHW or NHWC / RGB order]` (Matches notebook transform: [ ] Yes | [ ] No)

**Verdict:** [ ] PASS (Preprocessing pipeline strictly matches notebook configuration)  |  [ ] FAIL

---

## 4. Privacy & Network Traffic Check

> **Requirement:** Verify that no camera frames or uploaded images leave the local phone environment.

### External Network Requests

* **Network Inspection Method:** [e.g., Chrome DevTools Network Tab / Safari Web Inspector]
* **Total Remote External Requests Made:** `[Count]`

#### Loaded Resources List:
1. **URL:** `[URL 1]`
   * **Purpose:** `[e.g., Main application markup / index.html]`
2. **URL:** `[URL 2]`
   * **Purpose:** `[e.g., ONNX Runtime Web library CDN]`
3. **URL:** `[URL 3]`
   * **Purpose:** `[e.g., Quantized model weights / model.onnx]`

### Data Leak Verification

* **Images/Frames Sent via POST/FETCH/XHR:** [ ] NONE  |  [ ] DETECTED (Fail)
* **Telemetric Data Sent Externally:** [ ] NONE  |  [ ] DETECTED (Fail)

**Verdict:** [ ] PASS (All processing is strictly client-side; zero image data leaves the device)  |  [ ] FAIL
