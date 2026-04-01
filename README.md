Technologies Used
Technology	Purpose
Python	Programming language
OpenCV	Image processing
YOLOv8	Object detection
NumPy	Array and mathematical operations
Pillow (PIL)	Image conversion
Matplotlib	Image display
Tkinter	GUI (imported for expansion)

 System Architecture
The system consists of the following modules:
1. Object Detection Module
Uses YOLOv8 Nano model (yolov8n.pt)
Detects:
oPerson → Class ID 0
oCar → Class ID 2
2. Car Colour Detection Module
Extracts detected car region.
Converts BGR → HSV color space.
Applies blue color masking.
If ≥ 10% pixels are blue → classified as blue car.
3. Counting Module
Maintains:
ocar_count
operson_count
4. Display Module
Converts OpenCV image to RGB.
Displays using Matplotlib.

 Code Explanation
🔹 Step 1: Import Required Libraries
import cv2
from ultralytics import YOLO
import tkinter as tk
from tkinter import filedialog
from PIL import Image, ImageTk
import numpy as np
cv2 → Image processing
YOLO → Object detection model
tkinter → GUI support
PIL → Image conversion
numpy → Numerical operations

🔹 Step 2: Load YOLO Model
model = YOLO('yolov8n.pt')
Loads pre-trained YOLOv8 Nano model.
Model trained on COCO dataset.
Detects 80 object classes.

🔹 Step 3: Image Processing Function
def process_image(image_path):
This function:
1.Reads the image.
2.Runs object detection.
3.Classifies objects.
4.Detects car colour.
5.Draws bounding boxes.
6.Returns processed image and counts.

🔹 Step 4: Object Detection
results = model(img)
Performs inference on the image.
Returns detected bounding boxes.

🔹 Step 5: Loop Through Detected Objects
for result in results:
    for box in result.boxes:
Each box contains:
Class ID
Confidence score
Bounding box coordinates

🔹 Step 6: Detect People
if class_id == 0:
Class ID 0 → Person (COCO dataset)
Increments person_count
Draws 🟢 Green rectangle
cv2.rectangle(img, (x1, y1), (x2, y2), (0, 255, 0), 2)

🔹 Step 7: Detect Cars
elif class_id == 2:
Class ID 2 → Car
Increments car_count

🔹 Step 8: Blue Colour Detection (HSV Method)
Convert to HSV:
hsv = cv2.cvtColor(crop, cv2.COLOR_BGR2HSV)
HSV is better for color detection than RGB.
Define Blue Range:
lower_blue = np.array([100, 50, 50])
upper_blue = np.array([140, 255, 255])
Create Mask:
mask = cv2.inRange(hsv, lower_blue, upper_blue)
Check Blue Pixel Percentage:
if cv2.countNonZero(mask) > (crop.size * 0.1):
If more than 10% of pixels are blue → classify as blue car.

🔹 Step 9: Draw Rectangle Based on Colour
Condition	Rectangle Colour
Blue car	🔴 Red
Other cars	🔵 Blue
color = (0, 0, 255)  # Red
color = (255, 0, 0)  # Blue

🔹 Step 10: Return Output
return img, car_count, person_count

🔹 Step 11: Display Results
plt.imshow(img_pil)
plt.axis('off')
plt.show()
Converts BGR → RGB
Displays processed image

 Output Example
Console Output:
Cars detected: 5
People detected: 3
Visual Output:
Blue cars → 🔴 rectangle
Other cars → 🔵 rectangle
People → 🟢 rectangle

 Assumptions
Image contains cars and people.
YOLOv8 is pre-trained.
Class IDs follow COCO dataset:
o0 → Person
o2 → Car
Blue detection threshold = 10%.

 Limitations
Blue detection may fail in:
oLow lighting
oShadow conditions
oReflections
Only static image support (current version).
GUI not fully implemented (Tkinter only imported).

 Future Improvements
Real-time video detection
Full Tkinter GUI integration
Deep learning-based colour classifier
Traffic density analytics
Save detection report
Add confidence threshold filtering
Add label text above bounding box
