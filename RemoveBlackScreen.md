
### Chào bạn, việc can thiệp trực tiếp vào mã nguồn (Hard-code) là giải pháp triệt để nhất cho nhu cầu build ROM nội bộ. Với Android 16 (LineageOS 23), cấu trúc mã nguồn có một số thay đổi về cách quản lý Layer, nhưng logic cốt lõi vẫn nằm ở ### hai nơi: SurfaceFlinger (Native) và WindowManager (Java).

### Dưới đây là các vị trí chính xác bạn cần chỉnh sửa để vô hiệu hóa hoàn toàn FLAG_SECURE:

### 1. Can thiệp tầng Native (SurfaceFlinger)
### Đây là "chốt chặn" cuối cùng. Nếu SurfaceFlinger cho rằng một Layer không bảo mật, nó sẽ cho phép quay/chụp màn hình bất kể ứng dụng yêu cầu gì.

### File: frameworks/native/services/surfaceflinger/Layer.cpp

### Tìm hàm isSecure(). Trong các bản Android mới, nó có thể trông như thế này:
```
bool Layer::isSecure() const {
    // return mDrawingState.flags & layer_state_t::eLayerSecure; 
    return false; // Ép buộc luôn là false
}
```
