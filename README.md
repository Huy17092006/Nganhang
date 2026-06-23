# Điều khiển ảnh bằng cử chỉ tay 🤚📸

Một trang web đơn (`index.html`) tự động bật camera, dùng **MediaPipe Hands** để nhận
diện cử chỉ tay và **MediaPipe Face Detection** để nhận diện khuôn mặt, từ đó điều khiển
các tấm ảnh trên màn hình.

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

## Cử chỉ (hướng dẫn ẩn mặc định — nhấn **H** để xem)

| Cử chỉ | Hiệu ứng |
|---|---|
| ✋ Mở bàn tay | Ảnh xoay tròn quanh tâm |
| 🖕 Ngón giữa nghiêng | Nghiêng trái = phóng to, nghiêng phải = thu nhỏ |
| ☝️ Một ngón trỏ | Xếp ảnh thành hàng ngang — chạm ngón vào ảnh để phóng to ảnh đó |
| ✊ Nắm tay | Gom ảnh thành một chồng ở giữa |
| ✌️ Hai ngón | Xếp ảnh thành lưới |
| 🤏 Chụm ngón | Rải ảnh ngẫu nhiên khắp màn hình |
| 😀 Khuôn mặt | Ảnh xoay quanh khuôn mặt |

Khi không có cử chỉ, ảnh **lơ lửng** nhẹ nhàng.

## Thêm ảnh của bạn

Bấm nút **＋ Thêm ảnh** (góc trên trái, hiện rõ khi rê chuột vào) để chọn ảnh từ máy.
Ảnh mới sẽ tham gia ngay vào các hiệu ứng cử chỉ.

## Ghi chú kỹ thuật

- Nhận diện tay chạy mỗi khung hình (`modelComplexity: 0`, 1 tay) để **độ trễ thấp nhất**;
  nhận diện khuôn mặt chạy mỗi 4 khung hình.
- Vẽ ảnh trên `<canvas>` với nội suy mượt (lerp) tách rời khỏi vòng nhận diện.
