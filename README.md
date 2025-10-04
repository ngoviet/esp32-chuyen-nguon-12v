# ESP32 Auto Power Switch System

Hệ thống chuyển đổi nguồn tự động giữa Pin và Adaptor 12V dựa trên SOC (State of Charge) của pin từ Home Assistant.

## 🔋 Tính năng

- **Chuyển nguồn tự động**: Dựa trên SOC từ Home Assistant entity `sensor.jk_remaining_capacity`
- **Cấu hình linh hoạt**: Điều chỉnh ngưỡng SOC trực tiếp từ HA interface
- **3 chế độ hoạt động**: 
  - `Auto`: Tự động dựa trên SOC
  - `Force Pin`: Bắt buộc dùng Pin
  - `Force Adaptor`: Bắt buộc dùng Adaptor
- **Monitoring realtime**: Hiển thị trạng thái nguồn và SOC
- **Hysteresis logic**: Tránh dao động nguồn trong vùng trung gian

## ⚙️ Cấu hình mặc định

| Thông số | Giá trị mặc định | Có thể thay đổi |
|----------|------------------|-----------------|
| Chuyển sang Adaptor | SOC < 50% | ✅ Từ HA (20-60%) |
| Chuyển về Pin | SOC > 70% | ✅ Từ HA (60-90%) |
| GPIO Relay | Pin 16 | ✅ Trong code |
| SOC Entity | `sensor.jk_remaining_capacity` | ✅ Trong code |

## 📡 Yêu cầu phần cứng

- **ESP32 Dev Board**
- **Relay Module** (kết nối GPIO16)
- **MOSFET Module D4184** (điều khiển coil relay chính)
- **Nguồn 12V**: Pin + Adaptor

## 📁 Cấu trúc file

```
esp32_chuyen_nguon_12v/
├── README.md                      # File này
├── esp32_chuyen_nguon_12v.yaml   # Code chính ESP32
├── base_setup.yaml               # Setup cơ bản (WiFi, sensors, etc.)
├── .gitignore                    # Loại trừ files không cần thiết
├── flash.bat                     # Script flash code
├── log.bat                       # Script xem log
└── esare.bat                     # Script erase flash
```

## 🚀 Cài đặt

### 1. Chuẩn bị môi trường
```bash
# Cài đặt ESPHome
pip install esphome

# Clone repository này
git clone https://github.com/YOUR_USERNAME/esp32-chuyen-nguon-12v.git
cd esp32-chuyen-nguon-12v
```

### 2. Cấu hình
- Sửa WiFi credentials trong `base_setup.yaml`
- Thay đổi entity SOC nếu cần trong phần `substitutions`
- Kiểm tra GPIO pin phù hợp với phần cứng

### 3. Flash code
```bash
# Sử dụng ESPHome CLI
esphome run esp32_chuyen_nguon_12v.yaml

# Hoặc dùng script có sẵn
flash.bat
```

## 🔄 Logic hoạt động

### Chế độ Auto
```
SOC < 50% → Chuyển sang Adaptor (sạc pin)
50% ≤ SOC ≤ 70% → Giữ nguyên trạng thái (hysteresis)
SOC > 70% → Chuyển về Pin (ưu tiên pin)
```

### Chế độ Manual
- **Force Pin**: Luôn dùng pin (bỏ qua SOC)
- **Force Adaptor**: Luôn dùng adaptor (bỏ qua SOC)

## 📊 Home Assistant Integration

Sau khi flash thành công, các entity sau sẽ xuất hiện trong HA:

### Điều khiển
- `select.che_do_chuyen_nguon`: Chọn chế độ hoạt động
- `number.nguong_chuyen_sang_adaptor`: Ngưỡng chuyển sang adaptor (20-60%)
- `number.nguong_chuyen_ve_pin`: Ngưỡng chuyển về pin (60-90%)

### Monitoring
- `sensor.battery_soc_tu_ha`: SOC từ HA
- `binary_sensor.dang_dung_adaptor`: Trạng thái đang dùng adaptor
- `sensor.trang_thai_soc`: Mô tả trạng thái hiện tại

## 🛠️ Troubleshooting

### Không nhận SOC từ HA
1. Kiểm tra entity `sensor.jk_remaining_capacity` có tồn tại trong HA
2. Đảm bảo ESP32 đã kết nối API với HA
3. Xem log: `log.bat`

### Relay không hoạt động
1. Kiểm tra kết nối GPIO16
2. Kiểm tra nguồn cấp cho relay module
3. Test bằng chế độ Force Manual

### WiFi không kết nối
1. Kiểm tra SSID/password trong `base_setup.yaml`
2. Kiểm tra tín hiệu WiFi
3. Xem log kết nối

## 📝 License

MIT License - Sử dụng tự do cho các dự án cá nhân và thương mại.

## 🤝 Contributing

Mọi đóng góp đều được chào đón! Vui lòng tạo Issue hoặc Pull Request.

## 📧 Support

Nếu gặp vấn đề, vui lòng tạo Issue trên GitHub repository này.