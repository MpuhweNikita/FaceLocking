==================================================
FACE LOCKING LIVE DEMO
==================================================

**Preparation (before presenting):** 
Ensure your virtual environment is active, you are in the `face-recognition-5pt` directory, and you have enrolled your face as `Student` (`python -m src.enroll --name Student`). 

---

### STEP 1 — Start the application
**Command:**
```powershell
python -m src.face_tracking --target Student
```
**What I should see:** 
A camera window opens. The terminal says "Application Started". The on-screen text says `SEARCHING: Student`.

**What I should say:**
"Here is the face locking system. It's currently in the SEARCHING state, looking specifically for my enrolled identity."

---

### STEP 2 — Lock the face
**Action:** 
Step into the camera frame.

**What I should see:** 
A bounding box snaps to your face. The terminal prints `[LOCK_ACQUIRED] Locked onto identity: Student`. The on-screen text turns green and says `LOCKED: Student`.

**What I should say:**
"As soon as the camera detects me, it extracts my 5-point facial landmarks, computes the embedding, and compares it to the database. Since it confidently recognizes me as 'Student', it activates the Face Lock."

---

### STEP 3 — Demonstrate tracking and movement detection
**Action:** 
Slowly move your head to the Left, Right, Up, and Down. 

**What I should see:**
The on-screen UI will show "Recent Actions", updating with `FACE_MOVEMENT`. The terminal will continuously log `[FACE_MOVEMENT] Locked face moved left/right`.

**What I should explain:**
"While the lock is active, the system analyzes my face's position relative to a central dead-zone. When I move out of this zone, it detects the movement direction using clear, explainable spatial logic rather than a black-box neural network action classifier. It's completely CPU-only."

---

### STEP 4 — Introduce another face
**Action:**
Have a friend or instructor lean into the camera frame next to you. (Or use a photo on your phone).

**What I should see:**
The system ignores the other face. The box remains locked onto you. No target switching occurs.

**What I should explain:**
"As you can see, another face entering the frame doesn't break the lock. The system maintains identity continuity and explicitly ignores other detections once it has acquired its target."

---

### STEP 5 — Demonstrate temporary recognition failure
**Action:**
Cover your face completely with both hands for about 1 to 2 seconds, then uncover it.

**What I should see:**
The bounding box might disappear momentarily, but the system will *not* immediately go back to SEARCHING. As soon as you uncover your face, it picks you back up.

**What I should explain:**
"If my face is briefly occluded or recognition drops for a split second, the system handles this instability smoothly. It holds a buffer and waits before releasing the lock, ensuring we don't get jittery lock/unlock cycles."

---

### STEP 6 — Demonstrate sustained disappearance
**Action:**
Physically step completely out of the camera view. Wait 3 to 4 seconds.

**What I should see:**
The terminal prints `[TRACKING_LOST]` followed by `[LOCK_RELEASED] Sustained disappearance...`. The UI resets to `SEARCHING`.

**What I should explain:**
"Because I disappeared for a sustained period, the tracker timed out. It explicitly releases the lock and returns to the initial Searching state, ready to acquire the target again."

---

### STEP 7 — Show action history
**Action:**
Press `q` to quit the camera window. In the terminal, open the action history:
```powershell
cat action_history.txt
```

**What I should see:**
A chronological log with Timestamps, Action Types (LOCK_ACQUIRED, FACE_MOVEMENT, LOCK_RELEASED), and Descriptions.

**What I should explain:**
"Finally, every significant event that happened after the lock was established was saved to this persistent action history, complete with timestamps and action types, fulfilling the action logging requirement."
