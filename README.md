# Object Detection & Player Tracking — Football Match Analysis

![Python](https://img.shields.io/badge/Python-3.8+-blue?style=flat-square&logo=python)
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-purple?style=flat-square)
![OpenCV](https://img.shields.io/badge/OpenCV-Computer%20Vision-green?style=flat-square)
![SORT](https://img.shields.io/badge/SORT-Multi--Object%20Tracking-orange?style=flat-square)

> A real-time computer vision pipeline that detects and tracks players, referees, and the ball across football match video frames — assigning persistent IDs that survive occlusion and re-entry.

---

## The Problem

Manual player tracking in sports video analysis is slow, expensive, and error-prone at real-time frame rates. Coaches and analysts need an automated way to identify *who* is where on the pitch at every moment — consistently, even when players overlap or leave the frame.

---

## How It Works

```
Input Video (football match footage)
         │
         ▼
┌─────────────────────┐
│  YOLOv8 Detection   │  ← detects players, referee, ball
│  (frame-by-frame)   │    outputs bounding boxes + class labels
└────────┬────────────┘
         │  bounding boxes per frame
         ▼
┌─────────────────────┐
│  SORT Tracker       │  ← assigns persistent unique IDs
│  (Kalman Filter +   │    tracks identity across frames
│   Hungarian Match)  │    handles occlusion & re-entry
└────────┬────────────┘
         │  tracked objects with IDs
         ▼
┌─────────────────────┐
│  OpenCV Visualiser  │  ← draws bounding boxes + ID labels
│                     │    overlaid on original video frames
└─────────────────────┘
         │
         ▼
  Output: annotated video with tracked players
```

### Why SORT over basic detection?

YOLOv8 alone detects objects *per frame* — it has no memory. If a player leaves the frame and comes back, they get a new random ID each time. SORT (Simple Online and Realtime Tracking) uses a **Kalman Filter** to predict where each object will be next frame, and the **Hungarian Algorithm** to match predictions to detections — giving each player a consistent ID for the entire video.

---

## Results

| Metric | Result |
|--------|--------|
| Detection classes | Player, Referee, Ball |
| Tracking method | SORT (Kalman Filter + Hungarian Algorithm) |
| ID consistency | Persistent across frames including re-entries |
| Input format | MP4 video (720p tested) |
| Output | Annotated video with bounding boxes and IDs |

- Successfully tracked multiple players simultaneously with consistent IDs
- Ball tracking adapted to suppress duplicate detections (only one ball maintained)
- Demonstrated on a 15-second 720p football match clip

---

## Project Structure

```
Object_detection_tracking/
│
├── detect_from_video.py          # Main pipeline: detection + tracking + visualisation
├── sort.py                       # SORT multi-object tracking algorithm
├── yolov8n.pt                    # YOLOv8 nano model weights
├── 15sec_input_720p.mp4          # Sample input video
├── Object_Detection_Tracking_Report.pdf   # Full project report
└── README.md
```

---

## Run Locally

**1. Clone the repository**
```bash
git clone https://github.com/shree0156/Object_detection_tracking.git
cd Object_detection_tracking
```

**2. Install dependencies**
```bash
pip install ultralytics opencv-python numpy
```

**3. Run detection + tracking**
```bash
python detect_from_video.py
```

A live video window will open showing the annotated output with bounding boxes and player IDs. To process a different video, update the input path in `detect_from_video.py`.

> **Optional:** To save the output video instead of displaying it live, add a `cv2.VideoWriter` block to `detect_from_video.py`.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Object detection | YOLOv8 (Ultralytics) — nano model |
| Multi-object tracking | SORT (Kalman Filter + Hungarian Algorithm) |
| Video processing | OpenCV |
| Language | Python 3.8+ |
| Data handling | NumPy |

---

## Key Design Decisions

**Why YOLOv8 nano?** Speed matters for real-time tracking. The nano model processes frames fast enough for live video while still achieving reliable detection on standard match footage.

**Why SORT over DeepSORT?** SORT is lightweight and has no deep learning dependency for the tracking component — making it easier to run without a GPU. For this use case (single camera, controlled environment), SORT performs reliably without the overhead of DeepSORT's appearance embeddings.

**Ball deduplication:** The default YOLO model sometimes detects multiple overlapping bounding boxes for the ball. A custom suppression step in the pipeline keeps only the highest-confidence detection per frame.

---

## Future Improvements

- [ ] Train a custom YOLOv8 model on a larger football dataset to improve ball detection accuracy
- [ ] Switch to DeepSORT or ByteTrack for better re-identification after long occlusions
- [ ] Add player jersey number recognition using OCR
- [ ] Generate heatmaps of player positions over the full match
- [ ] Build a Streamlit dashboard for uploading and analysing match videos

---

## Author

**Shreeja Maiya**

---

*If you found this useful, consider giving it a ⭐*

Install all using the command

