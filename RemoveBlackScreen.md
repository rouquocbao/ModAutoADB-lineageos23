
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
### (có thể có hoặc KHÔNG) File: frameworks/native/services/surfaceflinger/FrontEndLayer.cpp (Nếu có trong bản build của bạn)
Android 14 trở đi sử dụng FrontEndLayer để quản lý trạng thái. Hãy đảm bảo thuộc tính isSecure luôn trả về false tại đây.

### 2. Can thiệp tầng Java Framework (WindowManager)
### Đây là nơi xử lý logic từ phía ứng dụng truyền lên. Việc sửa ở đây giúp hệ thống "lừa" các ứng dụng rằng màn hình đang an toàn.

### File: frameworks/base/services/core/java/com/android/server/wm/WindowState.java

### Tìm phương thức isSecureLocked(). Đây là nơi kiểm tra cờ FLAG_SECURE từ LayoutParams.
```
boolean isSecureLocked() {
    /* if ((mAttrs.flags & WindowManager.LayoutParams.FLAG_SECURE) != 0) {
        return true;
    }
    */
    return false; // Luôn trả về false để hệ thống bỏ qua chặn screenshot/record
}
```
### File: frameworks/base/core/java/android/view/SurfaceControl.java
### Bạn có thể tìm phương thức setSecure(boolean isSecure) và ép tham số truyền vào luôn là false.
### 3. Vô hiệu hóa kiểm tra trên Virtual Displays (Dành cho Scrcpy)
### Scrcpy hoạt động bằng cách tạo một màn hình ảo (Virtual Display). Android thường chặn nội dung bảo mật trên các màn hình không phải màn hình chính.

### File: frameworks/base/services/core/java/com/android/server/display/VirtualDisplayAdapter.java
### (Hoặc các file liên quan trong com.android.server.display)

### Tìm đoạn code kiểm tra cờ VIRTUAL_DISPLAY_FLAG_SECURE. Bạn nên comment hoặc xóa logic ngăn cản việc truyền dữ liệu bảo mật sang Virtual Display.
