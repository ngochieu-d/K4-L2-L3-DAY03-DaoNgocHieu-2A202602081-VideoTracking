# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Đào Ngọc Hiếu`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm: Không gán các xe đồ chơi, biển báo có hình xe hoặc xe bị che khuất hoàn toàn 100%.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (2 giây @ 12.5 fps) | Tránh phân mảnh track ID (track fragmentation) khi xe chỉ tạm thời bị che bởi vật cản |
| Xe bị che lâu hơn ngưỡng trên | Tách thành **track mới** (ID mới) | Khoảng thời gian che quá lâu khiến định danh chuyển động không còn tin cậy |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Đã ra khỏi khung là kết thúc vòng đời của track đó theo quy chuẩn MOTChallenge |
| Hai xe cắt nhau / chồng lên nhau | Giữ nguyên ID từng xe, không tráo đổi ID (ID switch) | Bbox bám theo đúng từng xe dựa vào hướng chuyển động và đặc điểm ngoại quan |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | Bbox chạm đúng rìa ảnh, không đoán phần xe nằm ngoài khung hình |
| Xe bị xe khác che một phần | Bbox chỉ ôm sát phần **nhìn thấy được** của xe |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | Bắt đầu track từ frame đầu tiên xác định rõ là xe bốn bánh (kích thước tối thiểu ~15x15 px) |
| Xe đang đỗ, không di chuyển | Giữ nguyên bbox suốt thời gian xe nằm trong khung hình; kiểm tra kỹ không để trôi box |
| Keyframe đặt dày ở đâu | Đặt dày (3-5 frame) ở các khúc cua, chuyển làn, đổi vận tốc và thời điểm xe sắp rời khung |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01 / frame 80–100 / ID 5`
- Tình huống: Xe ở xa xuất hiện mờ ở rìa phía trên của con đường trước khi tiến rõ vào luồng quan sát.
- Quyết định: Ban đầu gán từ frame 80 nhưng xe còn quá mờ và chưa rõ bốn bánh. Sau rework lùi thời điểm bắt đầu về frame 101 khi xe đã nhận diện rõ ràng.
- Lý do: Bắt đầu quá sớm khi chưa đủ căn cứ nhận diện dẫn đến False Positive (FP) và giảm điểm MOTA.

### Ca 2
- Clip / frame / ID: `clip_01 / frame 44–50 / ID 1`
- Tình huống: Xe chạy về phía rìa phải và rời khỏi khung hình ở frame 43.
- Quyết định: Bấm phím Outside (`O`) tại frame 44 thay vì để box tiếp tục nội suy trôi lơ lửng tới frame 50.
- Lý do: Bbox treo sau khi xe rời khung tạo ra chuỗi False Positive liên tục, làm tụt MOTA nghiêm trọng.

### Ca 3
- Clip / frame / ID: `clip_01 / frame 149–154 / ID 4`
- Tình huống: Xe rẽ và khuất sau lề đường ở frame 148.
- Quyết định: Đặt keyframe tại frame 148 và kích hoạt Outside ở frame 149.
- Lý do: Bbox nội suy nếu không cắt đúng điểm rời khung sẽ kéo dài ra vùng trống nền đường.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- **Bổ sung luật Outside triệt để:** Ngay tại frame đầu tiên mà đối tượng không còn nhìn thấy được bất kỳ phần nào, bắt buộc phải bấm phím Outside (`O`). Tuyệt đối không để box trôi tự do theo interpolation.
- **Quy định rõ ngưỡng nhận diện ban đầu:** Đối tượng chỉ được bắt đầu gán track khi ít nhất 2 bánh xe và hình khối thân xe được quan sát rõ ràng, tránh gán vội các vệt mờ ở đường chân trời.
