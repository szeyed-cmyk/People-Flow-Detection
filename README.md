# People Flow Detection using Object Tracking & Heatmap Visualization

## Overview
This project detects, tracks, and counts people entering and exiting a defined area in a video using object detection and multi-object tracking. It also generates a heatmap showing areas of highest presence and movement.

## Detection Method
- **Model**: YOLOv8n (Ultralytics)
- **Class**: Person only (`class_id = 0`)
- **Tracking**: ByteTrack (built into Ultralytics `model.track()`)
- **Confidence threshold**: 0.3
- **IoU threshold**: 0.5

Each detected person is assigned a unique `track_id` that persists across frames.

## Line Coordinates
Two horizontal lines are drawn across the frame:

| Line   | Position                  | Purpose          |
|--------|---------------------------|------------------|
| Upper  | 40% of frame height       | IN counter       |
| Lower  | 60% of frame height       | OUT counter      |

Example for a 1080p video (1920×1080):
- Upper line: `y = 432`
- Lower line: `y = 648`

These can be adjusted by changing `UPPER_LINE_FRAC` and `LOWER_LINE_FRAC` in the code.

## IN / OUT Logic
The system tracks the vertical center (`cy`) of each person across frames and compares it with their previous position (`prev_y`).

| Direction of Movement | Line Crossed | Counted As |
|-----------------------|--------------|------------|
| Moving **down**       | Upper line   | **IN**     |
| Moving **up**         | Lower line   | **OUT**    |

### Counting Rules
- A person is only counted **once** (their state is stored so they are not counted again).
- The check happens only when a person has a previous position recorded.
- Color coding:
  - **Green** box → Counted as IN
  - **Red** box → Counted as OUT
  - **White** box → Not yet counted

### Pseudocode
