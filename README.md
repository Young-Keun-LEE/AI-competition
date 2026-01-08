## 🚀 Defense AI Competition (Excellence Prize): AI-Powered Autonomous Navigation & Target Identification Robot

**[Project Overview]**
This project, which won the **Grand Prize** at the Defense AI Competition, is a fully autonomous robotics system. The NVIDIA Jetson-based **TikiMini robot** autonomously navigates a designated lane, stopping at specific mission waypoints (marked by green squares). Upon stopping, it scans the area using a **custom-trained YOLOv8n model** to perform real-time identification of allied vs. enemy forces (tanks and infantry). When an enemy tank is detected, the system automatically executes an 'attack' protocol—sounding an alarm and firing its cannon.

---

### 🧬 Core Technical Architecture

The system is built on three core technical pillars:

#### 1. Autonomous Navigation: Lane Detection & Steering Control (Computer Vision & Control)
Implemented a real-time computer vision and closed-loop control system to ensure the robot remains centered on its path.

* **Lane Detection:**
    * Utilized **OpenCV** to perform **Canny Edge Detection** on a Region of Interest (ROI) at the bottom of the camera feed, extracting lane edges.
    * Analyzed the **histogram** of the resulting edge image to calculate the precise positions of the left and right lanes (`left_peak`, `right_peak`) in real-time.
* **Steering Control:**
    * Calculated an 'error value' by comparing the midpoint of the detected lanes with the robot's center (image center).
    * This error value was fed into the `follow_line` function to implement **Differential Steering**, creating a **closed-loop control system** that continuously adjusts motor RPMs (e.g., `rpm-offset`, `rpm+offset`) to keep the robot centered in the lane.

#### 2. AI Mission: Object Detection & Target Identification (Deep Learning)
Developed and deployed a deep learning model for the robot's primary mission: identifying friend or foe (IFF).

* **AI Model:**
    * Selected the lightweight **YOLOv8n (nano)** model, ideal for embedded systems.
    * **Custom-trained the model** on a proprietary dataset to classify four categories: `enemy_tank`, `ally_tank`, `enemy_person`, and `ally_person`.
* **Real-time Inference:**
    * Leveraged the NVIDIA Jetson's **GPU (CUDA)** to load the trained `best_v8n.pt` model and perform high-speed, real-time inference on the live camera feed.
* **Mission Execution:**
    * The `run_inference` function triggers the `attack_tank` function immediately upon detecting an `enemy_tank`.
    * This function interfaces directly with the hardware, executing `tiki.fire_cannon()` and `tiki.play_buzzer()` to simulate an 'attack' response.

#### 3. Scenario Management: State Machine & Hardware Integration (System Integration)
Designed the overarching operational logic to integrate all subsystems and execute complex, multi-stage missions.

* **Finite State Machine (FSM):**
    * The main `while cap.isOpened():` loop acts as a simple FSM.
    * The robot defaults to a 'Lane Following' state and transitions to a 'Mission Execute' state only when a specific condition (waypoint detection) is met.
* **Waypoint Detection:**
    * The `square_detected` function uses **OpenCV's HSV color space filtering** (`cv2.inRange`) to identify **green square markers**.
    * These markers serve as physical **waypoints** (e.g., "Region A", "Region B"), signaling the robot to stop and perform its AI detection task.
* **Hardware Integration:**
    * Achieved full integration and control of the TikiMini robot's hardware (motors, cannon, buzzer) via the Python API.
    * Maximized embedded system performance by using a **GStreamer** pipeline (`nvarguscamerasrc`) to feed the NVIDIA Jetson's CSI camera input directly into OpenCV for low-latency processing.
