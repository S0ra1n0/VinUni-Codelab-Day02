# 01 — Problem Scan

# 🔍 Phase 1 — SCAN

| #   | Subsidiary | Lens               | Mô tả ngắn bài toán                                                                                                                                                                                                                           |
| --- | ---------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Xanh SM    | Pain từ người khác | Xử lý khiếu nại **đồ thất lạc trên xe**: nhân viên CSKH phải tra lịch sử chuyến, gọi tài xế xác nhận, hẹn lịch trả đồ thủ công (~20 phút/vụ; giả định 150 vụ/ngày ≈ 50 giờ công/ngày), khách phàn nàn vì chờ lâu.                             |
| 2   | Xanh SM    | Lặp lại            | **Kiểm tra hồ sơ tuyển tài xế** (bằng lái, lý lịch tư pháp, giấy khám sức khỏe): nhân viên tuyển dụng đối chiếu từng giấy tờ bằng mắt (10–15 phút/hồ sơ), mùa cao điểm tồn đọng khiến onboarding kéo dài 5–7 ngày, thiếu tài xế giờ cao điểm. |
| 3   | Xanh SM    | Tốn thời gian      | **Xử lý tranh chấp cước/phụ phí** (khách nghi tài xế đi đường vòng, tính sai phí cầu đường): CSKH mở GPS trace so với tuyến tối ưu (10–15 phút/vụ), giả định ~20% vụ hoàn tiền sai (thừa hoặc thiếu).                                         |
| 4   | Xanh SM    | AI có thể tốt hơn  | **Phản hồi đánh giá 1–2 sao trên app**: CSKH trả lời bằng mẫu câu rập khuôn, chậm 24–48 giờ, không phân loại được nguyên nhân gốc (tài xế, xe, app, giá).                                                                                     |
| 5   | Xanh SM    | Tốn thời gian      | **Phân tích báo cáo va chạm giao thông**: chuyên viên an toàn đọc lời khai tài xế, ảnh hiện trường, video camera hành trình để xác định lỗi và lập hồ sơ bảo hiểm (30–45 phút/vụ), hồ sơ bồi thường chậm.                                     |

> Các con số có chữ "giả định" là ước lượng, không phải số liệu chính thức của Xanh SM.

---

# 🃏 Phase 2 — QUICK-ASSESS

**Top 3 được chọn:** #1 (Đồ thất lạc), #3 (Tranh chấp cước), #4 (Review 1–2 sao)

**Lý do chọn / loại:** Chọn 3 bài có tần suất cao, quy trình rõ và lỗi AI có thể kiểm soát bằng bước duyệt của CSKH. Loại **#2** (hồ sơ tài xế) vì sai sót ảnh hưởng an toàn hành khách và cần đối chiếu giấy tờ gốc; loại **#5** (va chạm) vì kết luận lỗi có hệ quả pháp lý/bảo hiểm, phải do con người quyết định.

---

## Card #1 — Xử lý khiếu nại đồ thất lạc trên xe

| Mục                             | Nội dung                                                                                                                                                                                                                     |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bài toán (1 câu)**            | Khách quên đồ trên xe phải chờ hàng giờ vì CSKH tra chuyến và gọi tài xế thủ công.                                                                                                                                           |
| **Công ty thành viên**          | [x] Xanh SM                                                                                                                                                                                                                  |
| **Ai đang đau (Actor)?**        | CSKH tổng đài; khách mất đồ; tài xế bị gọi giữa cuốc.                                                                                                                                                                        |
| **Workflow thủ công hiện tại**  | 1. Khách báo mất đồ, mô tả mơ hồ (⏱ 3') → 2. 🔴 Tra lịch sử chuyến tìm tài xế (⏱ 6') → 3. 🔴🔄 Gọi tài xế xác nhận, thường phải gọi lại (⏱ 7') → 4. 🔄 Hẹn trả đồ (⏱ 3') → 5. Cập nhật ticket (⏱ 1'). **Tổng ≈ 20 phút/vụ.** |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–3 (⏱ ~13 phút): tra nhầm chuyến, tài xế không nghe máy.                                                                                                                                                               |
| **AI nhảy vào ở bước nào?**     | LLM trích thông tin từ lời khách → Rule gợi ý top 3 chuyến khớp; LLM soạn tin cho tài xế và tin hẹn trả đồ `[DRAFT_ONLY]`.                                                                                                   |
| **Metric thành công (có số)**   | **20 ──> dưới 5 phút/vụ**; **≥ 90%** vụ có chuyến đúng trong top 3.                                                                                                                                                          |
| **Quick Architecture**          | [ ] No AI [x] Rule [x] LLM [ ] Agent — Rule tra chuyến, LLM xử lý ngôn ngữ.                                                                                                                                                  |
| **Ranh giới**                   | **Cấm:** lộ SĐT/vị trí tài xế, hứa bồi thường, tự gửi tin. **Cần duyệt:** CSKH xác nhận chuyến; đồ giá trị cao → giám sát viên.                                                                                              |
| **Giả định số liệu**            | 150 vụ/ngày × 20 phút = **50 giờ công/ngày**.                                                                                                                                                                                |

---

## Card #2 — Xử lý tranh chấp cước / phụ phí

| Mục                             | Nội dung                                                                                                                                                                                    |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bài toán (1 câu)**            | Khiếu nại cước (đi đường vòng, sai phí cầu đường) được đối chiếu GPS thủ công, chậm và hoàn tiền không nhất quán.                                                                           |
| **Công ty thành viên**          | [x] Xanh SM                                                                                                                                                                                 |
| **Ai đang đau (Actor)?**        | CSKH; khách chờ hoàn tiền; tài xế bị nghi oan.                                                                                                                                              |
| **Workflow thủ công hiện tại**  | 1. Nhận khiếu nại (⏱ 2') → 2. 🔴 So GPS trace với tuyến ngắn nhất (⏱ 6') → 3. 🔴 Tính chênh lệch theo biểu phí (⏱ 4') → 4. 🔄 Quyết định hoàn tiền, phản hồi (⏱ 3'). **Tổng ≈ 15 phút/vụ.** |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–3 (⏱ ~10 phút): so bằng mắt, ~20% vụ hoàn tiền sai.                                                                                                                                  |
| **AI nhảy vào ở bước nào?**     | Rule so lộ trình và tính tiền; LLM chỉ soạn lời giải thích `[DRAFT_ONLY]`.                                                                                                                  |
| **Metric thành công (có số)**   | **15 ──> dưới 3 phút/vụ**; hoàn tiền sai **~20% ──> dưới 5%**.                                                                                                                              |
| **Quick Architecture**          | [ ] No AI [x] Rule [x] LLM [ ] Agent — Rule là chính vì tính tiền phải chính xác tuyệt đối.                                                                                                 |
| **Ranh giới**                   | **Cấm:** LLM tự tính tiền, kết luận tài xế gian lận. **Cần duyệt:** mọi phản hồi; hoàn trên 100.000đ → giám sát viên.                                                                       |
| **Giả định số liệu**            | 120 vụ/ngày × 15 phút = **30 giờ công/ngày**.                                                                                                                                               |

---

## Card #3 — Phản hồi đánh giá 1–2 sao trên app

| Mục                             | Nội dung                                                                                                                                                                 |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Bài toán (1 câu)**            | Review 1–2 sao được trả lời chậm (24–48 giờ), rập khuôn, không phân loại nguyên nhân.                                                                                    |
| **Công ty thành viên**          | [x] Xanh SM                                                                                                                                                              |
| **Ai đang đau (Actor)?**        | CSKH; khách không hài lòng; bộ phận chất lượng tài xế.                                                                                                                   |
| **Workflow thủ công hiện tại**  | 1. Đọc review (⏱ 1') → 2. 🔴 Đoán nguyên nhân: tài xế/xe/app/giá (⏱ 3') → 3. 🔄 Tra chuyến liên quan (⏱ 3') → 4. 🔴 Chọn mẫu câu, gửi (⏱ 3'). **Tổng ≈ 10 phút/review.** |
| **Bước tốn thời gian/lỗi nhất** | Bước 2 và 4 (⏱ ~6 phút): phân loại cảm tính, trả lời rập khuôn.                                                                                                          |
| **AI nhảy vào ở bước nào?**     | LLM phân loại nguyên nhân và soạn phản hồi `[DRAFT_ONLY]`; Rule ghép review với chuyến.                                                                                  |
| **Metric thành công (có số)**   | **24–48 giờ ──> dưới 2 giờ**; **≥ 85%** review phân loại đúng.                                                                                                           |
| **Quick Architecture**          | [ ] No AI [x] Rule [x] LLM [ ] Agent — LLM là chính (hiểu ngôn ngữ tự nhiên).                                                                                            |
| **Ranh giới**                   | **Cấm:** tự gửi, hứa voucher, đổ lỗi tài xế. **Cần duyệt:** CSKH duyệt trước khi gửi; review về an toàn → chuyển bộ phận an toàn.                                        |
| **Giả định số liệu**            | 240 review/ngày × 10 phút = **40 giờ công/ngày**.                                                                                                                        |

---
