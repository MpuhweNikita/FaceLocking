# Troubleshooting Cheat Sheet

**SYMPTOM:** Terminal says `RuntimeError: Camera not available` or screen is black.
**LIKELY CAUSE:** Another app (Zoom, Teams) is using the webcam, or the camera index is wrong.
**QUICK FIX:** Close Zoom/Teams. If you have multiple webcams, run: `python -m src.face_tracking --target Student --camera 1`

**SYMPTOM:** `ModuleNotFoundError: No module named 'cv2'` (or numpy, mediapipe).
**LIKELY CAUSE:** The virtual environment is not activated, or dependencies were not installed.
**QUICK FIX:** Run `.\.venv\Scripts\Activate.ps1`, then `pip install -r requirements.txt`.

**SYMPTOM:** `onnxruntime.capi.onnxruntime_pybind11_state.NoSuchFile: [ONNXRuntimeError] ... models/embedder_arcface.onnx`
**LIKELY CAUSE:** The ArcFace model was not downloaded. (It's too big for GitHub).
**QUICK FIX:** Run the `Invoke-WebRequest` and `Expand-Archive` commands from step 7 of `DEMO_GUIDE.md` to place `w600k_r50.onnx` into the `models/` directory as `embedder_arcface.onnx`.

**SYMPTOM:** `FileNotFoundError: Missing model file: models/face_landmarker.task`
**LIKELY CAUSE:** MediaPipe model is missing.
**QUICK FIX:** Run the `Invoke-WebRequest` command for the MediaPipe model from step 7 of `DEMO_GUIDE.md`.

**SYMPTOM:** `KeyError: 'TargetPerson'` or `[SEARCHING]` never finds you.
**LIKELY CAUSE:** You haven't enrolled your face under that name yet, or the database is missing.
**QUICK FIX:** Stop the tracker. Run `python -m src.enroll --name TargetPerson`. Smile for the camera, then restart the tracker.

**SYMPTOM:** The tracker is jumping around to the other person instead of locking onto me!
**LIKELY CAUSE:** The `dead_zone` or `iou` logic associated the other face because they crossed directly in front of you.
**QUICK FIX:** Tell the other person to stand slightly to the side, not directly overlapping your face.

**SYMPTOM:** ESP32 / MQTT errors spamming the console.
**LIKELY CAUSE:** You accidentally ran `python -m src.recognize`.
**QUICK FIX:** Quit with `q`. For the face locking demo without hardware, YOU MUST RUN `python -m src.face_tracking --target [YourName]`. It is completely decoupled from MQTT and the ESP32.

**SYMPTOM:** PowerShell gives `cannot be loaded because running scripts is disabled on this system`.
**LIKELY CAUSE:** Windows execution policy blocks the virtual environment script.
**QUICK FIX:** Run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`, then try activating again.
