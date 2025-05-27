# RealSense D435i with ROS 2 Humble - Complete Setup Guide

Welcome! 🎉 This guide walks you through installing and integrating the Intel RealSense D435i camera with ROS 2 Humble on Ubuntu 22.04. This includes setting up the SDK, verifying with `realsense-viewer`, and running the camera node with ROS 2. Perfect for robotics projects, perception, and your first open source contribution!

---

## 🧰 Requirements

- Ubuntu 22.04
- ROS 2 Humble installed
- Internet connection
- USB 3 port

---

## 📦 1. Install librealsense SDK (v2.56.3)

```bash
cd ~
git clone https://github.com/IntelRealSense/librealsense.git
cd librealsense
git checkout v2.56.3
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DFORCE_RSUSB_BACKEND=TRUE
make -j$(nproc)
sudo make install
```

> ⚠️ If `realsense-viewer` fails to start, try removing the config:
```bash
rm ~/.realsense-config.json
```

Then run:
```bash
realsense-viewer
```

---

## 🔌 2. Set USB rules

```bash
sudo cp ~/librealsense/config/99-realsense-libusb.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && sudo udevadm trigger
```

Check if your camera is detected:
```bash
lsusb
```

You should see something like:
```
Bus 002 Device 004: ID 8086:0b3a Intel Corp. Intel RealSense D435i
```

---

## 🧠 3. Set environment variables

Append to your `~/.bashrc`:
```bash
export CMAKE_PREFIX_PATH=/usr/local:$CMAKE_PREFIX_PATH
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```
Then:
```bash
source ~/.bashrc
```

---

## 🤖 4. Install ROS 2 dependencies

```bash
sudo apt update
sudo apt install ros-humble-cv-bridge ros-humble-image-transport ros-humble-camera-info-manager ros-humble-rmw-cyclonedds-cpp -y
```

---

## 📁 5. Clone and build realsense-ros

```bash
cd ~/ros2_ws/src
rm -rf realsense-ros
git clone -b ros2-development https://github.com/IntelRealSense/realsense-ros.git
```

### 🔧 Fix CMake version check
Edit the following file:
```bash
nano ~/ros2_ws/src/realsense-ros/realsense2_camera/CMakeLists.txt
```
Replace:
```cmake
find_package(realsense2 2.56.0 REQUIRED)
```
With:
```cmake
find_package(realsense2 2.56.3 EXACT REQUIRED)
```

### 🔨 Build the workspace
```bash
cd ~/ros2_ws
rm -rf build/ install/ log/
colcon build --symlink-install
source install/setup.bash
```

---

## 🚀 6. Launch the camera

```bash
ros2 launch realsense2_camera rs_launch.py
```

You should see logs confirming:
- RealSense ROS version
- LibRealSense version
- Device serial and model (D435i)
- Stream profiles being used

---

## 👀 7. Visualize in RViz

```bash
rviz2
```

In RViz:
- Set `Fixed Frame` to `camera_color_optical_frame`
- Add "Image" display
- Choose topic:
  - `/camera/camera/color/image_raw` (RGB)
  - `/camera/camera/depth/image_rect_raw` (Depth)

---

## ✅ Verification

Check camera topics:
```bash
ros2 topic list
```
Should include:
```
/camera/camera/color/image_raw
/camera/camera/depth/image_rect_raw
```

---

## 📄 Files to include in your GitHub repo

- `README.md` → This guide
- `.gitignore`:
```
build/
install/
log/
*.pyc
*.swp
.realsense*
```
- `LICENSE` (MIT recommended)

---

## 🧠 Tips

- Always check USB 3 is used (blue port)
- To clean and rebuild:
```bash
cd ~/ros2_ws
rm -rf build/ install/ log/
colcon build --symlink-install
```

---

## 📬 Credits

Maintained by [Your Name]. Inspired by community struggles to get RealSense D435i working reliably in ROS 2.

Contributions and issues welcome!

---

## 🐙 License

MIT License — feel free to fork and improve.

---
