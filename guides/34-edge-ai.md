# Guide 34: Edge Computing & Embedded AI

[← Previous: ROS2 for Drones](33-ros2-drones.md) | [Back to Index](../README.md) | [Next: Motor & Propeller Selection →](35-motor-propeller-selection.md)

---

## Edge AI for Drones

Edge AI runs neural network inference directly on the drone instead of streaming data to the cloud. This eliminates latency, works without internet, and enables real-time autonomous decisions.

    ┌──────────────────────────────────────────────────┐
    │         EDGE AI vs CLOUD AI                     │
    │                                                  │
    │  Cloud AI:                                       │
    │  Camera → 4G → Cloud Server → Results → Drone  │
    │  Latency: 200-2000ms                            │
    │  Requires: Internet connection                   │
    │                                                  │
    │  Edge AI:                                        │
    │  Camera → On-board Compute → Results            │
    │  Latency: 5-50ms                                │
    │  Requires: Nothing (fully autonomous)           │
    └──────────────────────────────────────────────────┘

---

## Edge AI Hardware Comparison

| Platform | AI Performance | Power | Weight | Price | Best For |
|----------|---------------|-------|--------|-------|----------|
| **Raspberry Pi 5** | 2 TOPS (with AI HAT) | 5-10W | 50g | ₹5,000 | General purpose |
| **RPi AI HAT (Hailo-8L)** | 13 TOPS | 3W | 10g | ₹5,000 | Add-on AI acceleration |
| **Hailo-8** | 26 TOPS | 2.5W | 5g (M.2) | ₹12,000 | Best perf/watt |
| **Grove Vision AI V2** | 1 TOPS | 0.5W | 15g | ₹2,500 | Ultra-lightweight |
| **Google Coral** | 4 TOPS | 2W | 5g (M.2) | ₹5,000 | TFLite models |
| **Jetson Orin Nano** | 40 TOPS | 7-15W | 60g | ₹20,000 | Multi-model, pro |
| **Jetson Orin NX** | 100 TOPS | 10-25W | 60g | ₹50,000 | Max performance |
| **Kendryte K230** | 6 TOPS | 3W | 10g | ₹3,000 | RISC-V, emerging |

---

## Model Deployment Pipeline

    1. Train Model (cloud/desktop)
       ├── Framework: PyTorch or TensorFlow
       ├── Dataset: COCO, custom collected, or synthetic
       └── Architecture: YOLOv8-nano, MobileNet, EfficientDet

    2. Optimize & Export
       ├── Quantize: FP32 → INT8 (4× smaller, ~2× faster)
       ├── Prune: Remove unnecessary weights
       └── Export format depends on target:
           ├── Hailo: HEF (Hailo Execution Format)
           ├── Coral: TFLite (quantized)
           ├── Jetson: TensorRT engine
           ├── ONNX: Universal intermediate format
           └── Grove AI: TFLite micro

    3. Deploy to Edge
       ├── Load model to accelerator
       ├── Set up camera pipeline
       ├── Run inference loop
       └── Send results to flight controller

---

## YOLO on Edge Devices

### YOLOv8-Nano on Raspberry Pi + Hailo

    # Install Hailo runtime
    pip install hailo-platform

    # Run YOLOv8n detection
    from hailo_platform import HEF, VDevice, ConfigureParams

    hef = HEF("yolov8n.hef")
    target = VDevice()
    network_group = target.configure(hef)[0]
    input_vstream = network_group.input_vstreams[0]
    output_vstream = network_group.output_vstreams[0]

    # Process frame
    input_vstream.send(preprocessed_frame)
    result = output_vstream.recv()
    # Parse detections from result

    Performance:
    ├── YOLOv8n on Hailo-8L: ~30 FPS at 640×640
    ├── YOLOv8n on Hailo-8: ~60 FPS at 640×640
    └── YOLOv8n on RPi 5 (CPU only): ~5 FPS

### YOLO on Jetson (TensorRT)

    from ultralytics import YOLO

    # Export to TensorRT
    model = YOLO('yolov8n.pt')
    model.export(format='engine', device=0, half=True)

    # Run inference
    model = YOLO('yolov8n.engine')
    results = model('/dev/video0', stream=True)
    for r in results:
        boxes = r.boxes
        for box in boxes:
            cls = int(box.cls[0])
            conf = float(box.conf[0])
            xyxy = box.xyxy[0].tolist()
            print(f"Class: {cls}, Conf: {conf:.2f}")

---

## Practical Applications

| Application | Model | Edge Device | FPS | Detection Range |
|-------------|-------|------------|-----|----------------|
| Landing pad detection | YOLOv8n custom | RPi 5 + Hailo | 30 | 5-10m |
| Person following | YOLOv8n + DeepSORT | Jetson Orin Nano | 25 | 15-30m |
| Obstacle classification | MobileNetV3 | Coral | 60 | 5-15m |
| Crop disease | EfficientDet-Lite | RPi 5 | 10 | 2-5m |
| ArUco marker | OpenCV (no ML) | Any RPi | 60+ | 1-10m |

---

## Power-Performance Tradeoffs

    ┌──────────────────────────────────────────────┐
    │  Performance vs Power vs Weight             │
    │                                              │
    │  Performance (TOPS)                          │
    │  100 │              ● Orin NX               │
    │   40 │        ● Orin Nano                   │
    │   26 │     ● Hailo-8                        │
    │   13 │   ● Hailo-8L                         │
    │    4 │  ● Coral                             │
    │    1 │ ● Grove                              │
    │      └──────────────────────── Power (W)    │
    │        0.5  2   3   7   15   25             │
    │                                              │
    │  Sweet spot for drones: 13-40 TOPS at 3-15W │
    └──────────────────────────────────────────────┘

---

[← Previous: ROS2 for Drones](33-ros2-drones.md) | [Back to Index](../README.md) | [Next: Motor & Propeller Selection →](35-motor-propeller-selection.md)
