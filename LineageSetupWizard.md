# modROM-Lineageos23
# bypass LineageSetupWizard
# 1. Loại bỏ LineageSetupWizard khỏi danh sách Build
Trong file bạn gửi, tìm đến dòng 146-150:
```
ifeq ($(PRODUCT_IS_AUTOMOTIVE),)
PRODUCT_PACKAGES += \
    LineageParts \
    LineageSetupWizard
endif
```
# Cách sửa: Hãy xóa hoặc comment dòng LineageSetupWizard đi.
```
ifeq ($(PRODUCT_IS_AUTOMOTIVE),)
PRODUCT_PACKAGES += \
    LineageParts
#    LineageSetupWizard
endif
```

# Vô hiệu hóa thuộc tính SetupWizard
```
PRODUCT_PRODUCT_PROPERTIES += \
    setupwizard.theme=glif_v4 \
    setupwizard.feature.day_night_mode_enabled=true
```
# Bạn hãy thêm dòng này vào ngay bên dưới để vô hiệu hóa nó ở tầng thuộc tính:
```
PRODUCT_PRODUCT_PROPERTIES += \
    ro.setupwizard.mode=DISABLED
```
