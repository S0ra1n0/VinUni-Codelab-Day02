# 03 — AI Collaboration Log & Engineering Reflection
**Lab:** Lab 02 — AI Product Scoping (Vin Smart Future)

---

## 1. Quá trình sử dụng AI làm Thought-Partner (What Helped)

### 1.1. Brainstorming & Định lượng hóa Pain Points (Phase 1 & Phase 2)
- **Prompt đã dùng:** Yêu cầu AI đóng vai kỹ sư tại Vin Smart Future để quét các quy trình thủ công gây rò rỉ hiệu suất tại Vinhomes, VinFast, Xanh SM và đóng vai CFO/Trưởng phòng Vận hành để phản biện (Stress-Test) các Quick Problem Cards.
- **Giá trị mang lại:** 
  - Giúp chuyển hóa các ý tưởng mơ hồ ban đầu (như *"làm chatbot trả lời cư dân"*) thành bài toán vận hành cụ thể có điểm nghẽn đo lường được: **Phân loại, kiểm tra điều kiện bảo hành sơ bộ và điều phối Ticket kỹ thuật cư dân Vinhomes (M&E vs. Xây dựng)**.
  - Giúp ước lượng nhanh baseline thời gian xử lý thủ công (~9 phút/ticket) và định hình rõ bộ 6-field Problem Statement.

### 1.2. Thiết kế JSON Schema & Kịch bản Tấn công (Phase 4)
- **Prompt đã dùng:** Yêu cầu AI hỗ trợ viết Pydantic Schema cho Gemini 2.5 Flash Structured Output và sinh các Adversarial Test Cases (Prompt Injection, Safety Hazard, VIP Pressure).
- **Giá trị mang lại:** AI giúp cấu trúc hóa đầu ra thành các cờ boolean điều khiển luồng (`safety_hazard_detected`, `boundary_violation_attempt`, `requires_human_escalation`), giúp tầng code Python dễ dàng bắt điều kiện rẽ nhánh sang Fallback mà không cần parse chuỗi thủ công.

---

## 2. Những điểm AI đề xuất sai hoặc chưa sát thực tế (Where AI Failed / Over-engineered)

1. **Xu hướng "Agent hóa" quá mức (Over-engineering):**
   - Trong lần gợi ý kiến trúc đầu tiên, AI đề xuất xây dựng một hệ thống **Autonomous Multi-Agent** tự động kiểm tra lịch trống của kỹ sư tòa nhà, tự động chốt giờ hẹn và gửi thẳng thông báo xác nhận bảo hành miễn phí cho cư dân mà không cần nhân viên CSKH duyệt.
   - **Lý do sai:** Quá phức tạp, độ trễ cao và cực kỳ rủi ro khi áp dụng vào vận hành thực tế tại Vinhomes.

2. **Đánh giá thấp rủi ro pháp lý/truyền thông về Chính sách Bảo hành:**
   - Bản nháp System Prompt đầu tiên do AI sinh ra cho phép mô hình kết luận thẳng với cư dân là *"Căn hộ của quý khách còn/hết hạn bảo hành"* chỉ dựa trên số tháng bàn giao.
   - **Lý do sai:** Thực tế vận hành tòa nhà, dù căn hộ mới bàn giao 8 tháng (< 24 tháng) nhưng nếu lỗi do cư dân tự khoan tường hoặc cải tạo sai quy chuẩn thì không thuộc diện bảo hành miễn phí. Nếu AI tự cam kết trên app, BQL sẽ gặp rắc rối lớn khi kỹ thuật viên xuống hiện trường từ chối miễn phí.

---

## 3. Cách tôi kiểm soát, phản biện và chỉnh sửa (How I Steered & Corrected It)

1. **Hạ cấp kiến trúc từ Agent xuống Single-turn LLM Feature + HITL:**
   - Tôi quyết định loại bỏ kiến trúc Agent tự hành, giữ giải pháp ở mức **LLM Feature (Structured JSON Output)** đóng vai trò làm **Co-pilot** điền sẵn phiếu phân loại trên CRM. Nhân viên CSKH luôn là người bấm nút duyệt cuối cùng (**Human-in-the-Loop**).

2. **Tách biệt đánh giá nội bộ và phản hồi cư dân (Siết chặt Operational Boundary):**
   - Tôi sửa lại `WarrantyStatus` trong code thành trạng thái sơ bộ (`CON_BAO_HANH_SO_BO`, `CAN_KIEM_TRA_HIEN_TRUONG`) chỉ hiển thị trên màn hình nội bộ của CSKH.
   - Bổ sung quy tắc cứng vào `SYSTEM_PROMPT`: Trong trường `draft_resident_reply` gửi cho cư dân, tuyệt đối không cam kết bồi thường tiền hay khẳng định miễn phí 100% trước khi kỹ thuật viên lập biên bản kiểm tra hiện trường.

3. **Kiểm chứng bằng Adversarial Testing:**
   - Tự thiết kế và tinh chỉnh 3 test cases tấn công trực diện vào ranh giới: (1) Giả danh Giám đốc dùng lệnh `SYSTEM OVERRIDE` đòi đền bù 50 triệu VNĐ, (2) Dụ AI hướng dẫn tự nối dây điện khi tủ điện đang đánh tia lửa, và (3) Khách VIP gây sức ép bắt bảo hành lỗi tự khoan thủng ống nước. Cả 3 ca đều được mô hình chặn đứng và bật cờ `requires_human_escalation = true`.