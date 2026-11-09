# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)

Quy trình vận hành thủ công hiện tại khi tài xế Xanh SM báo sự cố pin nguy cấp:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3 🔴      │     │ Bước 4 🔴      │
│ Nhận cuộc gọi  │ ──> │ Tra cứu định   │ ──> │ Tra cứu trạm   │ ──> │ Soạn tin nhắn  │
│ khẩn cấp / SOS │     │ vị GPS của xe  │     │ sạc còn trụ    │     │ chỉ đường thủ  │
│                │     │                │     │ trống phù hợp  │     │ công gửi xế    │
│ Ai: Dispatcher │     │ Ai: Dispatcher │     │ Ai: Dispatcher │     │ Ai: Dispatcher │
│ ⏱ 2 phút       │     │ ⏱ 2 phút       │     │ ⏱ 5 phút       │     │ ⏱ 5 phút       │
│ In: Thoại tài xế     │ In: Biển số xe │     │ In: Toạ độ GPS │     │ In: Dữ liệu trạm
│ Out: Log ticket│     │ Out: Toạ độ    │     │ Out: Địa chỉ   │     │ Out: SMS/App noti
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                                              │
                                                                              ▼
                                                                       ┌────────────────┐
                                                                       │ Bước 5         │
                                                                       │ Gọi xe sạc     │
                                                                       │ cứu hộ di động │
                                                                       │ (nếu pin < 5%) │
                                                                       │ Ai: Dispatcher │
                                                                       │ ⏱ 1 phút       │
                                                                       │ Out: Lệnh cứu hộ
                                                                       └────────────────┘
🔴 = Bottlenecks nghiêm trọng (Bước 3 & Bước 4 chiếm 10/15 phút)
🔄 = 3 điểm Handoff giữa các màn hình rời rạc: Tổng đài điện thoại ──> Telematics GPS ──> Dashboard Trạm sạc ──> Cổng gửi SMS
⏱ Tổng thời gian xử lý thủ công: 15 phút/lượt.
```

---

## 3.2. Problem Statement (6-field) & Metrics (15 min)

| Field | Nội dung chi tiết chuẩn Vin Smart Future |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) trực ca tại Trung tâm Điều vận Xanh SM (GSM Hà Nội & TP.HCM). |
| **2. Current Workflow** | Khi tài xế báo cạn pin hoặc lỗi sạc qua hotline/app, Dispatcher phải tra cứu GPS xe trên hệ thống giám sát hành trình nội bộ, mở song song dashboard VinFast Charging để tìm trụ sạc trống phù hợp với cổng sạc của dòng xe (VF5/VF8/e34), tự soạn tin nhắn SMS hướng dẫn đường đi bằng tay, và gọi điện thoại cho đội xe sạc cứu hộ nếu pin dưới 5%. Quy trình 5 bước thủ công trên 3 hệ thống rời rạc, mất trung bình 15 phút/lượt. |
| **3. Bottleneck** | **Bước 3 & 4 (mất 10/15 phút):** Tra cứu thủ công trạm sạc khả dụng theo bán kính an toàn và soạn thảo tin nhắn hướng dẫn lộ trình thân thiện cho tài xế. Trong giờ cao điểm, Dispatcher bị quá tải, dẫn đến phản hồi chậm trễ và tài xế hoang mang. |
| **4. Business Impact** | - Trung bình ~85 sự cố pin thực địa/ngày tại hai đô thị lớn.<br>- Gây lãng phí **21.25 giờ công lao động/ngày** của đội ngũ Dispatcher.<br>- Xe gián đoạn vận hành làm giảm 12-15% doanh thu ca chạy của tài xế, tăng tỉ lệ hủy cuốc của khách hàng lên 4.2%.<br>- Tốn chi phí cứu hộ kéo xe trung bình 1.200.000 VNĐ/ca nếu xe cạn kiệt pin giữa đường cao tốc/cầu vượt. |
| **5. Success Metric** | 1. **Hiệu năng (Efficiency):** Giảm thời gian xử lý sự cố từ 15 phút xuống **dưới 2.5 phút/lượt** (giảm 83% thời gian).<br>2. **Độ chính xác (Accuracy):** Tỉ lệ gợi ý đúng trạm sạc còn trụ trống và đúng chuẩn sạc xe đạt **>= 99%**.<br>3. **An toàn tuyệt đối (Safety Boundary):** **100%** trường hợp pin < 5% KHÔNG BAO GIỜ bị chỉ dẫn đi trạm sạc xa > 5km; hệ thống tự động kích hoạt đề xuất xe sạc pin cứu hộ lưu động (Mobile Charger). |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Tự động trích xuất thông tin định vị, mức pin, mã lỗi; truy vấn API trạm sạc VinFast gần nhất; tự động soạn thảo bản nháp chỉ đường dạng `[DRAFT_ONLY]`; trả về JSON điều xe cứu hộ khi pin < 5%.<br>**CẤM TUYỆT ĐỐI:**<br>1. CẤM AI tự ý bắn tin nhắn đến app tài xế mà chưa có nút click duyệt từ Dispatcher (Bắt buộc Human-in-the-Loop).<br>2. CẤM AI chỉ dẫn xe đi trạm sạc cách xa quá 5km khi pin báo dưới 5%.<br>3. CẤM gửi tin nhắn thiếu tiền tố xác minh `[DRAFT_ONLY]`. |

---

## 3.3. Future-State Flow & AI Fit (25 min)

### Phân tích AI Fit (AI-Fit Matrix):
* **Rule / State-Machine:** Thất bại vì tin nhắn/cuộc gọi từ tài xế thường là ngôn ngữ tự nhiên không chuẩn hóa (tiếng lóng, viết tắt, ngữ cảnh hoảng loạn, toạ độ tương đối). Rule cứng không thể hiểu được ý định hoặc soạn nội dung hướng dẫn mềm mỏng, chi tiết.
* **Autonomous Agent (Agent tự trị hoàn toàn):** Rủi ro quá cao. Nếu Agent tự động ra lệnh điều xe cứu hộ hoặc tự ý gửi lộ trình sai, xe có thể chết máy trên cao tốc gây tai nạn giao thông, ùn tắc đô thị và thiệt hại tài chính lớn cho Vingroup.
* **LỰA CHỌN TỐI ƯU:** **LLM Feature kết hợp Hard Safety Boundaries & Human-in-the-Loop (HITL Co-pilot)**. Mô hình LLM xử lý ngôn ngữ và trích xuất thông tin, nhưng các quy tắc an toàn cốt lõi được bảo vệ bởi ranh giới cấm nghiêm ngặt và con người là chốt chặn phê duyệt cuối cùng.

### Sơ đồ quy trình tương lai (Future-State Flow):

```text
┌────────────────┐     ┌───────────────────────┐     ┌───────────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2 🔵             │     │ Bước 3 🔵             │     │ Bước 4 🟢      │
│ Tài xế gửi SOS │ ──> │ AI trích xuất GPS,    │ ──> │ Kiểm tra ranh giới &  │ ──> │ Dispatcher     │
│ qua App/Cuộc gọi     │ mức pin & query trạm  │     │ Sinh bản nháp DRAFT   │     │ kiểm tra 1-click│
│                │     │ sạc VinFast gần nhất  │     │ hoặc JSON Cứu hộ      │     │ duyệt & gửi xế │
└────────────────┘     └───────────────────────┘     └───────────────────────┘     └────────────────┘
                                                                 │
                                                    [Nếu pin < 5% hoặc trạm > 5km]
                                                                 │
                                                                 ▼
                                                      Kích hoạt JSON khẩn cấp:
                                                      {"action": "dispatch_mobile_charger"}
                                                                 │
                                                                 ▼
                                                      ┌───────────────────────┐
                                                      │ ↩️ Fallback Plan      │
                                                      │ Nếu LLM timeout/lỗi,  │
                                                      │ chuyển ngay về quy    │
                                                      │ trình Dispatcher tay. │
                                                      └───────────────────────┘
🔵 = AI Step (Tự động hóa xử lý dữ liệu và soạn thảo)
🟢 = Human Step (Human-in-the-Loop phê duyệt trước khi gửi)
↩️ = Kế hoạch dự phòng Fallback bảo toàn vận hành
⏱ Thời gian xử lý tương lai: Chỉ còn 1.5 - 2.5 phút/lượt.
```

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] **Dữ liệu mẫu & logs sạch:** GSM đã có sẵn kho dữ liệu telemetry lịch sử các sự cố sạc pin và log điều vận GPS của hơn 30.000 xe taxi điện tại Hà Nội & TP.HCM.
2. [x] **Kiểm soát rủi ro an toàn:** Rủi ro được bao bọc 2 lớp: Hard Guard trong prompt + Human-in-the-Loop (Dispatcher bắt buộc phải bấm nút duyệt trước khi tin nhắn/lệnh được kích hoạt) + Fallback về quy trình tay nếu API timeout.
3. [x] **Sẵn sàng thay đổi từ Stakeholders:** Đội ngũ Dispatcher đang rất quá tải vào các khung giờ cao điểm (7h30-9h00 và 17h00-19h30) và rất mong đợi một công cụ co-pilot tự động soạn thảo dữ liệu.

---

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
* [x] **GO (Bắt đầu xây dựng Prototype với scope hẹp)**
* [ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline)**
* [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

### Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):
1. **Bằng chứng kỹ thuật vững chắc:**
   - Đã chứng minh bằng thực nghiệm: Prompt prototype trên Gemini 3.5 Flash ngăn chặn thành công 100% các cuộc tấn công ranh giới nguy hiểm (pin < 5%, bypass tag, jailbreak giả danh lãnh đạo).
   - Mô hình kiến trúc kết hợp **LLM Feature + HITL** loại bỏ triệt để rủi ro hành động tự trị ngoài tầm kiểm soát.
2. **Hiệu quả tài chính & ROI vượt trội:**
   - Chi phí API của Gemini 3.5 Flash cực kỳ thấp (~0.0001 USD/lượt xử lý sự cố). Với 85 sự cố/ngày, chi phí vận hành AI chưa tới 0.01 USD/ngày (~250 VNĐ/ngày).
   - Ngược lại, việc rút ngắn thời gian xử lý sự cố từ 15 phút xuống 2.5 phút giúp tiết kiệm hơn **18 giờ công lao động/ngày**, tăng hiệu suất quay vòng xe của tài xế và ngăn chặn trung bình 3-5 ca chết máy phải kéo xe mỗi ngày (tiết kiệm ước tính từ 3.600.000 đến 6.000.000 VNĐ/ngày chi phí cứu hộ cho GSM).
3. **Lộ trình triển khai thận trọng (Phased Rollout):**
   - **Giai đoạn 1 (Tuần 1 - 2):** Thử nghiệm Pilot nội bộ trên nhóm 50 xe Xanh SM tại khu vực trung tâm Hà Nội; Dispatcher đánh giá và chấm điểm chất lượng từng bản nháp.
   - **Giai đoạn 2 (Tuần 3 - 6):** Triển khai toàn diện trên toàn bộ đội xe Xanh SM Taxi tại Hà Nội và TP.HCM sau khi độ chính xác đạt ngưỡng >= 99%.
   - **Giai đoạn 3 (Sau 2 tháng):** Đóng gói tính năng thành module tiêu chuẩn để tích hợp vào ứng dụng VinFast Care phục vụ cho cả khách hàng cá nhân sở hữu xe điện VinFast trên toàn quốc.
