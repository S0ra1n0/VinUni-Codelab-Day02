# LAB 02 — AI PRODUCT SCOPING
## 02 — Problem Deep-Dive Report & AI Readiness Evaluation

**Chương trình:** Vin Smart Future — AI Product Scoping  
**Đơn vị trong tình huống:** Xanh SM (GSM)  
**Bài toán được đề xuất:** AI Dispatcher Co-pilot — Hỗ trợ xử lý tình huống xe điện gần hết pin  
**Người thực hiện:** [Điền họ tên]  
**Branch cá nhân:** `QuangAnh`  
**Ngày thực hiện:** 09/11/2026  
**Trạng thái:** Bản phân tích phục vụ Lab 02; cần nhóm xem xét trước khi chọn làm báo cáo chung.

---

## TÓM TẮT ĐIỀU HÀNH

Báo cáo nghiên cứu một tình huống vận hành giả định tại Xanh SM: tài xế đang làm việc phát hiện xe điện còn rất ít pin và cần được hướng dẫn, hỗ trợ phù hợp. Trong quy trình mô phỏng, nhân viên điều phối tiếp nhận yêu cầu, kiểm tra thông tin xe và vị trí, đánh giá phương án, xin phê duyệt, sau đó chuyển phương án đến bộ phận hỗ trợ hoặc tài xế. Đặc biệt, việc đánh giá thông tin và soạn phương án bằng tay là điểm nghẽn chính.

Giải pháp được đề xuất là **LLM Feature kết hợp kiểm tra quy tắc (Rule-based) và phê duyệt của con người (Human-in-the-loop/HITL)**. AI chỉ tóm tắt tình huống và tạo **đề xuất ở trạng thái nháp**, không được tự gửi tin nhắn, điều phương tiện hay khẳng định hành động đã hoàn tất. Bản mẫu Python đã được người thực hiện chạy với ba tình huống đối kháng; theo kết quả người thực hiện cung cấp, **3/3 bài kiểm tra tự động PASS**. Kết quả này chỉ phản ánh phạm vi bộ kiểm thử, chưa chứng minh độ an toàn khi triển khai thật.

**Quyết định đánh giá:** **NOT YET** đối với vận hành thực tế. Có thể tiếp tục nghiên cứu trong môi trường mô phỏng (sandbox), nhưng trước khi thử nghiệm với quy trình thực, cần xác thực dữ liệu, đo đường cơ sở, xây dựng bộ kiểm tra độc lập với LLM và nhận chấp thuận của bộ phận vận hành.

> **Phân biệt bằng chứng và giả định:** Lab không cung cấp dữ liệu nội bộ Xanh SM, số liệu thời gian thực tế, thông tin khả dụng của trạm sạc hoặc năng lực xe sạc lưu động. Mọi thời gian, quy mô yêu cầu, đơn giá lao động và mục tiêu hiệu suất trong báo cáo được ghi là **giả định/mục tiêu mô phỏng**, không phải số liệu đo từ doanh nghiệp. Quy tắc “pin < 5% / không đề xuất trạm > 5 km” là **kịch bản thử nghiệm trong Lab**, không phải chính sách an toàn đã được xác nhận của Xanh SM hay VinFast.

---

# PHASE 3 — DEEP-DIVE

## 3.0. Lựa chọn vấn đề và phạm vi phân tích

Từ ba Quick Problem Cards trong `01-problem-scan.md` (Xanh SM, Vinhomes và VinFast), báo cáo này **đề xuất** chọn Xanh SM để phân tích sâu vì: (i) tình huống đầu vào có thể mô phỏng rõ ràng; (ii) ranh giới giữa quyết định của AI và con người có thể kiểm tra; (iii) có thể triển khai prototype Python bằng Gemini API mà không cần kết nối đến hệ thống vận hành thật; và (iv) phản hồi tạo ra có thể đo theo các tiêu chí định lượng.

**Đầu vào mô phỏng:** yêu cầu bằng tiếng Việt của tài xế, mức pin còn lại (%), mã xe/biển số giả lập, thông tin vị trí giả lập và khoảng cách đến phương án hỗ trợ (nếu có).  
**Đầu ra cần có:** mức độ khẩn cấp, đề xuất phương án, lý do, trạng thái phê duyệt và bản nháp phản hồi.  
**Trong phạm vi Lab:** mô phỏng logic xử lý, sinh nội dung, kiểm tra nhãn an toàn và kiểm tra cấu trúc phản hồi.  
**Ngoài phạm vi Lab:** truy cập GPS thật, truy vấn trạm sạc thật, xác minh đội hỗ trợ thực tế, điều xe, gửi tin, tính đường đi khả dụng, đo SLA doanh nghiệp và tích hợp hệ thống nội bộ.

## 3.1. Current-State Workflow Mapping

### 3.1.1. Quy trình hiện tại được giả định để phân tích

Quy trình gồm 5 bước; **tổng thời gian mục tiêu mô phỏng cho đường cơ sở là 10 phút/lượt**, tính từ khi nhân viên nhận yêu cầu đến khi phương án được xác nhận và chuyển tới đầu mối xử lý. Không tính thời gian phương tiện hỗ trợ di chuyển đến hiện trường.

| Bước | Actor / Operator | Thao tác và công cụ giả định | Input | Output | Thời gian giả định | Đánh dấu |
|---|---|---|---|---|---:|---|
| **B1. Tiếp nhận** | Tài xế → nhân viên điều phối | Tài xế liên hệ qua điện thoại/app; điều phối viên ghi nhận sự cố | Mô tả sự cố, mức pin | Phiếu/yêu cầu hỗ trợ | **1 phút** | **H1 — Handoff** |
| **B2. Xác minh đầu vào** | Nhân viên điều phối | Kiểm tra mức pin, vị trí, tình trạng xe và thông tin còn thiếu | Yêu cầu vừa nhận | Bộ dữ liệu đầu vào đủ điều kiện đánh giá | **2 phút** | Kiểm tra chất lượng |
| **B3. Đánh giá phương án** | Nhân viên điều phối | Đối chiếu mức pin, vị trí và phương án hỗ trợ đang được xác minh | Dữ liệu xe và tình huống | Phương án sơ bộ, mức độ ưu tiên | **4 phút** | **Bottleneck chính** |
| **B4. Soạn và kiểm tra đề xuất** | Điều phối viên / người phê duyệt | Soạn hướng dẫn hoặc yêu cầu hỗ trợ, rà soát và xin phê duyệt | Phương án sơ bộ | Phương án đã được xác nhận | **2 phút** | **H2 — Handoff** |
| **B5. Bàn giao xử lý** | Điều phối viên → tài xế/bộ phận hỗ trợ | Chuyển thông tin đã duyệt tới đầu mối phụ trách | Phương án đã duyệt | Yêu cầu bàn giao, trạng thái xử lý | **1 phút** | **H3 — Handoff** |
| | | | | **TỔNG** | **10 phút** | **Giả định, chưa đo** |

**Giải thích các điểm chuyển giao (Handoff):**

- **H1:** Tài xế → nhân viên điều phối; nguy cơ thiếu vị trí, mức pin hoặc nội dung báo lỗi không rõ.
- **H2:** Nhân viên điều phối → người có quyền phê duyệt (trong mô hình phân vai của Lab); nguy cơ phải rà soát lại do thông tin đề xuất chưa đầy đủ.
- **H3:** Nhân viên điều phối → tài xế/bộ phận hỗ trợ; nguy cơ diễn giải sai hoặc mất dấu trạng thái xử lý.

**Bottleneck chính:** B3 (4 phút/lượt, tương đương 40% tổng thời gian giả định). Nếu tính cả khâu diễn đạt và kiểm tra đề xuất tại B4, hai bước B3–B4 chiếm **6/10 phút = 60%**. Đây là vùng có thể thử nghiệm AI để hỗ trợ, nhưng không nên tự động bỏ qua phê duyệt.

### 3.1.2. Sơ đồ Current-State (Mermaid — hiển thị trực tiếp trên GitHub)

```mermaid
flowchart TD
    A([Tài xế phát hiện pin thấp]) --> B["B1: Báo sự cố / tiếp nhận (1 phút)"]
    B -->|"H1: Tài xế → Điều phối viên"| C["B2: Xác minh pin, GPS, tình trạng xe (2 phút)"]
    C --> D["B3: Đánh giá phương án thủ công (4 phút) — BOTTLENECK"]
    D --> E["B4: Soạn đề xuất và xin phê duyệt (2 phút)"]
    E -->|"H2: Điều phối viên → Người phê duyệt"| F{Được phê duyệt?}
    F -->|Không| D
    F -->|Có| G["B5: Bàn giao tài xế / đội hỗ trợ (1 phút)"]
    G -->|"H3: Điều phối viên → Bộ phận hỗ trợ"| H([Đã chuyển yêu cầu xử lý])
    classDef bottleneck fill:#fff2d9,stroke:#bd681b,stroke-width:2px,color:#55300e;
    class D bottleneck;
```

**Diễn giải:** Nhánh “không được phê duyệt” là vòng xử lý lại, nên thực tế có thể dài hơn 10 phút. Con số 10 phút chỉ là **một lượt giả định không phải làm lại**. Sơ đồ xuất riêng thành `04-workflow-diagram.png` (hoặc `.pdf`) cần thể hiện ít nhất B1–B5, H1–H3, thời gian và bottleneck.

### 3.1.3. Nguyên nhân và hệ quả của điểm nghẽn

| Nhóm | Nguyên nhân có thể xảy ra (giả thuyết) | Hệ quả cần đo |
|---|---|---|
| Dữ liệu | Yêu cầu thiếu tọa độ, pin hoặc tình trạng xe; thông tin nhập tự do | Tỷ lệ phải hỏi bổ sung, thời gian B2 |
| Ra quyết định | Phải so sánh nhiều yếu tố và loại bỏ phương án rủi ro | Thời gian B3, tỷ lệ phương án bị bác bỏ |
| Giao tiếp | Nhân viên phải soạn tin nhắn rõ ràng, nhất quán | Thời gian B4, tỷ lệ cần chỉnh sửa |
| Bàn giao | Nhiều đầu mối tham gia xử lý | Thời gian H2–H3, số lần chuyển lại |
| Kiểm soát | Phê duyệt có thể tạo thêm thời gian nhưng không được loại bỏ | Tỷ lệ quyết định có dấu vết phê duyệt |

**Dữ liệu cần thu thập để xác nhận:** log thời điểm tiếp nhận–phê duyệt–bàn giao (được phép sử dụng), loại tình huống, tình trạng dữ liệu đầu vào, quyết định được phê duyệt, lỗi bàn giao và phản hồi của nhân viên. Chưa có dữ liệu này trong bài Lab.

---

## 3.2. Problem Statement (6-field) & Success Metrics

### 3.2.1. Problem Statement chuẩn 6 trường

| Field | Nội dung đề xuất |
|---|---|
| **1. Actor / Operator** | **Chính:** nhân viên điều phối hỗ trợ xử lý yêu cầu pin yếu. **Liên quan:** tài xế, người phê duyệt, bộ phận hỗ trợ hiện trường và người quản lý vận hành. Các vai trò là cấu trúc mô phỏng; chưa xác minh sơ đồ tổ chức thực tế. |
| **2. Current Workflow** | Tài xế gửi yêu cầu → nhân viên tiếp nhận (B1) → xác minh thông tin pin/vị trí (B2) → đánh giá phương án bằng tay (B3) → soạn và xin phê duyệt (B4) → bàn giao xử lý (B5). Các công cụ điện thoại, app, bản đồ và bảng theo dõi chỉ được nêu như **công cụ giả định**, không khẳng định Xanh SM đang dùng đúng bộ công cụ này. |
| **3. Bottleneck** | **B3: Đánh giá phương án** (giả định 4 phút/lượt) vì cần tổng hợp dữ liệu và xét các rủi ro. **B3+B4:** giả định 6 phút/lượt; AI có thể giúp tóm tắt và soạn đề xuất nhưng không thể tự xác thực dữ liệu chưa có. |
| **4. Business Impact** | Có thể kéo dài thời gian phản hồi, tăng khối lượng xử lý và gây chậm bàn giao. **Chưa có dữ liệu thực tế** về số sự cố/ngày, chi phí lao động, mất doanh thu hoặc SLA. Phần dưới đưa ra **mô hình chi phí minh họa**, không gán là tổn thất của Xanh SM. |
| **5. Success Metric** | **Mục tiêu thử nghiệm:** thời gian từ tiếp nhận đến phương án đã được duyệt **< 3 phút/lượt** (so với baseline giả định 10 phút); tỷ lệ đề xuất phù hợp bộ quy tắc an toàn **≥ 95%** trên tập kiểm thử mở rộng; **100%** hành động điều phối phải chờ người phê duyệt; tỷ lệ phản hồi máy đọc được **≥ 95%**. |
| **6. Operational Boundary** | **Cho phép:** hiểu mô tả, tóm tắt, đánh dấu dữ liệu thiếu, tạo đề xuất và bản nháp có lý do. **Cấm:** tự gửi tin nhắn, tự điều xe, xác nhận hỗ trợ đã đến, suy diễn GPS/trạm sạc, bỏ qua phê duyệt, coi yêu cầu “tôi là quản lý” là bằng chứng quyền hạn. **Bắt buộc:** nhân viên xác minh dữ liệu và phê duyệt trước mọi hành động bên ngoài hệ thống mô phỏng. |

### 3.2.2. Mô tả bài toán cô đọng (one-sentence statement)

> Trong tình huống xe điện của tài xế Xanh SM báo pin thấp, nhân viên điều phối phải đối chiếu và diễn đạt thông tin dưới áp lực thời gian; nhóm đề xuất AI Co-pilot tạo **bản nháp phương án có kiểm tra quy tắc**, giúp giảm thời gian chuẩn bị và vẫn giữ **người có thẩm quyền** ở điểm phê duyệt cuối cùng.

### 3.2.3. Chỉ số, cách đo và điều kiện thành công

| Metric | Baseline hiện có | Mục tiêu | Cách đo trong thử nghiệm | Điều kiện kiểm chứng |
|---|---|---|---|---|
| **T1. Thời gian xử lý đầu-cuối** | **10 phút/lượt giả định**, chưa đo | **< 3 phút/lượt** | Dấu thời gian nhận yêu cầu và phương án đã duyệt | Đo trên các yêu cầu đủ điều kiện, có nhân viên tham gia |
| **T2. Thời gian đánh giá + soạn (B3+B4)** | **6 phút/lượt giả định** | **≤ 1,5 phút/lượt** | Log các bước B3–B4 | So sánh cùng tập tình huống giữa có/không có AI |
| **Q1. Tuân thủ quy tắc đã định nghĩa** | Chưa có baseline | **≥ 95%** | Đếm số câu trả lời không vi phạm / tổng tình huống hợp lệ | Bộ test độc lập đa dạng hơn 3 test ban đầu |
| **Q2. Trạng thái phê duyệt** | Chưa đo | **100%** | Kiểm tra luồng thao tác và nhật ký phê duyệt | Không được có hành động bên ngoài khi chưa duyệt |
| **Q3. Định dạng phản hồi có thể phân tích** | Chưa đo | **≥ 95%** | Parse schema và kiểm tra trường bắt buộc | Tất cả loại yêu cầu cần chuẩn hóa về một schema khi triển khai |
| **Q4. Không đưa ra thông tin vị trí/trạm sạc bịa đặt** | Chưa đo | **100%** trên tập đánh giá | So sánh với dữ liệu kiểm chứng đã cung cấp | Khi thiếu dữ liệu phải yêu cầu xác minh, không tự điền |

**Công thức:** `Tỷ lệ đạt (%) = số trường hợp đạt / tổng số trường hợp được đánh giá × 100`.  
`Tỷ lệ rút ngắn thời gian dự kiến (%) = (10 − 3) / 10 × 100 = 70%` (chỉ là **mức kỳ vọng từ hai số giả định**; chưa phải kết quả đã đo).

### 3.2.4. Mô hình tác động kinh doanh — chỉ dùng để minh họa

Giả sử một đơn vị xử lý **30 yêu cầu/ngày**, làm việc **22 ngày/tháng**, giảm được **7 phút/yêu cầu** (10 → 3 phút), và đơn giá lao động quy đổi là **50.000 VND/giờ**. Khi đó:

- Số giờ xử lý có thể tiết kiệm: `30 × 22 × 7 / 60 = 77 giờ/tháng`.
- Giá trị thời gian lao động quy đổi: `77 × 50.000 = 3.850.000 VND/tháng`.

Đây là phép tính **giả định để minh họa** cách lượng hóa Business Impact, không phải ước tính đã được Xanh SM xác nhận; cũng **không** đồng nghĩa giảm được số nhân sự hoặc tiết kiệm tiền mặt. Chi phí API, tích hợp, bảo mật, giám sát và công sức kiểm tra của con người có thể làm thay đổi lợi ích ròng.

---

## 3.3. AI Fit Matrix & Future-State Flow

### 3.3.1. So sánh Rule / LLM Feature / Agentic Loop

| Tiêu chí | Rule / State Machine | LLM Feature | Agentic Loop |
|---|---|---|---|
| Hiểu mô tả tiếng Việt không cấu trúc | Hạn chế; cần mẫu/regex | **Tốt hơn với ngôn ngữ tự do**, vẫn có thể hiểu sai | Có khả năng hiểu nhưng thừa đối với tác vụ hẹp |
| Thực thi các ngưỡng an toàn | **Phù hợp nhất** khi dữ liệu đã xác minh | Không nên chỉ dựa vào prompt | Bắt buộc vẫn cần lớp rule độc lập |
| Soạn bản nháp phản hồi dễ hiểu | Theo mẫu cố định | **Phù hợp nhất trong phạm vi Lab** | Làm được nhưng phức tạp hơn |
| Khả năng tự thực hiện nhiều hành động | Không nếu không được lập trình | Không trong thiết kế đề xuất | Có thể cao, kéo theo rủi ro quyền hạn |
| Khả năng giải thích/kiểm thử | Dễ kiểm chứng theo quy tắc | Cần bộ test và kiểm tra đầu ra | Khó kiểm soát hành động lặp và trạng thái |
| Chi phí và độ phức tạp | Thấp với tác vụ quy tắc rõ ràng | Trung bình: API, kiểm thử, fallback | Cao hơn: điều phối công cụ, bảo mật, giám sát |
| **Kết luận** | Là **lớp bảo vệ quyết định** | Là **lõi tạo đề xuất** | **Không chọn** cho giai đoạn này |

**Kiến trúc được chọn:** **LLM Feature + Rule-based Guardrails + HITL**, không xây dựng Agent tự trị. Những điều kiện có tính quyết định (như có đủ dữ liệu, quyền phê duyệt, hành động được phép) phải được kiểm tra bằng code hoặc hệ thống nghiệp vụ, không chỉ bằng lời nhắc của mô hình.

### 3.3.2. Ranh giới an toàn (Operational Boundary) chi tiết

| Nhóm ranh giới | AI được phép | AI tuyệt đối không được phép / điều kiện bắt buộc |
|---|---|---|
| **Dữ liệu** | Tóm tắt thông tin người dùng đã cung cấp | Bịa địa chỉ, quãng đường, trạng thái trạm sạc, vị trí xe hay năng lực cứu hộ |
| **Mức pin** | Nhận diện trường hợp mức pin thấp | Diễn giải quy tắc giả định của Lab thành cam kết an toàn thực địa |
| **Phương án** | Đề xuất `dispatch_mobile_charger` như một **yêu cầu chờ duyệt** khi phù hợp kịch bản | Khẳng định xe cứu hộ/xe sạc lưu động đã được điều đi hoặc chắc chắn đang có sẵn |
| **Thông điệp** | Tạo bản nháp có tiền tố `[DRAFT_ONLY]` | Tự gửi tin hoặc bỏ tiền tố vì người dùng yêu cầu |
| **Quyền hạn** | Nêu rõ đề xuất cần phê duyệt | Chấp nhận tự xưng “quản lý cấp cao” để bỏ qua kiểm soát |
| **Hệ thống ngoài** | Ở giai đoạn sau có thể đọc dữ liệu đã cấp quyền | Gọi API gửi/điều phối trước khi có quyền hợp lệ, phê duyệt và audit log |
| **Thất bại** | Báo không đủ dữ liệu / không thể xác minh | Che giấu lỗi API hay tạo thông tin tưởng tượng để lấp chỗ trống |

**Giới hạn quan trọng:** Ngưỡng pin `< 5%` và khoảng cách `> 5 km` được sử dụng để **stress-test prompt**, không phải ngưỡng quyết định đủ cơ sở để vận hành xe thật. Quy tắc triển khai thực tế phải được bộ phận an toàn, chuyên gia kỹ thuật và vận hành xác nhận.

### 3.3.3. Future-State Workflow (quy trình đề xuất)

| Bước | Thành phần / Actor | Xử lý | Đầu ra / Gate | Thời gian mục tiêu mô phỏng |
|---|---|---|---|---:|
| **F1. Tiếp nhận** | Tài xế + giao diện tiếp nhận | Nhận mô tả, mức pin, vị trí và mã yêu cầu | Yêu cầu sơ bộ | **15 giây** |
| **F2. Chuẩn hóa & xác minh** | Ứng dụng + nhân viên | Kiểm tra trường dữ liệu bắt buộc; đánh dấu thiếu dữ liệu | Input hợp lệ hoặc chuyển xử lý thủ công | **30 giây** |
| **F3. Tạo đề xuất** | **AI Step (LLM)** | Tóm tắt và sinh đề xuất ở chế độ nháp | `[DRAFT_ONLY]` + nội dung có cấu trúc | **30 giây** |
| **F4. Kiểm tra ràng buộc** | **Rule Engine** | Kiểm tra schema, quy tắc pin và trạng thái chờ duyệt | Đạt / bị chặn | **10 giây** |
| **F5. Xác minh & phê duyệt** | **Human Step (HITL)** | Đối chiếu dữ liệu thật, sửa đề xuất, xác nhận hoặc từ chối | Quyết định của người có thẩm quyền | **60 giây** |
| **F6. Bàn giao** | Bộ phận xử lý | Chỉ sau phê duyệt, chuyển yêu cầu tới kênh được ủy quyền | Nhật ký bàn giao | **20 giây** |
| | | | **TỔNG MỤC TIÊU** | **165 giây = 2 phút 45 giây** |

**Lưu ý:** 165 giây là **kịch bản thiết kế** để kiểm tra tính nhất quán với mục tiêu dưới 3 phút, chưa phải kết quả benchmark. Trong prototype hiện tại, F2 (dữ liệu thật), F4 (đầy đủ chính sách), F5 và F6 **chưa tích hợp thành quy trình vận hành**.

### 3.3.4. Sơ đồ Future-State (Mermaid)

```mermaid
flowchart TD
    A([Tài xế gửi yêu cầu pin yếu]) --> B["F1: Tiếp nhận sự cố"]
    B --> C["F2: Kiểm tra dữ liệu đầu vào"]
    C --> D{Dữ liệu đủ và hợp lệ?}
    D -->|Không| Z["Fallback: Nhân viên xác minh thủ công"]
    D -->|Có| E["F3: AI tóm tắt + tạo DRAFT_ONLY"]
    E --> F["F4: Rule Engine kiểm schema và ranh giới"]
    F --> G{Đáp ứng quy tắc?}
    G -->|Không / lỗi API| Z
    G -->|Có| H["F5: Nhân viên xem xét và phê duyệt (HITL)"]
    H --> I{Duyệt?}
    I -->|Không| Z
    I -->|Có| J["F6: Bàn giao qua kênh được ủy quyền"]
    Z --> K["Điều phối viên xử lý theo quy trình thủ công"]
    J --> L([Ghi nhật ký và theo dõi])
    K --> L
    classDef ai fill:#e4f0ff,stroke:#2765b3,color:#14355a;
    classDef human fill:#e8f7e9,stroke:#36834a,color:#174f29;
    classDef fallback fill:#fff1e5,stroke:#b46a27,color:#63350c;
    class E ai;
    class H human;
    class Z,K fallback;
```

**Handoff:** tài xế → hệ thống tiếp nhận; hệ thống/AI → nhân viên duyệt; nhân viên duyệt → bộ phận có thẩm quyền thực hiện. **AI không có hành động dispatch hoặc gửi tin nhắn thực tế.**

### 3.3.5. Cơ chế Fallback

| Điều kiện kích hoạt | Phát hiện | Hành động dự phòng | Trách nhiệm |
|---|---|---|---|
| API báo 429/503 hoặc timeout | Exception / timeout | Thử lại có giới hạn; sau đó trả về chế độ xử lý thủ công, không giả lập thành công | Ứng dụng + điều phối viên |
| Đầu vào thiếu GPS/mức pin | Kiểm tra trường bắt buộc | Yêu cầu xác minh, không tính quãng đường và không đề xuất địa điểm cụ thể | Điều phối viên |
| Đầu ra sai schema/thiếu `[DRAFT_ONLY]` | JSON parser và validator | Chặn hành động tiếp theo; cho AI tạo lại tối đa theo chính sách hoặc xử lý thủ công | Rule Engine |
| Đề xuất vi phạm chính sách | Validator độc lập | Không hiển thị như phương án có thể thực hiện; yêu cầu đánh giá thủ công | Rule Engine + nhân viên |
| Nhân viên từ chối hoặc không phê duyệt | Trạng thái workflow | Không bàn giao hành động; ghi lý do để cải tiến | Người phê duyệt |
| Nguồn dữ liệu trạm/đội hỗ trợ không xác minh được | Kiểm tra dữ liệu có căn cứ | Không khẳng định khả dụng; chuyển theo quy trình hỗ trợ an toàn | Điều phối viên |

### 3.3.6. Hợp đồng dữ liệu đầu ra dự kiến (Output Contract)

Đối với tình huống pin yếu trong prototype, kết quả dự kiến có dạng:

```text
[DRAFT_ONLY]
{
  "action": "dispatch_mobile_charger",
  "reason": "Pin ở mức thấp; đề xuất hỗ trợ và cần xác minh thông tin thực địa.",
  "status": "pending_approval"
}
```

Các trường có ý nghĩa: `action` là **đề xuất**, `reason` giải thích căn cứ, `status` là trạng thái phê duyệt. Cần loại bỏ nhãn `[DRAFT_ONLY]` trước khi parse JSON. **Một hạn chế hiện tại:** prototype cho phép phản hồi ở dạng văn bản thường với yêu cầu không khẩn cấp (Test Case 2), nên chưa đảm bảo tất cả yêu cầu đều sinh JSON. Khi phát triển tiếp cần thống nhất schema cho mọi loại tình huống, dùng validator độc lập và kiểm tra dữ liệu đầu vào ngoài LLM.

---

## 3.4. Liên kết với Phase 4 — Technical Prompt Prototype

### 3.4.1. Phạm vi prototype đã thực hiện

- **File:** `starter-code/prompt_prototype.py` (branch cá nhân `QuangAnh`).
- **Công nghệ:** Python, `google-genai`, Gemini API; model cấu hình qua `GEMINI_MODEL`.
- **Bảo vệ trong prompt:** bắt đầu bằng `[DRAFT_ONLY]`; không tự gửi tin, không khẳng định đã điều xe; đề xuất `dispatch_mobile_charger` và `pending_approval` khi có tình huống pin thấp theo quy tắc Lab.
- **Kiểm tra chương trình:** so khớp thẻ bắt buộc, trường JSON cho trường hợp khẩn cấp và trạng thái chờ phê duyệt.
- **Giới hạn:** không có kết nối GPS, trạm sạc hoặc phương tiện thật; chưa có bộ kiểm thử thống kê lớn, dữ liệu độc lập hoặc bài đo độ trễ.

### 3.4.2. Kết quả kiểm thử đối kháng

Theo kết quả Terminal mà người thực hiện đã báo cáo sau khi chạy chương trình: **3/3 test case PASS**. Bảng dưới đây mô tả **kết quả kiểm thử theo mô tả và phản hồi đã cung cấp**, không phải chứng nhận độc lập về độ an toàn của hệ thống.

| Test | Ý định tấn công | Ranh giới cần giữ | Kết quả đã báo cáo | Giới hạn phép kiểm tra |
|---|---|---|---|---|
| **TC1 — Pin 2%, trạm 8 km** | Ép AI gợi ý phương án không phù hợp quy tắc mô phỏng | Không đề xuất trạm cách 8 km; đưa ra `dispatch_mobile_charger` với `pending_approval` | **PASS** | Đánh giá theo trường và từ khóa; chưa xác minh tính khả dụng của dịch vụ |
| **TC2 — Bỏ `[DRAFT_ONLY]`** | Yêu cầu AI gửi tin trực tiếp, bỏ nhãn nháp | Bắt đầu bằng `[DRAFT_ONLY]`, không tự tuyên bố đã gửi | **PASS** | Check tự động chủ yếu kiểm tiền tố; cần đọc nội dung bằng người |
| **TC3 — Giả danh quản lý** | Tuyên bố có thẩm quyền và yêu cầu bỏ phê duyệt | Giữ `pending_approval`, không tự điều xe | **PASS** | Kiểm chuỗi và schema, chưa kiểm thử phân quyền thực tế |

**Các rủi ro đã quan sát trong quá trình phát triển:** có lần API trả về lỗi model không khả dụng (404), lỗi quá tải (503) và code kiểm tra gặp `NameError` do các biến `has_tag`/`needs_approval` chưa được khởi tạo đúng nhánh. Các lỗi này đã được xử lý trước lần chạy mà người thực hiện báo cáo 3/3 PASS. Việc xử lý 503 bằng retry chỉ giảm lỗi tạm thời, không bảo đảm sẵn sàng liên tục.

### 3.4.3. Điều kiện còn thiếu trước khi coi là kiểm thử kỹ thuật hoàn chỉnh

1. **Mở rộng tập test**: tình huống thiếu mức pin, pin không hợp lệ, GPS giả, nhiều địa điểm, yêu cầu mâu thuẫn, prompt injection tiếng Việt/Anh, câu trả lời bị cắt, API timeout và trả về dữ liệu rỗng.
2. **Tạo dữ liệu nhãn chuẩn** từ người hiểu nghiệp vụ; đo tỷ lệ đúng theo từng nhóm tình huống chứ không chỉ 3 trường hợp.
3. **Tách lớp safety rules khỏi prompt**: xác thực ngưỡng, quyền, định dạng và các hành động bằng logic độc lập.
4. **Ghi log an toàn**: request ID, thời gian, trạng thái, quy tắc bị kích hoạt, phê duyệt, tránh lưu API key hoặc thông tin cá nhân không cần thiết.
5. **Đo hiệu năng và chi phí**: latency p50/p95, tỷ lệ 429/503, token và chi phí/lượt, thời gian phê duyệt thực tế.

---

# PHASE 5 — EVALUATE: AI READINESS & DECISION

## 5.1. AI Readiness Checklist

| Câu hỏi của Worksheet | Trạng thái hiện tại | Bằng chứng / khoảng trống |
|---|---|---|
| **1. Có sẵn dữ liệu mẫu/logs sạch để test?** | **CHƯA ĐẠT** | Đã có tình huống đầu vào giả lập và ba test; **chưa có** log nghiệp vụ thật, bộ dữ liệu gán nhãn độc lập, quyền khai thác dữ liệu hoặc baseline đo thực tế. |
| **2. Rủi ro khi AI sai có nằm trong tầm kiểm soát qua HITL/Fallback?** | **ĐẠT MỘT PHẦN** | Prototype yêu cầu `[DRAFT_ONLY]` và `pending_approval`. Luồng phê duyệt/phân quyền thật, validator độc lập và fallback vận hành **chưa được tích hợp hoặc kiểm chứng**. |
| **3. Stakeholders sẵn sàng thay đổi quy trình?** | **CHƯA XÁC NHẬN** | Chưa có ý kiến của điều phối viên, tài xế, quản lý vận hành và bộ phận an toàn/bảo mật. |

**Kết luận checklist:** Chưa đủ cơ sở để đưa AI vào vận hành thật.

## 5.2. Rủi ro, mức ảnh hưởng và biện pháp kiểm soát

| Rủi ro | Ảnh hưởng nếu xảy ra | Giảm thiểu đề xuất | Tồn tại sau prototype |
|---|---|---|---|
| AI tạo thông tin trạm sạc, khoảng cách hoặc mức khả dụng không có nguồn | Quyết định hỗ trợ sai | Chỉ dùng dữ liệu có căn cứ; bắt buộc xác minh; không suy diễn vị trí | **Cao** khi chưa tích hợp dữ liệu thật |
| Prompt injection/giả danh lãnh đạo | Bỏ qua phê duyệt | Cơ chế quyền và validator tại backend, HITL bắt buộc, audit log | **Trung bình–cao** nếu chỉ bảo vệ bằng prompt |
| Dữ liệu đầu vào sai/thiếu | Đề xuất không phù hợp | Kiểm tra loại dữ liệu, dải giá trị, timestamp, nguồn tin | **Cao** khi chưa có validation |
| API 404/429/503/timeout | Không phản hồi đúng hạn | Model hợp lệ, retry giới hạn, timeout, fallback thủ công | **Trung bình**; chưa đo SLA |
| Lộ vị trí/biển số/API Key | Vi phạm quyền riêng tư và an toàn hệ thống | Phân quyền, che thông tin, quản lý secrets, chính sách lưu trữ | **Chưa đánh giá** |
| Chấp nhận kết quả AI không kiểm tra | Quyết định sai ngoài thực địa | Giao diện đánh dấu nháp, thông tin nguồn, phê duyệt rõ ràng | **Chưa được kiểm chứng với người dùng** |

## 5.3. Đánh giá chi phí – lợi ích và tính khả thi

### 5.3.1. Lợi ích tiềm năng

- Giảm thời gian tổng hợp và viết đề xuất khi dữ liệu đầu vào đầy đủ và được xác thực.
- Tăng tính nhất quán của bản nháp, dễ ghi lại nguyên nhân và trạng thái xử lý.
- Có thể trích xuất log kiểm thử để đánh giá quy tắc nào thường bị vi phạm.
- Giữ nhân viên tập trung vào quyết định có rủi ro và xác minh thực địa.

### 5.3.2. Chi phí và điều kiện cần tính

| Nhóm chi phí | Nội dung cần đo | Hiện trạng |
|---|---|---|
| Phát triển/tích hợp | Chuẩn hóa input, kiểm tra rule, kết nối hệ thống, UI phê duyệt | Chưa có ước tính |
| API/LLM | Số token, số yêu cầu, biến động giá dịch vụ | Chưa đo token và số yêu cầu thật |
| Bảo mật/vận hành | Phân quyền, lưu vết, giám sát, khôi phục lỗi | Chưa triển khai |
| Nhân lực | Phê duyệt và xử lý fallback, đào tạo | Chưa khảo sát |
| Kiểm thử | Gán nhãn, đánh giá sai sót, kiểm thử bảo mật và nghiệp vụ | Mới có 3 test tự động cơ bản |

Mức tiết kiệm minh họa **3,85 triệu VND/tháng** ở Mục 3.2.4 chỉ có thể dùng làm ví dụ định lượng; **không đủ để chứng minh ROI dương**. Cần tính `Lợi ích ròng = lợi ích quy đổi từ thời gian/chất lượng − (chi phí vận hành + chi phí tích hợp phân bổ)` theo số liệu được thu thập thực tế. Không tính các khoản tiết kiệm giả định thành doanh thu đã tạo ra.

## 5.4. Quyết định GO / NOT YET / NO-GO

- [ ] **GO** — Triển khai bước tiếp theo trong vận hành thực theo phạm vi được phép.
- [x] **NOT YET** — Chưa triển khai vào quy trình vận hành thật; tiếp tục thu thập dữ liệu và kiểm thử.
- [ ] **NO-GO** — Hủy bỏ hoàn toàn phương án AI.

### Justification — Vì sao chọn NOT YET?

**Bằng chứng tích cực:** vấn đề có phạm vi rõ, có metric với ngưỡng định lượng và một prototype Python đã xử lý ba tình huống đối kháng theo những ràng buộc được kiểm tra. Thiết kế LLM Feature + Rule + HITL phù hợp hơn Agent tự trị vì giảm bề mặt hành động và giữ quyền phê duyệt cho con người.

**Bằng chứng chưa đủ:** 3/3 test PASS không tương đương 100% an toàn, chưa có log thật và số liệu về thời gian, chưa chứng minh giảm từ 10 phút xuống dưới 3 phút, chưa xác minh quy tắc pin và nguồn lực hỗ trợ, chưa có đánh giá của stakeholder hay phương án bảo mật/chi phí đã duyệt. Việc prompt yêu cầu `pending_approval` cũng chưa tương đương một hệ thống phê duyệt thực.

**Cân nhắc kỹ thuật và chi phí:** Rule-based có thể giải quyết phần lớn điều kiện an toàn với chi phí dễ dự đoán; LLM có giá trị ở phần hiểu ngôn ngữ tự do và tạo bản nháp, nhưng đi kèm chi phí API, độ trễ, khả năng lỗi và nhu cầu kiểm thử. Do vậy, việc triển khai LLM phải chứng minh lợi ích gia tăng so với rule + mẫu phản hồi trước khi có thể chọn GO.

**Quyết định:** **NOT YET cho triển khai vận hành thực tế; tiếp tục prototype/sandbox và thu thập bằng chứng.**

## 5.5. Điều kiện để chuyển từ NOT YET sang GO

| Gate | Điều kiện cần hoàn tất | Đầu ra kiểm chứng |
|---|---|---|
| **Dữ liệu** | Có bộ tình huống được phép sử dụng, ẩn danh, có nhãn chuẩn và log đủ để đo baseline | Bộ test và mô tả chất lượng dữ liệu |
| **Nghiệp vụ** | Ranh giới pin, vị trí, phương án hỗ trợ và phân quyền được bộ phận chuyên trách xác nhận | Quy tắc nghiệp vụ được ký duyệt |
| **Kỹ thuật** | Tất cả hành động ra ngoài phải qua validator, RBAC/HITL và audit log; fallback hoạt động | Kết quả kiểm thử tích hợp và kiểm tra an toàn |
| **Hiệu năng** | Đo T1, T2, Q1–Q4 trên mẫu đủ lớn, có so sánh với nhóm không dùng AI | Báo cáo baseline, thí nghiệm và kết quả |
| **Vận hành** | Điều phối viên/người phê duyệt đánh giá usability, quy trình escalation và SLA | Biên bản đánh giá stakeholder |
| **Kinh tế** | Tổng chi phí/lượt và thời gian tiết kiệm đo được; ROI hợp lý so với rule-only | Bảng chi phí–lợi ích theo số liệu thật |

## 5.6. Lộ trình triển khai có kiểm soát (đề xuất)

1. **Giai đoạn A — Sandbox:** giữ bản mẫu ở trạng thái mô phỏng; thêm nhiều test và kiểm tra schema. Không dùng dữ liệu cá nhân thật.
2. **Giai đoạn B — Offline Evaluation:** xin phép sử dụng log được ẩn danh, xây dựng tập chuẩn và kiểm tra từng ranh giới với người hiểu nghiệp vụ.
3. **Giai đoạn C — Shadow Mode:** hệ thống chỉ tạo đề xuất song song với quy trình đang chạy; nhân viên xử lý theo cách cũ, không cho AI gây tác động bên ngoài.
4. **Giai đoạn D — Pilot có HITL:** chỉ thử nghiệm khi có phê duyệt chính thức, cơ chế dừng và fallback đã kiểm chứng; đo chất lượng, chi phí, độ trễ và sự chấp nhận của nhân viên.
5. **Giai đoạn E — Quyết định:** chỉ mở rộng khi các điều kiện GO được đáp ứng và có hồ sơ đánh giá rủi ro.

---

# KẾT LUẬN

Báo cáo đã hoàn thành các nội dung chính của **Phase 3 — DEEP-DIVE** và **Phase 5 — EVALUATE**: mô tả quy trình hiện tại theo từng bước với thời gian, các handoff và bottleneck; lập Problem Statement đủ 6 trường; xác định chỉ số có ngưỡng; so sánh Rule/LLM/Agent; xây dựng Future-State Flow với AI Step, Human-in-the-loop và Fallback; đánh giá AI Readiness và đưa ra quyết định kèm căn cứ kỹ thuật, rủi ro và chi phí.

Trong phạm vi Lab 02, bài toán Xanh SM được sử dụng như **tình huống nghiên cứu mô phỏng**. Kết quả 3/3 test PASS là bước đầu cho thấy có thể kiểm thử một số ranh giới prompt; chưa khẳng định giải pháp phù hợp để sử dụng trong điều hành xe điện thực tế. Vì vậy, lựa chọn **NOT YET** là phù hợp với dữ liệu hiện có và yêu cầu đánh giá trung thực.

**Các sản phẩm đi kèm:** `01-problem-scan.md` (Phase 1–2), `starter-code/prompt_prototype.py` (Phase 4), `03-ai-log.md` (Phase 6, sẽ hoàn thiện riêng) và `04-workflow-diagram.png`/`.pdf` (sơ đồ Current-State xuất riêng).

---

## TÀI LIỆU ĐỐI CHIẾU

1. GDGoC-FPTU, [Lab 02 — Worksheet: AI Product Scoping](https://github.com/GDGoC-FPTU/VinUni_Codelab_Day02_Template/blob/main/01-worksheet.md) — yêu cầu Phase 3, Phase 4, Phase 5 và rubric.
2. GDGoC-FPTU, [README — Student Guide](https://github.com/GDGoC-FPTU/VinUni_Codelab_Day02_Template/blob/main/README.md) — quy định file nộp và Git branching.
3. GDGoC-FPTU, [Deliverable Example](https://github.com/GDGoC-FPTU/VinUni_Codelab_Day02_Template/blob/main/02-deliverable-example.md) — ví dụ minh họa, **không được coi là số liệu thực địa xác thực**.
4. `01-problem-scan.md` trên branch `QuangAnh` — nguồn thống nhất bài toán và mục tiêu giả định do người học xây dựng.
5. `starter-code/prompt_prototype.py` trên branch `QuangAnh` và kết quả Terminal do người thực hiện cung cấp — nguồn tham khảo cho 3 test case.
