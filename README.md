# Durian Detection App

Ứng dụng nhận diện sầu riêng sử dụng AI với React Native, TypeScript và Expo SDK 54.

## Tính năng

- 📸 **Chụp ảnh và chọn từ thư viện**: Chụp ảnh mới hoặc chọn ảnh từ gallery để phân tích
- 🎥 **Detection trực tiếp**: Real-time object detection qua camera feed
- 🧠 **AI Prediction**: Sử dụng PyTorch model (.pth) được convert sang ONNX để nhận diện đối tượng
- 📊 **Lưu lịch sử**: Lưu trữ kết quả prediction với bounding boxes
- 📈 **Thống kê**: Xem thống kê tổng quan về các lần detection

## Cài đặt

### Yêu cầu hệ thống
- Node.js 16+
- Expo CLI
- React Native development environment
- iOS/Android device hoặc simulator

### Cài đặt dependencies

```bash
cd DurianApp
npm install
```

### Chạy ứng dụng

```bash
# Development build
npx expo start

# iOS
npx expo start --ios

# Android  
npx expo start --android
```

## Cấu trúc dự án

```
DurianApp/
├── src/
│   ├── components/          # React components
│   │   └── CameraComponent.tsx
│   ├── screens/            # App screens
│   │   ├── HomeScreen.tsx
│   │   ├── CameraScreen.tsx
│   │   ├── LiveDetectionScreen.tsx
│   │   └── HistoryScreen.tsx
│   ├── services/           # Business logic
│   │   ├── ModelService.ts
│   │   └── DatabaseService.ts
│   ├── types/              # TypeScript types
│   │   └── index.ts
│   └── utils/              # Utility functions
├── assets/                 # Images, icons, fonts
├── models/                 # PyTorch/ONNX model files
└── app.json               # Expo configuration
```

## Sử dụng Model PyTorch

### 1. Convert PyTorch model sang ONNX

```python
import torch
import torch.onnx

# Load your trained PyTorch model
model = torch.load('your_model.pth', map_location='cpu')
model.eval()

# Create dummy input
dummy_input = torch.randn(1, 3, 640, 640)

# Export to ONNX
torch.onnx.export(
    model,
    dummy_input,
    "durian_model.onnx",
    export_params=True,
    opset_version=11,
    do_constant_folding=True,
    input_names=['input'],
    output_names=['output']
)
```

### 2. Đặt model vào app

```bash
# Copy model file vào assets
cp durian_model.onnx ./assets/models/
```

### 3. Load model trong app

```typescript
import { modelService } from './src/services/ModelService';

// Load model khi app khởi động
useEffect(() => {
  const loadModel = async () => {
    const modelPath = `${FileSystem.documentDirectory}durian_model.onnx`;
    const success = await modelService.loadModel(modelPath);
    if (success) {
      console.log('Model loaded successfully');
    }
  };
  loadModel();
}, []);
```

## Permissions

App yêu cầu các permissions sau:

- **Camera**: Để chụp ảnh và live detection
- **Photo Library**: Để chọn ảnh từ thư viện
- **Storage**: Để lưu ảnh và model files

## Kiến trúc

### Model Service
- Quản lý ONNX model loading và inference
- Xử lý image preprocessing
- Convert PyTorch predictions sang app format

### Database Service  
- SQLite database để lưu prediction history
- Quản lý bounding boxes và metadata
- Thống kê usage

### Navigation
- Stack Navigator với 4 screens chính
- Smooth transitions giữa các màn hình

## Customization

### Thêm model mới

1. Convert model sang ONNX format
2. Update `ModelService.ts` với input/output specifications
3. Cập nhật classes trong `types/index.ts`

### Thay đổi UI

- Colors và themes trong StyleSheet của từng component
- Icons sử dụng @expo/vector-icons
- Responsive design với Dimensions API

## Troubleshooting

### Model không load được
- Kiểm tra model file path
- Verify ONNX model compatibility
- Check console logs cho error details

### Camera không hoạt động
- Kiểm tra permissions
- Test trên device thật (không phải simulator)
- Restart Metro bundler

### Database errors
- Clear app data và reinstall
- Check SQLite compatibility

## Build Production

```bash
# Build for app stores
npx expo build:ios
npx expo build:android

# Or use EAS Build (recommended)
eas build --platform ios
eas build --platform android
```

## Tech Stack

- **React Native** + **TypeScript**: Core framework
- **Expo SDK 54**: Development platform
- **ONNX Runtime**: ML inference engine  
- **SQLite**: Local database
- **React Navigation**: Routing
- **Expo Camera**: Camera functionality
- **React Native SVG**: Bounding box overlays

## License

MIT License - see LICENSE file for details.

## Contributing

1. Fork the repository
2. Create feature branch
3. Commit changes  
4. Push to branch
5. Create Pull Request# Durian_Application
