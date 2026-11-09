# 🔍 Lab 02 — Deliverable 01: Problem Scan & Quick Problem Cards

**Học phần:** AI Product Scoping (Vin Smart Future — Vingroup)  
**Tác giả / Kỹ sư AI Product:** Ngọc Ninh  
**Đơn vị:** Vin Smart Future — Khối Giải pháp AI & Tự động hóa Vận hành  
**Trọng tâm nghiên cứu:** GSM (Xanh SM) & Hệ sinh thái công nghệ Vingroup  

---

## 🏛️ Bối cảnh Vận hành: Vin Smart Future

Sau khi Vingroup sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành đơn vị thống nhất **Vin Smart Future**, nhiệm vụ trọng tâm được đặt ra là: **Nhận diện các điểm nghẽn vận hành thực tế (Operational Bottlenecks), phân tích ranh giới can thiệp của AI, và chuyển đổi từ quy trình thủ công rời rạc sang kiến trúc tương lai tối ưu**.

Tài liệu này tổng hợp kết quả của **Phase 1 (SCAN)** và **Phase 2 (QUICK-ASSESS)**, áp dụng mô hình quét 4 thấu kính (4 Lenses) trên quy mô toàn tập đoàn để tìm kiếm các bài toán có ROI (Return on Investment) cao nhất và tính khả thi kỹ thuật vững chắc nhất.

---

# 🔍 Phase 1 — SCAN: Bảng Quét Cơ Hội AI Vingroup

Áp dụng **4 Thấu kính (4 Lenses)**:
1. 🔁 **Lặp lại (Repetitive):** Tác vụ tuần hoàn với tần suất cao mỗi ngày, tiêu tốn công sức xử lý máy móc.
2. ⏳ **Tốn thời gian (Time-consuming):** Tác vụ thủ công kéo dài, làm nghẽn dòng vận hành và tạo độ trễ lớn.
3. 🚀 **AI có thể tốt hơn (AI-upgrade):** Quy trình hiện tại dùng rule cứng nhắc hoặc phản hồi rập khuôn, gây giảm trải nghiệm.
4. 💢 **Pain từ người khác (Stakeholder Pain):** Điểm tắc nghẽn tạo ra phàn nàn trực tiếp từ khách hàng hoặc nhân viên tuyến đầu.

| # | Công ty thành viên | Thấu kính (Lens) | Tên bài toán / Nghiệp vụ | Mô tả ngắn hiện trạng & Điểm nghẽn | Tổn thất vận hành ước tính |
|---|--------------------|------------------|--------------------------|-----------------------------------|----------------------------|
| 1 | **Xanh SM (GSM)** | ⏳ Tốn thời gian & 💢 Pain | Điều phối trạm sạc & Cứu hộ pin thực địa | Khi taxi điện báo pin dưới 5%, điều phối viên phải tra cứu chéo định vị GPS xe, dashboard trạm sạc VinFast trống, và gõ tay tin nhắn hướng dẫn cho tài xế (mất 15 min/lượt). | Lãng phí ~20 giờ làm việc/ngày của team điều vận; xe dừng đón khách gây rò rỉ ~15% doanh thu ca. |
| 2 | **Vinhomes** | 🔁 Lặp lại & 🚀 AI-upgrade | Phân loại & Điều hướng phản ánh cư dân trên Resident App | Cư dân gửi phản ánh tự do bằng văn bản/hình ảnh (ví dụ: mất nước, chập điện hành lang, rác thải). CSKH phải đọc tay và gán thủ công tới 40+ ban quản lý tòa nhà (mất 8-12 tiếng). | Trễ SLA phản hồi (chỉ đạt 65% đúng hạn); phản hồi rập khuôn gây bức xúc trong các hội nhóm cư dân. |
| 3 | **Vinmec** | ⏳ Tốn thời gian & 💢 Pain | Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) | Bác sĩ điều trị phải tổng hợp thủ công dữ liệu từ bệnh án điện tử (EHR), kết quả xét nghiệm, lịch sử dùng thuốc để viết báo cáo xuất viện (mất 20-30 phút/bệnh nhân). | Quá tải bác sĩ lâm sàng vào cuối giờ; trễ giờ bàn giao phòng bệnh cho bệnh nhân mới nhập viện. |
| 4 | **VinFast** | 🔁 Lặp lại | Đối chiếu và so khớp hóa đơn trạm sạc đối tác | So khớp đối soát dữ liệu phiên sạc hàng tuần từ hàng nghìn trụ sạc liên kết ngoài với báo cáo tài chính nội bộ. | 3 chuyên viên tài chính mất 16 giờ/tuần đối soát thủ công; rủi ro rò rỉ thanh toán do sai lệch dữ liệu. |
| 5 | **Xanh SM (GSM)** | 💢 Pain từ người khác | Phân tích lý do khách hàng hủy chuyến taxi điện | Khách hàng hủy chuyến sau khi tài xế đã nhận cuốc. Hệ thống hiện chỉ có lý do chung chung; cần trích xuất nguyên nhân sâu từ ghi âm cuộc gọi và chat để cải thiện điều xe. | Rò rỉ ~8% cuốc xe giờ cao điểm; thiếu dữ liệu định tính để tối ưu thuật toán gán chuyến. |
| 6 | **Vinpearl** | 🚀 AI-upgrade & ⏳ Tốn thời gian | Tự động hóa xử lý yêu cầu đặt phòng đoàn (Group Booking) | Đọc email yêu cầu đặt phòng phức tạp (nhiều loại phòng, dịch vụ đi kèm, yêu cầu ăn uống đặc biệt) từ các đại lý du lịch và draft bảng báo giá phòng trống. | Thời gian phản hồi báo giá mất 4-6 giờ, tỷ lệ chuyển đổi khách hàng đoàn bị giảm 12% so với đối thủ. |
| 7 | **VinFast** | 🚀 AI-upgrade | Chẩn đoán sơ bộ lỗi xe điện từ mô tả tự nhiên của khách hàng | Khách hàng mô tả hiện tượng xe qua ứng dụng VinFast bằng tiếng Việt đời thường (ví dụ: *"đi qua gờ giảm tốc nghe tiếng lục cục ở lốp phụ bên lái"*). Hệ thống tự động map mã lỗi ban đầu. | Kỹ thuật viên mất thời gian gọi lại hỏi chi tiết; phân luồng xưởng dịch vụ chưa tối ưu. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Thẻ Bài Toán Tiềm Năng (Quick Problem Cards)

Từ danh sách 7 bài toán trên, nhóm tiến hành lọc và đánh giá sâu **Top 3 bài toán** có tính ứng dụng cao nhất:

---

## 📌 QUICK PROBLEM CARD #1: Xanh SM — Điều Phối & Tự Động Soạn Hướng Dẫn Sạc Pin Thực Địa

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                       │
│                                                                             │
│ Bài toán (1 câu): Hỗ trợ điều phối viên Xanh SM tra cứu tự động và soạn     │
│ thảo tin nhắn hướng dẫn tài xế đến trạm sạc trống hoặc kích hoạt cứu hộ     │
│ pin di động khi taxi điện gặp sự cố cạn pin giữa hành trình.                │
│                                                                             │
│ Công ty thành viên: [x] Xanh SM (GSM)   [ ] VinFast   [ ] Vinhomes           │
│                     [ ] Vinmec          [ ] Vinpearl                        │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Điều phối viên (Dispatcher): Bị quá tải khi nhiều xe cùng báo sự cố.      │
│ - Tài xế Xanh SM: Lo lắng vì pin cạn giữa đường, áp lực đón khách tiếp theo.│
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Tài xế gọi điện/gửi tin khẩn cấp báo hết pin đến tổng đài              │
│   → 2. Điều phối viên tra cứu thủ công toạ độ GPS xe trên GSM Fleet Portal  │
│   → 3. Mở Dashboard trạm sạc VinFast, lọc trụ trống phù hợp với cổng sạc xe │
│   → 4. Gõ tay tin nhắn SMS/In-app hướng dẫn chi tiết đường đi gửi tài xế    │
│   → 5. Liên hệ đội xe sạc pin di động (Mobile Charger) nếu pin dưới 5%      │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                            │
│ - Bước 3 & Bước 4: Mất 10 phút/lượt (chiếm 67% tổng thời gian quy trình).   │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                       │
│ - Bước 3 & Bước 4: Tự động gom dữ liệu định vị + trạm sạc trống, LLM sinh   │
│   bản nháp hướng dẫn đường đi chi tiết và gợi ý điều xe cứu hộ nếu pin < 5%.│
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ 1. Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút (giảm 80%).       │
│ 2. Tỷ lệ hướng dẫn đúng loại cổng sạc và đúng trạm trống đạt >= 98%.         │
│ 3. 0 trường hợp tài xế bị cạn kiệt pin giữa đường do chỉ dẫn sai.           │
│                                                                             │
│ Quick Architecture: [x] LLM Feature (kèm Human-in-the-loop & Safety Boundary)│
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 QUICK PROBLEM CARD #2: Vinhomes — Phân Loại & Điều Hướng Phản Ánh Cư Dân Trên App

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                       │
│                                                                             │
│ Bài toán (1 câu): Tự động phân tích nội dung văn bản & hình ảnh phản ánh    │
│ của cư dân trên Vinhomes Resident App để gắn nhãn danh mục, xác định độ khẩn│
│ cấp, và điều hướng chính xác đến ban quản lý toà nhà phụ trách.              │
│                                                                             │
│ Công ty thành viên: [ ] Xanh SM   [ ] VinFast   [x] Vinhomes                │
│                     [ ] Vinmec    [ ] Vinpearl                              │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Cư dân Vinhomes: Bực bội vì phản ánh mất 8-12 tiếng mới được phản hồi.    │
│ - Nhân viên CSKH/Ban quản lý: Mất 4 giờ mỗi ca chỉ để đọc và route vé.      │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Cư dân gửi ticket phản ánh (text + ảnh) qua ứng dụng Resident          │
│   → 2. Nhân viên CSKH trung tâm đọc từng ticket, phân loại danh mục         │
│   → 3. Tra cứu danh bạ ban quản lý tòa nhà tương ứng và forward ticket      │
│   → 4. Soạn phản hồi ban đầu theo mẫu quy chuẩn gửi lại cư dân               │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                            │
│ - Bước 2 & Bước 3: Mất 15-20 phút/ticket vào giờ cao điểm, dễ route nhầm ban│
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                       │
│ - Bước 2 & Bước 3: LLM trích xuất thực thể, phân loại danh mục, gán priority│
│   và auto-route trực tiếp vào queue của kỹ thuật viên tòa nhà liên quan.    │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ 1. Thời gian route vé giảm từ 4 giờ ──> dưới 30 giây.                        │
│ 2. Độ chính xác phân loại danh mục sự cố đạt >= 95%.                        │
│ 3. SLA phản hồi bước 1 cho cư dân giảm từ 12 tiếng ──> dưới 15 phút.        │
│                                                                             │
│ Quick Architecture: [x] LLM Classifier + Router (Rule/State-Machine)        │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 QUICK PROBLEM CARD #3: Vinmec — Tự Động Trích Xuất & Soạn Thảo Tóm Tắt Hồ Sơ Xuất Viện

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                       │
│                                                                             │
│ Bài toán (1 câu): Hỗ trợ bác sĩ điều trị tự động tổng hợp thông tin lâm     │
│ sàng từ hồ sơ bệnh án điện tử (EHR) để tạo bản nháp tóm tắt xuất viện       │
│ (Discharge Summary) kèm lời dặn tái khám bằng ngôn ngữ dễ hiểu.             │
│                                                                             │
│ Công ty thành viên: [ ] Xanh SM   [ ] VinFast   [ ] Vinhomes                │
│                     [x] Vinmec    [ ] Vinpearl                              │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Bác sĩ điều trị: Mất 20-30 phút/bệnh nhân, bị kiệt sức vì công việc giấy tờ│
│ - Bệnh nhân & Gia đình: Chờ đợi hàng giờ tại sảnh để nhận giấy xuất viện.   │
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Bác sĩ rà soát toàn bộ kết quả chẩn đoán, xét nghiệm, phẫu thuật       │
│   → 2. Rà soát danh mục thuốc điều trị nội trú và thuốc chỉ định ngoại trú   │
│   → 3. Gõ tay bản tóm tắt diễn tiến bệnh lý và kết quả điều trị             │
│   → 4. Soạn thảo hướng dẫn chăm sóc tại nhà và lịch hẹn khám lại            │
│   → 5. Ký tên và bàn giao hồ sơ cho điều dưỡng trưởng                        │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                            │
│ - Bước 3 & Bước 4: Mất 20 phút/bệnh nhân, áp lực cao dễ dẫn đến sót thông tin│
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                       │
│ - Bước 3 & Bước 4: LLM đọc dữ liệu có cấu trúc từ EHR và tạo bản nháp văn   │
│   bản tóm tắt xuất viện chuẩn y khoa kèm phiên bản diễn giải đơn giản.      │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ 1. Giảm thời gian soạn hồ sơ xuất viện từ 25 phút ──> dưới 5 phút (giảm 80%) │
│ 2. 100% bản thảo bắt buộc phải có chữ ký số của bác sĩ điều trị duyệt.      │
│ 3. Mức độ hài lòng của bệnh nhân khi nhận hồ sơ đạt >= 4.7/5 sao.           │
│                                                                             │
│ Quick Architecture: [x] LLM Feature (RAG trên EHR + Nghiêm ngặt HITL)       │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết Định Lựa Chọn Của Nhóm Cho Deep-Dive

Sau khi đối chiếu cả 3 thẻ bài toán theo **Khung Đánh Giá Scoping Toàn Diện (Feasibility - Business Impact - Safety Risk)**, nhóm thống nhất lựa chọn **QUICK PROBLEM CARD #1: Xanh SM — Điều Phối & Tự Động Soạn Hướng Dẫn Sạc Pin Thực Địa** để triển khai phân tích chi tiết (Phase 3 Deep-Dive) và xây dựng bản mẫu kỹ thuật (Phase 4 Prototype).

### 🔍 Lý giải chi tiết quyết định:

1. **Vì sao chọn Card #1 (Xanh SM)?**
   * **Tác động trực tiếp đến doanh thu và trải nghiệm khách hàng thời gian thực (Real-time):** Xe taxi điện hết pin trên đường gây tắc nghẽn, mất doanh thu tức thì cho cả tài xế và công ty GSM, đồng thời ảnh hưởng tiêu cực đến hình ảnh phương tiện xanh thông minh của Vingroup.
   * **Dữ liệu rõ ràng, có sẵn:** Hệ thống GPS và API trạm sạc VinFast đã được số hóa hoàn toàn trong hệ sinh thái Vingroup, cho phép tích hợp dữ liệu sạch ngay lập tức mà không phải chờ chuẩn hóa hạ tầng.
   * **Ranh giới an toàn (Operational Boundary) đo lường được bằng logic số học:** Ngưỡng pin 5% và khoảng cách 5km là bài kiểm tra hoàn hảo cho ranh giới an toàn của LLM — rủi ro có thể được kiểm soát 100% bằng cơ chế Human-in-the-loop kết hợp Guardrails.

2. **Vì sao không chọn Card #2 (Vinhomes)?**
   * Các khiếu nại của cư dân Vinhomes liên quan đến tranh chấp căn hộ, phí quản lý, và quy chế pháp lý nội bộ đòi hỏi kết nối với nhiều cơ sở dữ liệu pháp chế phức tạp. Việc triển khai bước đầu bằng LLM có rủi ro tạo ra các phản hồi sai lệch về trách nhiệm pháp lý nếu chưa hoàn thiện hệ thống Rule-based routing ban đầu.

3. **Vì sao không chọn Card #3 (Vinmec)?**
   * Y tế là lĩnh vực có rào cản quy chuẩn an toàn bệnh nhân cực kỳ nghiêm ngặt (HIPAA, Nghị định 13/2023 về bảo vệ dữ liệu cá nhân y tế, và phê chuẩn của Hội đồng Y khoa Vinmec). Thời gian xin cấp phép thử nghiệm nội bộ dài hơn rất nhiều so với thời lượng một buổi Lab scoping. Bài toán này phù hợp cho giai đoạn nghiên cứu trung hạn.
