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
