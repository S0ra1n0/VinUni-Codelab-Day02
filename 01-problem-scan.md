# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Sử dụng **4 Lenses** quét qua hoạt động vận hành của các công ty thành viên Vingroup để xác định các bài toán thực tế:
1. **Lặp lại (Repetitive):** Tác vụ diễn ra tần suất cao, theo khuôn mẫu.
2. **Tốn thời gian (Time-consuming):** Thao tác thủ công ngốn nhiều giờ làm việc mỗi ca trực.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ hoặc quy trình hiện tại phản hồi rập khuôn, thiếu cá nhân hóa, hoặc chậm trễ.
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck gây ức chế, phàn nàn trực tiếp từ tài xế, cư dân, bệnh nhân hoặc nhân viên tuyến đầu.

### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Xanh SM (GSM)** | Tốn thời gian & Khẩn cấp | Điều phối viên xử lý thủ công các cuộc gọi sự cố pin khẩn cấp (<5%) hoặc xe cạn điện giữa đường; tra cứu trạm sạc trống và gọi cứu hộ (mất 12-15 phút/lượt, gây tắc nghẽn giờ cao điểm). |
| 2 | **VinFast EV** | Lặp lại | Đối soát và kiểm tra chênh lệch dữ liệu điện năng tiêu thụ giữa trụ sạc công cộng VinFast và hóa đơn thanh toán của người dùng cuối (mất 2 ngày công mỗi tuần cho đội đối soát). |
| 3 | **Vinhomes** | Pain từ người khác | Phân loại và điều phối tự động các phản ánh sự cố kỹ thuật hạ tầng khẩn cấp (thang máy kẹt, mất điện, rò rỉ nước) từ app cư dân đến đúng đội kỹ thuật tòa nhà (hiện bị trễ 2-4 tiếng do lọc thủ công). |
| 4 | **Vinmec** | Tốn thời gian | Bác sĩ mất 20-30 phút/bệnh nhân để tổng hợp lịch sử khám, xét nghiệm và soạn thảo tóm tắt hồ sơ bệnh án xuất viện (Discharge Summary). |
| 5 | **Vinpearl / VinWonders** | AI-upgrade | Trợ lý số đa ngôn ngữ gợi ý hành trình tham quan, cập nhật thời gian chờ trò chơi theo thời gian thực và xử lý hoàn đổi vé khi thời tiết xấu. |
| 6 | **Xanh SM (GSM)** | Lặp lại | Tự động hóa trích xuất thông tin và đối chiếu GPS/logs cuốc xe để xử lý khiếu nại tài xế về sự cố tính sai phụ phí giờ cao điểm hoặc phí cầu đường. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên (#1 Xanh SM Sự cố pin, #3 Vinhomes Sự cố hạ tầng, #2 VinFast Đối soát sạc) và hoàn thiện **3 Quick Problem Cards**.

### Thẻ bài toán 1: Card #1 — Xanh SM Xử lý sự cố pin nguy cấp & Cứu hộ thực địa
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế Xanh SM gặp sự cố pin nguy cấp     │
│ (<5%) hoặc chết máy giữa đường cần điều phối cứu hộ hoặc    │
│ trạm sạc trống gần nhất trong bán kính an toàn.             │
│ Công ty thành viên: [x] Xanh SM  [ ] VinFast  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế (hoảng loạn, nguy cơ chết máy   │
│ giữa đường), Điều phối viên Dispatcher (quá tải giờ cao điểm)│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận cuộc gọi SOS ──> 2. Tra GPS xe trên bản đồ        │
│   ──> 3. Lọc trạm sạc trống ──> 4. Soạn SMS chỉ đường       │
│   ──> 5. Gọi xe sạc cứu hộ di động nếu pin dưới 5%          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4 & 5          │
│ (Trích xuất toạ độ + gợi ý trạm trống + draft SMS + auto-   │
│ đề xuất xe sạc lưu động nếu pin chạm ngưỡng nguy cấp).      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 2.5 phút.  │
│ - 100% trường hợp pin < 5% được kích hoạt cứu hộ kịp thời.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Thẻ bài toán 2: Card #2 — Vinhomes Phân loại phản ánh sự cố hạ tầng cư dân
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tiếp nhận, phân loại mức độ khẩn cấp      │
│ và định tuyến ticket sự cố kỹ thuật hạ tầng (thang máy,     │
│ chập điện, tràn nước) từ App cư dân đến đội phản ứng nhanh. │
│ Công ty thành viên: [ ] Xanh SM  [ ] VinFast  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Cư dân (bức xúc vì chờ lâu), Ban Quản  │
│ lý toà nhà (quá tải khi sự cố dồn dập vào mùa mưa bão).    │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Đọc ticket cư dân gửi ──> 2. Đánh giá mức độ khẩn cấp  │
│   ──> 3. Chọn phòng ban phụ trách ──> 4. Gửi email/Zalo     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8 phút/ticket)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 (Đọc NLP,   │
│ gắn nhãn khẩn cấp, trích xuất số căn hộ, route tự động).    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Giảm thời gian định tuyến sự cố khẩn từ 30 phút ──> < 2 min│
│ - Độ chính xác phân loại bộ phận xử lý đạt >= 92%.           │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Thẻ bài toán 3: Card #3 — VinFast Tự động đối soát dữ liệu trạm sạc & thanh toán
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Tự động phát hiện và cảnh báo sai lệch    │
│ dữ liệu kWh giữa trụ sạc công cộng và log thanh toán ví.    │
│ Công ty thành viên: [ ] Xanh SM  [x] VinFast  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Chuyên viên Tài chính - Đối soát       │
│ trạm sạc VinFast, khách hàng bị trừ tiền oan.               │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Export file CSV trạm sạc ──> 2. Export log thanh toán  │
│   ──> 3. Viết hàm VLOOKUP đối chiếu ──> 4. Tạo ticket xử lý │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 120 phút/ngày)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4 (Phát hiện  │
│ bất thường bằng thuật toán anomaly detection).              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ - Thời gian đối soát giảm từ 2 ngày công/tuần ──> < 10 phút. │
│ - Phát hiện sai lệch giao dịch đạt 99.9%.                    │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **Card #1 — Xanh SM Xử lý sự cố pin nguy cấp & Cứu hộ thực địa** để thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các thẻ khác:
* **Loại Card #3 (VinFast Đối soát):** Nghiệp vụ mang tính chất xử lý dữ liệu bảng có cấu trúc và tính toán số học chính xác. Bài toán này phù hợp với mô hình **Rule-based + SQL script/Data pipeline** tự động, không cần đến sự phức tạp và chi phí của mô hình ngôn ngữ lớn (LLM).
* **Loại Card #2 (Vinhomes Sự cố hạ tầng):** Dù có giá trị vận hành cao, tuy nhiên rủi ro phân loại sai các sự cố nghiêm trọng (như báo cháy nhầm hoặc bỏ sót kẹt thang máy) đòi hỏi hệ thống cảm biến IoT tích hợp sâu trước khi triển khai AI.
* **Chọn Card #1 (Xanh SM Sự cố pin):**
  1. **Nỗi đau cấp bách & tần suất cao:** Diễn ra hằng ngày trên diện rộng (~85 ca/ngày tại Hà Nội & TP.HCM), ảnh hưởng trực tiếp đến tính an toàn giao thông, SLA phục vụ của Xanh SM và tuổi thọ pin xe VinFast.
  2. **Giá trị kinh tế lớn:** Rút ngắn thời gian chết máy, giảm tỉ lệ tài xế bỏ cuốc, tiết kiệm chi phí cứu hộ kéo xe đắt đỏ.
  3. **Phù hợp hoàn hảo với ranh giới an toàn AI:** LLM có thế mạnh lớn trong việc trích xuất ngôn ngữ tự nhiên từ tin nhắn tài xế và sinh chỉ dẫn lộ trình thân thiện, kết hợp hoàn hảo với quy tắc ranh giới cứng (Rule Guards) và mô hình **Human-in-the-Loop (HITL)**.
