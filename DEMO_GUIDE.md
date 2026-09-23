# DEMO_GUIDE.md

## 1. Prerequisites
- **OS:** Windows 10/11
- **Terminal:** PowerShell
- **Python:** 3.9 to 3.11 recommended (MediaPipe can be picky with newer versions)
- **Webcam:** Required for the live demo

## 2. Opening the folder
Open PowerShell and navigate to the project:
```powershell
cd D:\Embedded-Y3\Face-recognition-new\Face-Recognition-main
```

## 3. Creating the virtual environment
```powershell
python -m venv .venv
```

## 4. Activating the virtual environment
```powershell
.\.venv\Scripts\Activate.ps1
```
*(If you get an execution policy error, run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` first)*

## 5. Installing dependencies
```powershell
pip install -r requirements.txt
```
(This installs `opencv-python`, `numpy`, `onnxruntime`, `scipy`, `tqdm`, `mediapipe`, `paho-mqtt`).

## 6. Verifying installation & structure
Initialize the project structure (creates empty `data/` and `models/` folders):
```powershell
cd face-recognition-5pt
python init_project.py
```

## 7. Obtaining required models
The pipeline relies on two models. The code expects them in the `models/` directory.

### Model 1: ArcFace (embedder_arcface.onnx)
Run this from the `face-recognition-5pt` directory:
```powershell
Invoke-WebRequest -Uri "https://sourceforge.net/projects/insightface.mirror/files/v0.7/buffalo_l.zip/download" -OutFile "buffalo_l.zip"
Expand-Archive -Path buffalo_l.zip -DestinationPath . -Force
Copy-Item "w600k_r50.onnx" -Destination "models/embedder_arcface.onnx"
Remove-Item buffalo_l.zip, w600k_r50.onnx, 1k3d68.onnx, 2d106det.onnx, det_10g.onnx, genderage.onnx -ErrorAction SilentlyContinue
```

### Model 2: MediaPipe Face Landmarker (face_landmarker.task)
```powershell
Invoke-WebRequest -Uri "https://storage.googleapis.com/mediapipe-models/face_landmarker/face_landmarker/float16/1/face_landmarker.task" -OutFile "models/face_landmarker.task"
```

## 8. Preparing / Enrolling a face
Before testing tracking, you need an enrolled identity. We will enroll yourself as "TargetPerson".
```powershell
# You must be in the face-recognition-5pt directory
python -m src.enroll --name TargetPerson
```
- Sit in front of the camera, wait for the window to appear.
- The system will take a few pictures and save them to `data/enroll/TargetPerson`.
- The script automatically builds `data/db/face_db.npz`.

## 9. Starting the application
To start the face locking demo:
```powershell
python -m src.face_tracking --target TargetPerson
```

## 10. Demo Execution Steps
1. **Selecting Identity:** Done via the `--target TargetPerson` flag.
2. **Activating Face Lock:** Walk into the camera frame. The application will recognize you and output `[LOCK_ACQUIRED] Locked onto identity: TargetPerson`. 
3. **Demonstrating Tracking:** Move your head left, right, up, and down. The console will print `[FACE_MOVEMENT]` and log it to `action_history.txt`. The on-screen UI will show "Recent Actions".
4. **Demonstrating Another Face:** Have someone else enter the frame. The system will continue to lock on you and will not switch targets.
5. **Temporary Recognition Failure:** Cover your face with your hands for 1-2 seconds. The tracker uses a buffer and handles brief failures without releasing the lock.
6. **Sustained Disappearance:** Walk out of the camera view. Wait about 3-4 seconds. The system will print `[TRACKING_LOST]` and eventually `[LOCK_RELEASED]`, returning to the `[SEARCHING]` state.
7. **Action History:** Open `action_history.txt` using Notepad to prove that all movements and locks were persistently saved with timestamps.

## 11. Stopping the application
Press `q` on your keyboard while the camera window is active.
```powershell
cat action_history.txt
```
To verify the history.

## 12. Important Notes
- **Do NOT** rely on the ESP32 for the pure face-locking demo. The Python `face_tracking.py` script works perfectly on CPU without requiring an MQTT broker or hardware servo.
- Keep the terminal visible alongside the camera feed so the instructor can see the live logging.
