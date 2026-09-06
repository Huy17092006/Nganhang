# Điều khiển ảnh bằng cử chỉ tay 🤚📸

Một trang web đơn (`index.html`) **tự động bật camera**, dùng **MediaPipe Hands** để nhận
diện cử chỉ tay, từ đó điều khiển các tấm ảnh lơ lửng trên màn hình. Hướng dẫn được
**ẩn mặc định**, độ trễ thao tác tay được tối ưu thấp nhất.

## Cách chạy

> ⚠️ Camera chỉ hoạt động trong **ngữ cảnh an toàn** (HTTPS hoặc `localhost`).
> Mở trực tiếp bằng `file://` sẽ bị trình duyệt chặn camera.

Trong thư mục dự án, chạy một máy chủ cục bộ rồi mở trong trình duyệt:

```bash
python3 -m http.server 8000
# rồi mở http://localhost:8000
```

Lần đầu trình duyệt sẽ hỏi quyền camera — hãy chọn **Cho phép (Allow)**.
Cần kết nối Internet để tải thư viện MediaPipe từ `cdn.jsdelivr.net`.

## Cử chỉ (hướng dẫn ẩn mặc định — nhấn **H** hoặc nút **?** để xem)

| Cử chỉ | Hiệu ứng |
|---|---|
| ✋ Mở bàn tay | Các tấm ảnh **xoay tròn** quanh tâm |
| ☝️ Một ngón tay | Ảnh **xếp thành hàng ngang** |
| 🤏 Chạm màn hình / chụm ngón | **Phóng to · thu nhỏ** kích thước ảnh |
| 🫧 Không cử chỉ | Ảnh **lơ lửng** nhẹ nhàng |

- Trên máy tính: **cuộn chuột** để phóng to/thu nhỏ; trên cảm ứng: **chụm 2 ngón**.

## Thêm ảnh của bạn

Bấm nút **＋** (góc trên trái, hiện rõ khi rê chuột vào) để chọn ảnh từ thư viện máy.
Ảnh mới tham gia ngay vào các hiệu ứng cử chỉ.

## Ghi chú kỹ thuật

- Camera yêu cầu độ phân giải cao nhất (`ideal 3840×2160`, 60fps) và `applyConstraints` đẩy
  độ sáng / độ nét / tương phản / lấy nét lên mức tối đa cảm biến hỗ trợ; tự động dự phòng nếu
  thiết bị không hỗ trợ 4K hoặc không cho chỉnh các thông số đó.
- Camera nền và các tấm ảnh dùng **chung một bộ lọc màu** (`--look`) nên màu sắc, độ sáng,
  độ nét đồng đều như nhau.
- Nhận diện tay chạy mỗi khung hình (`modelComplexity: 0`, 1 tay) để **độ trễ thấp nhất**.
- Vẽ ảnh trên `<canvas>` với nội suy mượt (lerp) tách rời khỏi vòng nhận diện.

---

## Phòng thử giọng — `voice.html`

Trang thứ hai trong repo, độc lập với trang cử chỉ tay. Dùng bộ đọc có sẵn của
máy (Web Speech API) để thử giọng nữ trẻ trước khi mang thông số đi render bản
chất lượng cao.

```bash
python3 -m http.server 8000
# rồi mở http://localhost:8000/voice.html
```

### Thông số mặc định lấy từ đâu

Không phải đoán. Audio mẫu được tách ra và đo bằng autocorrelation + kiểm chứng
qua chuỗi hài (398 → 789 → 1195 Hz…), cho ra:

| Chỉ số | Giọng mẫu | Giọng nữ TTS chuẩn |
|---|---|---|
| Cao độ F0 (trung vị) | **≈ 340 Hz** | ≈ 208 Hz |
| Khoảng dao động (p25–p75) | 308 – 400 Hz | — |
| Nhịp đọc | 4.9 âm tiết/giây | — |
| Dao động cao độ | 4.7 nửa cung | — |

Tỉ lệ 340 / 208 ≈ **1.6** chính là giá trị mặc định của núm cao độ.

### Trang này làm gì hơn một cái `speechSynthesis.speak()`

- **Tự chấm điểm và chọn giọng** — cộng điểm cho `vi-VN`, tên giọng nữ và bản
  neural; trừ điểm giọng nam (`NamMinh`, `David`…) và giọng eSpeak. Nhãn *nữ* /
  *nam* hiện ngay trong danh sách, chọn nhầm giọng nam thì có cảnh báo.
- **Ngữ điệu theo câu** — kịch bản được tách thành từng vế, mỗi vế nhận cao độ
  và tốc độ riêng: nhấc giọng đầu câu, hạ dần về cuối, câu hỏi vống lên ~2.4 nửa
  cung, câu kể rơi xuống ~1.2. Nhiễu tất định nên nghe lại vẫn ra đúng ngữ điệu cũ.
- **Ngắt nghỉ thật** — khoảng lặng có thật ở dấu phẩy (150 ms) và dấu chấm
  (330 ms), chỉnh được bằng núm.
- **Nốt mẫu 340 Hz** — phát một nốt răng cưa có rung nhẹ để căn cao độ bằng tai,
  vì mỗi engine hiểu tham số `pitch` một kiểu khác nhau.
- **Nhớ hiệu chỉnh theo từng giọng** qua `localStorage`.
- Cắt kịch bản thành khúc ≤ 170 ký tự để tránh lỗi Chrome đứt tiếng ở câu dài.

### Quy đổi sang dịch vụ render

| Dịch vụ | Thiết lập tương đương núm 1.60 |
|---|---|
| Azure TTS | `vi-VN-HoaiMyNeural` + `<prosody pitch="+55%" rate="+5%">` |
| Vbee / FPT.AI | giọng nữ miền Bắc trẻ, pitch `+6` đến `+8` semitone |
