# 01 — Problem Scan & Quick Cards

**Thành viên:** Lê Ngọc Khánh — lekhanh2k5@gmail.com

> Các con số thời gian và khối lượng trong file này là **ước tính** dựa trên quan sát và giả định hợp lý, chưa được đo thực tế. Cần xác lập baseline trước khi dùng cho quyết định đầu tư.

---

## 🔍 Phase 1 — SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinFast** | AI-upgrade | Khách mô tả lỗi xe bằng tiếng Việt tự do (vd: *"đi qua gờ giảm tốc bánh trước kêu cụp cụp"*) qua hotline/app. CSKH ghi chép thủ công, cố vấn dịch vụ phải gọi lại hỏi thêm rồi mới phân loại được nhóm lỗi và mức ưu tiên. |
| 2 | **Vinhomes** | Lặp lại | Hàng trăm phản ánh mỗi ngày trên App Vinhomes Resident (mất nước, hỏng đèn hành lang, ồn ào) được nhân viên đọc và chuyển tay đến đúng ban quản lý tòa nhà. |
| 3 | **Vinmec** | Tốn thời gian | Bác sĩ mất 20–30 phút/bệnh nhân để viết tóm tắt xuất viện từ bệnh án điện tử, xét nghiệm và ghi chú lâm sàng. |
| 4 | **Vinpearl** | Pain từ người khác | Review xấu trên Booking/Agoda/Google Maps (*"phòng bẩn"*, *"nhân viên thái độ"*) chỉ được đọc theo tuần, Manager biết quá muộn để xử lý khi khách còn lưu trú. |
| 5 | **Xanh SM** | Lặp lại | Tài xế gửi tin nhắn mô tả điểm đón thực tế khác với ghim GPS (*"em đứng ở cổng sau, cạnh cây xăng"*), điều phối viên phải đọc và cập nhật lại điểm đón thủ công. |
| 6 | **VinFast** | Lặp lại | Đối chiếu hằng tuần dữ liệu phiên sạc từ trụ sạc đối tác với hóa đơn gửi về phòng tài chính. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn top 3: **#1 (VinFast chẩn đoán sơ bộ)**, **#2 (Vinhomes phân loại phản ánh)**, **#4 (Vinpearl review khẩn cấp)**.

### Card #1 — VinFast: Phân loại sơ bộ lỗi xe từ mô tả tiếng Việt

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Phân loại sơ bộ nhóm lỗi & mức ưu tiên an toàn    │
│ từ mô tả tiếng Việt tự do của khách trước khi xe vào xưởng. │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Cố vấn dịch vụ (CVDV) tại xưởng,       │
│ nhân viên CSKH hotline, và khách hàng (chờ phản hồi lâu).   │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Khách gọi hotline/chat app mô tả lỗi                   │
│   → 2. CSKH ghi chép tự do vào ticket CRM                   │
│   → 3. Ticket chờ trong hàng đợi, CVDV gọi lại hỏi thêm     │
│   → 4. CVDV tra tài liệu kỹ thuật, phân loại, đặt lịch      │
│   → 5. KTV kiểm tra xe thực tế                              │
│                                                             │
│ Bước nào tốn nhất? Bước 3–4 (⏱ ~18 phút xử lý/ticket,       │
│ cộng thời gian chờ hàng đợi trung bình ~4 giờ)              │
│ AI hỗ trợ ở bước nào? Bước 2–4: chuẩn hóa mô tả, gợi ý      │
│ top-3 nhóm lỗi, cờ an toàn, câu hỏi bổ sung cần hỏi khách.  │
│                                                             │
│ Metric: Thời gian từ lúc tạo ticket → có phân loại sơ bộ    │
│ giảm từ ~4 giờ ──> dưới 15 phút; top-3 đúng nhóm lỗi ≥ 95%. │
│                                                             │
│ Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent    │
│ (kèm Rule-based safety gate chạy trước LLM)                 │
└─────────────────────────────────────────────────────────────┘
```

### Card #2 — Vinhomes: Phân loại & điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Tự động phân loại phản ánh cư dân và chuyển đúng  │
│ bộ phận/ban quản lý tòa nhà.                                │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Nhân viên CSKH trung tâm, cư dân chờ xử lý.    │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Cư dân gửi phản ánh qua app (text + ảnh)               │
│   → 2. CSKH đọc, xác định loại sự cố & tòa nhà              │
│   → 3. Chuyển ticket cho BQL/kỹ thuật tòa nhà               │
│   → 4. Trả lời cư dân bằng mẫu soạn sẵn                     │
│                                                             │
│ Bước nào tốn nhất? Bước 2 (⏱ ~5 phút/ticket, dồn vào cuối   │
│ tuần, thời gian phản hồi đầu tiên có thể lên tới 12 giờ)    │
│ AI hỗ trợ ở bước nào? Bước 2–4: phân loại + draft trả lời.  │
│                                                             │
│ Metric: 85% ticket được route đúng bộ phận trong < 1 phút;  │
│ thời gian phản hồi đầu tiên < 30 phút.                      │
│                                                             │
│ Quick Architecture: [ ] No AI [x] Rule [x] LLM [ ] Agent    │
│ (Rule cho form có chọn danh mục; LLM cho mô tả tự do)       │
└─────────────────────────────────────────────────────────────┘
```

### Card #3 — Vinpearl: Phát hiện review khẩn cấp

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Phát hiện sớm review tiêu cực cần xử lý ngay khi  │
│ khách vẫn đang lưu trú tại resort.                          │
│ Công ty thành viên: [x] Khác: Vinpearl                      │
│                                                             │
│ Ai đang đau? Guest Relations Manager, khách lưu trú.        │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Nhân viên mở từng nền tảng (Booking/Agoda/Google)      │
│   → 2. Đọc review mới, copy sang file tổng hợp tuần         │
│   → 3. Manager đọc báo cáo tuần, giao bộ phận xử lý         │
│                                                             │
│ Bước nào tốn nhất? Bước 1–2 (⏱ ~2 giờ/ngày/resort), và độ   │
│ trễ đến 7 ngày mới tới Manager.                             │
│ AI hỗ trợ ở bước nào? Bước 2: phân loại mức độ khẩn cấp,    │
│ chủ đề phàn nàn, tóm tắt 1 dòng.                            │
│                                                             │
│ Metric: Review khẩn cấp đến Manager trong < 2 giờ (từ ~7    │
│ ngày); recall nhóm "khẩn cấp" ≥ 90%.                        │
│                                                             │
│ Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Lựa chọn để Deep-Dive

Chọn **Card #1 — VinFast phân loại sơ bộ lỗi xe** để Deep-Dive (xem `02-deep-dive-report.md`).

* **Vì sao chọn #1:** Mô tả lỗi của khách là ngôn ngữ tự nhiên rất đa dạng (từ tượng thanh, tiếng lóng, vùng miền) → đây là chỗ rule-based keyword làm kém và LLM thực sự có giá trị. Đồng thời có yếu tố an toàn rõ ràng, buộc phải thiết kế Operational Boundary và HITL nghiêm túc.
* **Vì sao chưa chọn #2:** Một phần lớn ticket Vinhomes đã có danh mục chọn sẵn trong form → rule-based router giải quyết được phần lớn trước, LLM chỉ cần cho phần còn lại. Giá trị cận biên của AI thấp hơn.
* **Vì sao chưa chọn #3:** Tác vụ back-office, không có rủi ro vận hành thời gian thực; đồng thời phụ thuộc vào việc thu thập review từ nền tảng bên thứ ba (điều khoản API/scraping cần làm rõ trước).
