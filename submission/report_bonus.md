# Bonus — Bài tập về nhà: Export ONNX và đo latency

## Phương pháp
- Export YOLO26n.pt sang ONNX bằng `model.export(format="onnx", imgsz=640)`
- Đo latency CPU với onnxruntime, warm-up 5 lần + trung bình 30 lần
- Input: bus.jpg resize 640×640, chuẩn hóa [0,1], NCHW

## Kết quả
- Model ONNX: 9.47 MB (PyTorch gốc 5.3 MB)
- Input shape: (1, 3, 640, 640)
- Output shape: (1, 84, 8400) — khớp PyTorch
- **Inference CPU: 99.67 ± 15.22 ms** (n=30, min 70.81 / max 139.46)
- So sánh GPU T4 PyTorch: ~9.6 ms → CPU ONNX chậm hơn **~10.4×**

## Kết luận
- ONNX export giữ nguyên kiến trúc YOLO26n (120 layers, 2.4M params) 
  nhưng cho phép chạy trên CPU/NPU không cần PyTorch, phù hợp triển 
  khai edge device.
- Latency CPU cao hơn GPU đáng kể (~10×), cần cân nhắc cho realtime. 
  Có thể dùng quantization INT8 hoặc export OpenVINO để giảm thêm 
  trên CPU Intel.
- ONNX hữu ích khi cần triển khai đa nền tảng (Windows, Linux, mobile) 
  mà không muốn phụ thuộc PyTorch/CUDA.
