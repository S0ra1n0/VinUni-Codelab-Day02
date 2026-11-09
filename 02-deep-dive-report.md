# 02 — Deep-Dive Report: VinFast Pre-Service Fault Triage

**Thành viên:** Lê Ngọc Khánh — lekhanh2k5@gmail.com

**Bài toán:** Phân loại sơ bộ nhóm lỗi và mức ưu tiên an toàn từ mô tả tiếng Việt tự do của khách hàng VinFast, trước khi xe vào xưởng dịch vụ.

> Các con số dưới đây là **giả định/ước tính** để scoping, chưa phải số liệu đo thực tế của VinFast. Mục đầu tiên của kế hoạch pilot là đo baseline thật.

---

## 🏗️ Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow

Sơ đồ chi tiết: [`04-workflow-diagram.png`](04-workflow-diagram.png)

| Bước | Ai làm | Việc | Input → Output | Thời gian | Ghi chú |
|---|---|---|---|---:|---|
| 1 | Khách hàng → CSKH | Gọi hotline / chat app mô tả lỗi | Lời kể → cuộc gọi | 4 phút | |
| 2 | CSKH hotline | Ghi chép tự do vào ticket CRM | Lời kể → ticket text | 3 phút | 🔄 Handoff 1: CSKH → hàng đợi xưởng |
| 3 | CVDV xưởng | Đọc ticket, gọi lại khách hỏi thêm chi tiết | Ticket → mô tả đầy đủ hơn | 10 phút (+ chờ ~4 giờ trong hàng đợi) | 🔴 Bottleneck 1 |
| 4 | CVDV xưởng | Tra tài liệu kỹ thuật, xác định nhóm lỗi, mức ưu tiên, đặt lịch | Mô tả → lịch hẹn + phân loại | 8 phút | 🔴 Bottleneck 2 · 🔄 Handoff 2: CVDV → KTV |
| 5 | Kỹ thuật viên (KTV) | Kiểm tra xe thực tế, chẩn đoán cuối cùng | Xe → kết luận lỗi | (ngoài phạm vi) | |

**Tổng thời gian xử lý thủ công: ~25 phút/ticket**, chưa tính ~4 giờ chờ trong hàng đợi trước khi CVDV gọi lại.

**Vì sao bước 3–4 là bottleneck:**
* Ticket do CSKH ghi chép thường thiếu thông tin kỹ thuật quan trọng (tốc độ khi xảy ra, vị trí tiếng kêu, đèn cảnh báo nào sáng) → CVDV gần như luôn phải gọi lại.
* Cùng một lỗi được mô tả rất khác nhau (*"kêu cụp cụp"*, *"lục cục bánh trước"*, *"rung vô lăng khi qua gờ"*) → khó tra cứu, phụ thuộc kinh nghiệm cá nhân CVDV.
* Ticket nguy hiểm (phanh, lái, pin cao áp) nằm lẫn trong hàng đợi chung với ticket thông thường → có rủi ro xử lý chậm.

---

### 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Cố vấn dịch vụ (CVDV) tại xưởng dịch vụ VinFast; nhân viên CSKH hotline là người tạo ticket đầu vào. |
| **2. Current Workflow** | Khách gọi hotline/chat app → CSKH ghi chép tự do vào CRM → ticket chờ trong hàng đợi xưởng → CVDV gọi lại hỏi thêm, tra tài liệu kỹ thuật, tự phân loại nhóm lỗi & mức ưu tiên, đặt lịch → KTV kiểm tra xe. Công cụ: CRM, điện thoại, tài liệu kỹ thuật dạng PDF. ~25 phút xử lý/ticket. |
| **3. Bottleneck** | Bước 3–4 (~18 phút + ~4 giờ chờ): hiểu mô tả ngôn ngữ tự nhiên không chuẩn, hỏi bổ sung thông tin còn thiếu, và ánh xạ sang nhóm lỗi kỹ thuật. Ticket nguy hiểm không được ưu tiên tự động. |
| **4. Business Impact** | Giả định 1 xưởng nhận ~60 ticket/ngày → ~18 giờ công CVDV/ngày cho bước 3–4. Khách chờ phản hồi trung bình ~4 giờ, gây phàn nàn và giảm CSAT. Phân loại sai khiến xưởng chuẩn bị sai phụ tùng/khoang sửa chữa → xe phải hẹn lại lần 2. Rủi ro nghiêm trọng nhất: ticket an toàn (mất phanh, cảnh báo pin cao áp, mùi khét) bị xử lý chậm. |
| **5. Success Metric** | 1. **Tốc độ:** thời gian từ lúc tạo ticket → có phân loại sơ bộ: từ ~4 giờ xuống **< 15 phút** (P90).<br>2. **Chất lượng:** top-3 nhóm lỗi gợi ý chứa kết luận cuối của KTV **≥ 95%**; top-1 **≥ 80%**.<br>3. **An toàn:** recall nhóm "nguy hiểm an toàn" **≥ 99%** (gần như không được bỏ sót), đo trên tập ticket lịch sử đã gán nhãn.<br>4. **Hiệu suất:** thời gian CVDV xử lý bước 3–4 giảm từ ~18 phút xuống **< 6 phút/ticket**. |
| **6. Operational Boundary** | **AI được phép:** đọc nội dung ticket, chuẩn hóa mô tả, gợi ý top-3 nhóm lỗi kèm độ tự tin, gắn cờ an toàn, soạn danh sách câu hỏi bổ sung, soạn **bản nháp** tin nhắn cho khách.<br>**AI TUYỆT ĐỐI KHÔNG được:** (a) đưa ra chẩn đoán cuối cùng; (b) báo giá hoặc hứa hẹn bảo hành; (c) nói với khách rằng xe "vẫn chạy an toàn / cứ tiếp tục đi"; (d) tự gửi tin cho khách — mọi output bắt đầu bằng `[DRAFT_ONLY]` và chỉ gửi sau khi CVDV duyệt; (e) hạ mức ưu tiên của ticket đã bị Safety Gate gắn cờ.<br>**Điểm bắt buộc duyệt (HITL):** CVDV duyệt mọi phân loại và mọi tin nhắn gửi khách. Ticket có cờ an toàn → chuyển thẳng trưởng xưởng/cứu hộ, không chờ LLM. |

---

### 3.3. Future-State Flow & AI Fit

#### So sánh Rule vs LLM vs Agent

| Tiêu chí | Rule / State-machine | **LLM Feature** ✅ | Agentic Loop |
|---|---|---|---|
| Hiểu mô tả tiếng Việt tự do, từ tượng thanh, vùng miền | Kém — keyword list không bao hết cách nói | **Tốt** | Tốt |
| Phát hiện từ khóa an toàn rõ ràng (khói, cháy, mất phanh, cảnh báo pin đỏ) | **Tốt, xác định, dễ audit** | Tốt nhưng không đảm bảo 100% | Tốt nhưng không đảm bảo 100% |
| Cần hành động nhiều bước tự trị (gọi API đặt lịch, đặt phụ tùng)? | — | Không cần | Có thể, nhưng **không cần** cho bài toán này |
| Rủi ro khi sai | Thấp | Trung bình — kiểm soát bằng HITL | **Cao** — tự hành động trên hệ thống thật |
| Chi phí & độ phức tạp | Thấp | Thấp–trung bình | Cao |

**Kết luận AI Fit:** **Hybrid — Rule-based Safety Gate + LLM Feature.**
* Rule-based chạy **trước** để bắt các ca an toàn rõ ràng một cách xác định (không phụ thuộc vào LLM).
* LLM chỉ làm phần nó giỏi: hiểu ngôn ngữ tự do và gợi ý phân loại + câu hỏi bổ sung.
* **Không dùng Agent:** quy trình có cấu trúc cố định, không cần tự lập kế hoạch nhiều bước; để AI tự đặt lịch/đặt phụ tùng sẽ tăng rủi ro mà không tăng nhiều giá trị.

#### Future-State Flow

```text
┌──────────────┐   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────┐
│ Bước 1       │   │ Bước 2           │   │ Bước 3           │   │ Bước 4           │   │ Bước 5       │
│ Khách mô tả  │──▶│ ⚙️ Safety Gate   │──▶│ 🔵 AI Step (LLM) │──▶│ 🟢 HITL: CVDV    │──▶│ KTV kiểm tra │
│ lỗi (hotline │   │ (Rule-based)     │   │ - Top-3 nhóm lỗi │   │ duyệt/sửa phân   │   │ xe thực tế   │
│ / chat app)  │   │ Từ khóa nguy hiểm│   │ - Độ tự tin      │   │ loại + duyệt tin │   │              │
│ ⏱ 4 phút     │   │ ⏱ < 1 giây       │   │ - Câu hỏi bổ sung│   │ nhắn rồi gửi     │   │              │
└──────────────┘   └────────┬─────────┘   │ - [DRAFT_ONLY]   │   │ ⏱ ~5 phút        │   └──────────────┘
                            │             │   tin nhắn khách │   └──────────────────┘
                            │ có cờ       │ ⏱ ~10 giây       │
                            ▼ an toàn     └────────┬─────────┘
                   ┌──────────────────┐            │ JSON lỗi / độ tự tin < 0.6 /
                   │ 🚨 Escalate ngay │            │ API timeout
                   │ trưởng xưởng /   │            ▼
                   │ cứu hộ — KHÔNG   │   ┌──────────────────┐
                   │ chờ LLM          │   │ ↩️ Fallback:      │
                   └──────────────────┘   │ ticket quay về    │
                                          │ quy trình thủ công│
                                          │ cũ (CVDV gọi lại) │
                                          └──────────────────┘
```

**Output mẫu của AI Step (structured JSON):**

```json
{
  "draft_tag": "[DRAFT_ONLY]",
  "safety_flag": false,
  "top3_fault_groups": [
    {"group": "Hệ thống treo trước (rotuyn/cao su càng A)", "confidence": 0.72},
    {"group": "Thanh cân bằng / cao su thanh cân bằng", "confidence": 0.18},
    {"group": "Giảm xóc trước", "confidence": 0.10}
  ],
  "follow_up_questions": [
    "Tiếng kêu xuất hiện ở bánh trước bên trái hay bên phải?",
    "Có kèm rung vô lăng hoặc lệch lái không?",
    "Trên màn hình có đèn cảnh báo nào sáng không?"
  ],
  "customer_message_draft": "[DRAFT_ONLY] Chào anh/chị, VinFast đã ghi nhận ..."
}
```

**Cơ chế Fallback chi tiết:**

| Tình huống | Hành động |
|---|---|
| LLM trả JSON sai schema | Retry 1 lần; vẫn lỗi → ticket về quy trình thủ công, log lại để phân tích |
| Độ tự tin top-1 < 0.6 | Không hiển thị gợi ý nhóm lỗi, chỉ hiển thị câu hỏi bổ sung; CVDV xử lý như cũ |
| API Gemini timeout / hết quota | Ticket về quy trình thủ công, không chặn luồng làm việc |
| Output thiếu `[DRAFT_ONLY]` hoặc chứa cụm cấm ("vẫn chạy an toàn", báo giá) | Hậu kiểm bằng rule chặn output, không hiển thị cho CVDV gửi |

---

## 💻 Phase 4 — Prompt Prototype (tham chiếu)

Bản prototype prompt với ranh giới `[DRAFT_ONLY]` và ngưỡng pin < 5% (bài Xanh SM của lab) đã chạy thành công trên **Gemini 2.5 Flash** ở branch cá nhân: cả 2 adversarial test đều **Passed** (mô hình giữ tag `[DRAFT_ONLY]` khi bị yêu cầu bỏ; điều xe sạc di động thay vì chỉ đường tới trạm 8km khi pin 2%). Bài học áp dụng cho bài toán VinFast: ranh giới phải được viết thành quy tắc tuyệt đối, có ví dụ cách nói gián tiếp, và nên có thêm lớp rule hậu kiểm thay vì tin hoàn toàn vào prompt.

---

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

| # | Câu hỏi | Đánh giá | Bằng chứng / Ghi chú |
|---|---|---|---|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test? | ⚠️ **Một phần** | Ticket CRM lịch sử có mô tả của khách, nhưng chưa được gán nhãn khớp với kết luận cuối của KTV. Cần ~500 ticket gán nhãn để đánh giá metric 2 và 3. Dữ liệu chứa thông tin cá nhân (SĐT, biển số) → phải ẩn danh trước khi gửi API bên ngoài. |
| 2 | Rủi ro khi AI sai nằm trong tầm kiểm soát? | ✅ **Có** | Safety Gate rule-based chạy độc lập với LLM; mọi output là `[DRAFT_ONLY]` và CVDV duyệt; Fallback về quy trình cũ khi lỗi. AI không bao giờ tự hành động trên hệ thống. |
| 3 | Stakeholders sẵn sàng thay đổi quy trình? | ⚠️ **Chưa xác nhận** | Cần phỏng vấn CVDV và trưởng xưởng; rủi ro CVDV không tin gợi ý của AI hoặc ngược lại tin quá mức (automation bias). |

### Quyết định

**[x] GO — với scope hẹp (shadow mode tại 1 xưởng, 4 tuần)**
[ ] NOT YET
[ ] NO-GO

**Justification:**
> Bài toán có nút thắt rõ ràng nằm ở việc hiểu ngôn ngữ tự nhiên — đúng điểm mạnh của LLM mà rule-based không làm tốt. Rủi ro lớn nhất (bỏ sót ca nguy hiểm) được tách ra cho Safety Gate rule-based xử lý một cách xác định, nên AI sai chỉ làm chậm CVDV chứ không gây nguy hiểm trực tiếp. Chi phí thấp vì là LLM Feature, không cần Agent hay hạ tầng mới.
>
> Tuy nhiên checklist mục 1 và 3 chưa đạt hoàn toàn, nên **không** triển khai thẳng cho khách. Kế hoạch GO có điều kiện:
> 1. **Tuần 1:** đo baseline thật (thời gian chờ, thời gian xử lý, tỉ lệ phân loại sai) và gán nhãn ~500 ticket lịch sử đã ẩn danh.
> 2. **Tuần 2–4:** chạy **shadow mode** — AI phân loại song song nhưng CVDV không thấy kết quả; so sánh với kết luận KTV.
> 3. **Điều kiện chuyển sang thử nghiệm thật:** recall nhóm an toàn ≥ 99% (kể cả khi tính riêng LLM, để làm lớp bảo vệ thứ hai) và top-3 ≥ 95%. Nếu không đạt → chuyển về **NOT YET** và mở rộng Safety Gate rule-based trước.
