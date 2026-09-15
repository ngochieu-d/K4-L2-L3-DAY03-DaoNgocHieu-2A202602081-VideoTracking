# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `Đào Ngọc Hiếu`
Ngày: `2026-09-15`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT Community 2.74.1 |
| Thời gian gán `clip_02` (warm-up) | 20 phút |
| Thời gian gán `clip_01` | 45 phút |
| Số track đã vẽ trong `clip_01` | 8 track |
| Số keyframe trung bình mỗi track | 4–6 keyframe/track |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. **Xe bị che khuất một phần (Occlusion) và đi giao cắt nhau:** Khi hai xe chạy đan xen, nếu chỉ dựa vào cảm quan rất dễ tráo đổi ID cho nhau (ID switch). Tôi xử lý bằng cách theo dõi trọn vẹn từng xe từ đầu đến cuối clip thay vì nhảy qua lại giữa các xe, đặt keyframe dày hơn quanh điểm giao cắt để bbox luôn bám chặt phần nhìn thấy của xe.
2. **Xác định thời điểm kết thúc của xe ở rìa ảnh:** Xe di chuyển nhanh dần ra khỏi mép dưới/phải của ảnh khiến việc canh frame cuối cùng nhìn thấy xe dễ bị trôi. Tôi dùng phím mũi tên `>` để tua chậm từng frame một và bấm ngay phím Outside (`O`) tại frame đầu tiên xe mất hẳn khỏi ảnh.
3. **Xe mới xuất hiện ở phía xa:** Xe ban đầu xuất hiện như một vệt mờ rất nhỏ ở đường chân trời. Tôi chọn ngưỡng chỉ bắt đầu tạo track khi nhìn rõ được khối hình xe và nhận biết được xe bốn bánh, tránh bắt vội các điểm mờ gây ra False Positive.

---

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: Tập trung nhìn số ID trên bbox, kiểm tra tính liên tục. Không có hiện tượng nhấp nháy hoặc đổi số giữa chừng (ID Switch = 0).
- Lượt 2: Soi frame đầu và frame cuối của từng track. Phát hiện một số xe sau khi rời khung hình bị quên bấm phím Outside (`O`), khiến bbox trôi tự do thêm 3–20 frame ở khoảng trống nền đường.
- Lượt 3: Tua vào các frame nằm giữa hai keyframe xa nhau để kiểm tra độ khít (interpolation drift). Bbox nhìn chung bám sát thân xe, độ khít đạt yêu cầu.

Kiểm chéo với: `Bạn cùng nhóm (PAIR-02)`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `4 lỗi`. Số lỗi bạn ấy tìm được trong bản của bạn: `5 lỗi (chủ yếu là bbox treo)`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

- Hai người quyết khác nhau ở thời điểm bắt đầu gán track cho xe ở xa (ID 5): một bên gán ngay khi thấy vệt mờ (frame 80), một bên gán khi xe đã rõ bánh và thân xe (frame 101).
- Luật còn thiếu: Cần bổ sung ngưỡng nhận diện cụ thể (kích thước pixel và độ nét) cho xe xuất hiện ở đường chân trời trong `GUIDELINE_MINI.md`.

---

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `96c219a74b29180b7bf2aac3f78727676f294061b0c479ae965bded68300b17e` |
| Thời điểm khóa | `2026-09-15T10:51:00.579210+00:00` |
| Số row / frame / track trước khi mở reference | 601 rows / 190 frames / 8 tracks |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| **Bản pre-gold** | 0.581 | 0.541 | 0.637 | 0.753 | 0.796 | 0.581 | 0.711 | 134 | 106 | 0 |
| **Sau rework** | **0.724** | **0.691** | **0.764** | **0.814** | **0.900** | **0.803** | **0.794** | **48** | **65** | **0** |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **ĐÃ ĐẠT CẢ 3 CỔNG** (IDF1 = 0.900, MOTA = 0.803, MOTP = 0.794)

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Bbox treo | 44–50 | 1 | Bấm Outside (`O`) tại frame 44 ngay khi xe rời khỏi rìa khung |
| Bbox treo | 12–20 | 2 | Bấm Outside (`O`) tại frame 12 ngay khi xe vừa khuất |
| Bbox treo | 149–151 | 4 | Bấm Outside (`O`) tại frame 149 sau khi xe rẽ khỏi tầm nhìn |
| Bắt đầu quá sớm | 80–100 | 5 | Bỏ các frame quá mờ ở xa, bắt đầu track từ frame 101 khi rõ nét |
| Bbox treo | 169–180 | 8 | Bấm Outside (`O`) tại frame 169 khi xe vừa khuất hẳn |

---

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | 3.13.15 / 8.4.145 / 2.11.0+cpu / 0.5.13 |
| weights / hai tracker | yolo26n.pt / ByteTrack (`bytetrack.yaml`) & BoT-SORT + ReID (`botsort-reid.yaml`) |
| conf / IoU / imgsz / classes | 0.25 / 0.7 / 960 / [2, 5, 7] |
| device | cpu |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| **bạn vs gold** | 0.724 | 0.691 | 0.764 | 0.814 | 0.900 | 0.803 | 0.794 | 48 | 65 | 0 |
| **ByteTrack control vs gold** | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| **BoT-SORT + ReID vs gold** | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| **ReID vs bạn** | 0.653 | 0.598 | 0.718 | 0.810 | 0.863 | 0.707 | 0.778 | 122 | 40 | 1 |

---

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

- Trong kết quả của tôi, **IDF1 (0.900) cao hơn MOTA (0.803)**. Điều này hoàn toàn phù hợp vì nhãn của tôi đạt **ID Switch = 0** (duy trì định danh xe xuyên suốt vòng đời rất tốt).
- Nếu gặp trường hợp **MOTA cao mà IDF1 thấp**, điều đó cho thấy mô hình/nhãn mắc lỗi tráo đổi ID nghiêm trọng (ID switches hoặc track fragmentation) nhưng độ phủ phát hiện vật thể (detection) lại tốt.
- MOTA không phạt nặng lỗi ID vì trong công thức CLEAR MOT:
  $$\text{MOTA} = 1 - \frac{\sum (\text{FP} + \text{FN} + \text{IDSW})}{\sum \text{GT}}$$
  Mỗi lần tráo đổi ID (IDSW) chỉ bị phạt đúng **1 lần trừ** tại frame chuyển tiếp. Ngược lại, IDF1 tính toán dựa trên tỉ lệ gán đúng danh tính trên **toàn bộ chiều dài quỹ đạo (IDTP / (IDTP + 0.5 IDFP + 0.5 IDFN))**, nên một track bị tách đôi hoặc tráo ID sẽ bị phạt trên toàn bộ nửa thời gian còn lại của xe.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

- BoT-SORT + ReID thể hiện sự vượt trội rõ rệt về mặt định danh so với ByteTrack control:
  - **IDF1**: Tăng từ 0.875 lên **0.900** (+2.5%).
  - **AssA**: Tăng từ 0.776 lên **0.820** (+4.4%).
  - **IDSW**: Cả hai đều có 2 lần nhảy ID.
  - **FN**: Giảm mạnh từ 54 xuống còn **26** (giảm hơn một nửa số frame mất dấu xe).
- **Dẫn chứng frame sequence**: Ở đoạn frame 105–125 (khi xe số 6 và số 7 đi vào vùng quan sát phức tạp và bị khuất nhẹ), ByteTrack chỉ dựa vào chuyển động Kalman và IoU nên đã để mất dấu một đoạn dài (track 6 chỉ phủ được 75% thời gian). Trong khi đó, BoT-SORT có thêm vector đặc trưng ngoại hình (appearance embedding) nên sau khi xe tái xuất hiện, mô hình nhanh chóng liên kết lại với track cũ, giúp độ phủ của xe 6 tăng lên 79% và duy trì track ổn định hơn.
- *Lưu ý quan trọng:* Thí nghiệm này là so sánh cấp hệ thống (system-level comparison), không thể cô lập hoàn toàn nguyên nhân chỉ do ReID vì BoT-SORT và ByteTrack có các cơ chế nội tại khác nhau (cách tính ma trận liên kết, bù chuyển động camera CMC).

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

- DetA tăng từ 0.649 lên 0.711; FN giảm mạnh từ 54 xuống 26; FP thay đổi không đáng kể (88 so với 91).
- Hầu hết lỗi còn lại của model nằm ở **Detector** (YOLO26n chạy zero-shot từ pretrained COCO, không được fine-tune trên camera giao thông góc nghiêng này). Cụ thể, detector thường bỏ sót các xe quá nhỏ ở rìa xa (FN) hoặc phát hiện các cấu trúc tĩnh ven đường như bốt điện, quầy hàng thành vehicle (sinh ra FP kéo dài hàng chục frame). Association (liên kết) của cả hai tracker đều hoạt động tương đối tốt (AssA đều > 0.77).

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

- **Frame 16–116, ID 7 của ReID (43 frame):** Mô hình ReID liên tục tạo ra một track ID 7 bám vào một vật thể tĩnh bên lề đường (quầy hàng/bốt ven đường) và gán nhãn là vehicle suốt 43 frame. Bản nhãn tay của tôi không gán đối tượng này. Đáp án gold cũng xác nhận đây là False Positive của mô hình.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

- **Frame 106–115, xe rẽ từ ngã ba:** Ban đầu khi gán tay, tôi bắt đầu track cho chiếc xe này hơi muộn (từ frame 111) vì nghĩ xe chưa vào trọn đường. Khi đối chiếu với ReID, model đã bắt đầu bám track từ frame 106 rất mượt mà. Soi kỹ lại frame 106–110 thì thấy xe đã xuất hiện rõ phần đầu và thân trước, việc bắt đầu từ frame 106 của ReID là hợp lý hơn.

---

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

1. **Sửa trong `GUIDELINE_MINI.md`:**
   - Quy định một tiêu chuẩn định lượng cho việc kết thúc track: *"Ngay khi tâm khối hoặc >80% diện tích xe chạm rìa ngoài bức ảnh và không còn khả năng quan sát bánh xe, phải ấn Outside (`O`) ngay tại frame tiếp theo"*.
   - Đưa ra hình ảnh minh họa mẫu cho xe xuất hiện ở đường chân trời (kích thước tối thiểu bao nhiêu pixel) để thống nhất giữa các annotator.
2. **Thay đổi trong quy trình làm việc:**
   - Luôn áp dụng quy tắc gán dứt điểm từng đối tượng (single-object pass) từ đầu đến cuối clip thay vì gán đồng thời nhiều xe cùng lúc.
   - Luôn chạy script kiểm tra cú pháp `check_mot_labels.py` ngay sau khi export từ CVAT trước khi thực hiện bất kỳ bước tiếp theo nào.

---

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
