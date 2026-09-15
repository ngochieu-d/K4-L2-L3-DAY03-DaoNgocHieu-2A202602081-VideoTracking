# Peer review — Day 3

Chép file này thành `reports/review_partner.md`. Reviewer chỉ ghi finding; tác giả
tự sửa bài của mình và điền closure.

| Trường | Giá trị |
| --- | --- |
| Author | `Đào Ngọc Hiếu` |
| Reviewer | `Bạn cùng nhóm (Peer Reviewer)` |
| Pair ID | `PAIR-02` |
| CVAT version | `2.74.1` |
| Thời điểm review | `2026-09-15 17:30` |

## Danh sách finding

Mỗi finding bắt buộc có `frame + ID + lỗi + sửa thế nào`. Nếu chưa thống nhất,
dùng `needs-review`; không ép tác giả sửa theo cảm tính.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | ---: | --- | --- | --- |
| 1 | 43–49 | 44–50 | 1 | Bbox treo | Xe đã rời khỏi khung ở frame 43 nhưng box vẫn trôi tới frame 50 | Bấm phím Outside (O) tại frame 44 | fixed |
| 2 | 11–19 | 12–20 | 2 | Bbox treo | Xe chạy ra khỏi góc dưới màn hình nhưng chưa kết thúc track | Đặt Outside tại frame 12 | fixed |
| 3 | 79–100 | 80–101 | 5 | Bắt đầu quá sớm | Vệt mờ ở xa chưa xác định rõ là xe bốn bánh, gây FP | Bắt đầu track từ frame 101 khi rõ nét | fixed |
| 4 | 148–150 | 149–151 | 4 | Bbox treo | Xe rẽ khuất góc lề đường, box vẫn còn | Bật Outside tại frame 149 | fixed |
| 5 | 168–179 | 169–180 | 8 | Bbox treo | Xe chạy mất dấu nhưng track kéo dài tới frame 180 | Bật Outside tại frame 169 | fixed |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | Đủ 8 track xe bốn bánh hợp lệ |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | ID switch = 0 trên toàn sequence |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Các đoạn che khuất đều duy trì đúng ID |
| Entry/exit đúng; không box treo sau khi xe rời khung | PASS (sau fix) | Đã sửa xong các frame Outside bị trôi |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Bbox khít, MOTP đạt 0.794 |
| Frame giữa hai keyframe không bị interpolation drift | PASS | Kiểm tra midpoint các đoạn dài đều ôm khít xe |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | Đã validate bằng check_mot_labels.py |
| Mọi finding có cách sửa và closure do tác giả điền | PASS | 5/5 finding đã đóng trạng thái fixed |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | ID không bị nhảy số, IDSW = 0 |
| 2 — endpoint/scope | ĐÃ SỬA | Đã sửa các điểm outside ở ID 1, 2, 4, 5, 8 |
| 3 — geometry/interpolation | PASS | Bbox ôm khít phần nhìn thấy được |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `Cần bấm Outside (phím O) ngay tại frame đầu tiên xe rời khung để tránh bbox treo sinh ra hàng loạt FP.`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `N/A - Toàn bộ các finding đều xác đáng và đã được khắc phục hoàn toàn.`
3. Một rule cần Lab Coach làm rõ (nếu có): `Ngưỡng kích thước tối thiểu (pixel) chính xác để bắt đầu track một xe mới xuất hiện ở đường chân trời.`
