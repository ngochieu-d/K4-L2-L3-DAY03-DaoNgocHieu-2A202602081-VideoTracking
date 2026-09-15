# Khai báo nhóm — chỉ điền khi làm nhóm

Nếu làm cá nhân, không cần đưa file này vào repo nộp. Nếu làm nhóm, mỗi thành
viên nộp repo cá nhân và sao chép/điền cùng bảng thành viên dưới đây trong repo
của mình.

## Nhóm

- Tên nhóm: PAIR-02
- Kênh liên lạc dùng để phối hợp: Discord / Trực tiếp tại lớp lab
- Cách phân chia review và kiểm chứng evidence: Độc lập gán nhãn clip_01, hoán đổi file gt.txt để chạy evaluate_tracking.py --mode peer, ghi finding vào review_partner.md và đối chiếu sau khi mở gold reference.

| Họ và tên | MSSV | Vai trò / phần việc | Artifact tự sở hữu |
| --- | --- | --- | --- |
| Đào Ngọc Hiếu | 2A202602081 | Annotator, Modeling & Reporting | `annotations/clip_01/gt.txt`, `evidence/pre-gold/clip_01/`, `outputs/`, `reports/REPORT.md` |

## Phần đóng góp và học được của người nộp repo này

- Họ và tên / MSSV: Đào Ngọc Hiếu - 2A202602081
- Tôi trực tiếp tạo hoặc chỉnh sửa những artifact nào: Toàn bộ nhãn `clip_01`, `clip_02`, pre-gold snapshot, cấu hình chạy tracker trên Colab, và báo cáo tổng hợp.
- Finding hoặc quyết định annotation tôi chịu trách nhiệm: Xử lý Outside cho 5 track xe ở frame rời khung hình, quyết định thời điểm bắt đầu gán xe ở xa (frame 101) để triệt tiêu FP.
- Tôi học được gì về identity, occlusion, MOT hoặc ReID: Hiểu rõ cơ chế duy trì ID xuyên suốt (AssA, IDF1), sự khác biệt giữa CLEAR MOT và Identity metrics (MOTA phạt IDSW 1 lần trong khi IDF1 phạt trên toàn bộ nửa thời gian còn lại), và vai trò của appearance feature (ReID) khi xử lý occlusion.
- Điều tôi đã kiểm lại độc lập trước khi nộp: Chạy `check_mot_labels.py` (0 lỗi), đối chiếu metric với gold (IDF1 = 0.900, MOTA = 0.803, MOTP = 0.794), xác nhận không commit các file cấm (`gold/clip_01`, `*.pt`, `*.zip`, `outputs/vis_*`).
