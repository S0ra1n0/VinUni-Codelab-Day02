# 01 — Problem Scan & Quick Assess (Vin Smart Future × Vinmec)

> **Người thực hiện:** ________ (AI Engineer, Vin Smart Future)
> **Mảng lựa chọn:** 🏥 **Vinmec** — chăm sóc và trải nghiệm bệnh nhân
>
> **Lưu ý về số liệu:** Vinmec không công bố số liệu vận hành nội bộ (thời gian chờ, tỷ lệ giao sai suất ăn, tỷ lệ bỏ hẹn...). Mọi con số trong file này là **giả thuyết cần đo** hoặc **số liệu tham khảo từ nghiên cứu ngoài Vinmec** (ghi nguồn ở file 02). Các ô `___` là baseline phải đo tại hiện trường.

---

# 🔍 Phase 1 — SCAN

Quét bằng 4 lenses: **Lặp lại**, **Tốn thời gian**, **AI có thể tốt hơn**, **Pain từ người khác**.

### 📝 List bài toán của tôi

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinmec** | Pain từ người khác | **Suất ăn nội trú:** bệnh nhân không biết món nào trong menu bệnh viện hợp với chế độ ăn được chỉ định; suất ăn có thể giao sai/trễ; bệnh nhân khoa Phục hồi chức năng (PHCN) bỏ bữa hoặc ăn nguội vì trùng giờ tập. |
| 2 | **Vinmec** | AI có thể tốt hơn | **Điều phối lịch hẹn:** bệnh nhân muốn chọn bác sĩ theo khung giờ làm việc của bác sĩ, nhưng phải lọc qua nhiều màn hình hoặc gọi tổng đài; lượt khám kéo theo xét nghiệm/CĐHA bị đặt rời rạc; ca trống do hủy sát giờ không được lấp. |
| 3 | **Vinmec** | Pain từ người khác | **Phục hồi sau phẫu thuật tại nhà:** bệnh nhân và người nhà không rõ ăn gì, vận động ra sao, dấu hiệu nào cần quay lại viện; dễ quên lịch tái khám. |
| 4 | **Vinmec** | Tốn thời gian | **Ghi chép lâm sàng:** bác sĩ ngoại trú vừa khám vừa gõ bệnh sử, diễn tiến vào EMR; phần hoàn tất hồ sơ dồn ra ngoài giờ. |
| 5 | **Vinmec** | Lặp lại | **Tiếp đón & định danh:** nhân viên quầy đọc CCCD/thẻ bảo hiểm, tìm hồ sơ cũ, kiểm tra quyền lợi bảo hiểm thủ công; dễ tạo hồ sơ trùng. |
| 6 | **Vinmec** | Tốn thời gian | **Bảo lãnh viện phí:** đối chiếu thủ công chỉ định, chẩn đoán, chứng từ với điều khoản từng hãng bảo hiểm; hồ sơ bị trả làm bệnh nhân chờ lúc xuất viện. |
| 7 | **Vinmec** | AI có thể tốt hơn | **Hỗ trợ bác sĩ CĐHA ca khó:** bác sĩ phải tự tìm và mở phim cũ để so sánh, đắn đo với ca mơ hồ. |

---

# 🃏 Phase 2 — QUICK-ASSESS

Top 3 được chọn: **#1 (Suất ăn nội trú PHCN)**, **#2 (Điều phối lịch hẹn)**, **#3 (Phục hồi sau phẫu thuật)**.

Lý do loại các bài toán còn lại ở vòng này:
- **#4 Ghi chép lâm sàng:** Vinmec đã có công cụ tự động tóm tắt hồ sơ bệnh án và biên bản hội chẩn từ đầu 2025, nguy cơ trùng; thời gian tiết kiệm của bác sĩ khó quy ra tiền.
- **#5 Tiếp đón:** chủ yếu là bài toán tích hợp (đọc chip CCCD, API tra thẻ), phần AI rất mỏng.
- **#6 Bảo lãnh viện phí:** cần quyền truy cập hợp đồng của từng hãng bảo hiểm, phạm vi lớn.
- **#7 CĐHA:** rủi ro lâm sàng và pháp lý cao (có thể thuộc diện thiết bị y tế), cần mô hình thị giác chuyên dụng.

---

## Card #1 — Suất ăn thông minh cho bệnh nhân nội trú (pilot khoa PHCN)

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Bệnh nhân nội trú khoa PHCN tự chọn món   │
│ trong menu bệnh viện qua hệ thống đề xuất dựa trên chỉ định │
│ ăn và hồ sơ; món không phù hợp bị chặn/cảnh báo; bữa ăn     │
│ được điều phối theo lịch tập và đối chiếu tại giường.       │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bệnh nhân nội trú PHCN và người nhà    │
│ (ít lựa chọn, không biết món nào được ăn, bỏ bữa vì đang    │
│ tập). Phụ: điều dưỡng và bếp (chuyển chỉ định, đổi/hủy suất │
│ bằng tay).                                                  │
│                                                             │
│ Workflow thủ công hiện tại (giả thuyết, cần xác nhận):      │
│   1. BS chỉ định chế độ ăn trong y lệnh                     │
│   ──> 2. ĐD tổng hợp, chuyển cho bếp (phiếu/điện thoại)     │
│   ──> 3. BN chọn món từ thực đơn giấy hoặc hỏi ĐD           │
│   ──> 4. Bếp chia khay, giao theo giờ cố định               │
│   ──> 5. Đổi y lệnh/lịch tập → báo lại bếp bằng tay;        │
│         không ai ghi nhận BN ăn được bao nhiêu              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2, 3 và 5             │
│   (⏱ ước tính ~15 phút nhân sự/bệnh nhân/ngày, cần đo)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3: đề xuất món   │
│   trong danh sách đã được LUẬT lọc, giải thích dễ hiểu;     │
│   bước 2 và 5: tự động hóa bằng luật/tích hợp HIS.          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Món "đỏ" (vi phạm chỉ định/dị ứng) đặt thành công = 0   │
│   - % suất thừa giảm ≥ 20% so với baseline                  │
│   - % bữa bị bỏ/ăn nguội do lịch tập giảm ≥ 50%             │
│   - Phút ĐD cho suất ăn/BN/ngày giảm từ ~7 → ≤ 2            │
│   (ngưỡng là giả thuyết, chốt sau 2 tuần đo baseline)       │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│   Rule = lõi an toàn; LLM chỉ diễn đạt và trò chuyện.       │
└─────────────────────────────────────────────────────────────┘
```

**Stress-test (vai CFO/Trưởng phòng Vận hành):**
- Phần lõi (lọc món theo chống chỉ định, định tuyến đơn) là **luật**, không cần AI. LLM chỉ là lớp hội thoại.
- Menu sau khi lọc có thể chỉ còn vài món cho một số chế độ ăn, lúc đó "đề xuất" mất ý nghĩa.
- Nhóm đột quỵ có nuốt khó là rủi ro cao nhất, phải loại khỏi pilot đầu.

---

## Card #2 — Trợ lý điều phối lịch hẹn với bác sĩ

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Bệnh nhân chọn bác sĩ và giờ khám bằng    │
│ hội thoại; hệ thống khớp với lịch làm việc thật của bác sĩ, │
│ xếp chuỗi dịch vụ liên tiếp và lấp ca trống khi có hủy.     │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bệnh nhân (khó tìm giờ phù hợp, đi     │
│ nhiều lần vì lịch rời rạc). Phụ: nhân viên tổng đài.        │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. BN tìm bác sĩ trên MyVinmec/web hoặc gọi tổng đài      │
│   ──> 2. Thử từng khung giờ còn trống                       │
│   ──> 3. Khám xong, đặt riêng lịch xét nghiệm/CĐHA          │
│   ──> 4. Muốn đổi giờ → gọi tổng đài hoặc hủy đặt lại       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 và 4                │
│   (⏱ ___ phút/lượt đặt; ___ cuộc gọi tổng đài/tháng)        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-2: hiểu yêu    │
│   cầu nhiều ràng buộc ("sáng thứ 7, bác sĩ nữ"), gọi API    │
│   lịch, đề xuất 2-3 phương án; bước 4: đổi lịch bằng 1 tin. │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - Cuộc gọi tổng đài về đặt/đổi lịch giảm ≥ 30%            │
│   - % ca trống do hủy được lấp lại tăng từ ___ lên ___      │
│   - Đặt nhầm bác sĩ/cơ sở/giờ = 0 trong mẫu kiểm            │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [x] Agent │
│   Agent có giới hạn: chỉ gọi API xem/đặt/đổi lịch, mọi thao │
│   tác ghi đều cần bệnh nhân xác nhận.                       │
└─────────────────────────────────────────────────────────────┘
```

**Stress-test:**
- MyVinmec đã cho đặt lịch, quản lý lịch cho cả gia đình. Phải chỉ ra khoảng trống cụ thể (yêu cầu nhiều ràng buộc, chuỗi dịch vụ), nếu không thì chỉ cần thêm bộ lọc vào app.
- Phụ thuộc lịch bác sĩ chính xác theo thời gian thực qua API, chưa xác nhận có hay không.
- Nếu lịch bác sĩ đã kín, đặt nhanh hơn không tạo thêm doanh thu.

---

## Card #3 — Đồng hành phục hồi sau phẫu thuật tại nhà

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Kế hoạch ăn uống, vận động, thuốc và lịch │
│ tái khám theo phác đồ bác sĩ duyệt, kèm check-in hằng ngày  │
│ và cảnh báo sớm biến chứng sau xuất viện.                   │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bệnh nhân sau xuất viện và người nhà.  │
│ Phụ: điều dưỡng (gọi hỏi thăm, trả lời câu hỏi lặp lại).    │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Xuất viện, nhận lời dặn chung chung                    │
│   ──> 2. Ở nhà tự tra mạng về ăn kiêng, vận động            │
│   ──> 3. Có dấu hiệu lạ: tự quyết đi viện hay gọi tổng đài  │
│   ──> 4. Tái khám theo lịch hẹn (hoặc quên)                 │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 và 3                │
│   (⏱ ___ cuộc gọi hỏi sau xuất viện/ca; ___ % tái khám đúng │
│   hạn)                                                      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: ghép kế hoạch │
│   ngày trong khung phác đồ đã duyệt; bước 3: check-in, LUẬT │
│   phân loại đỏ/vàng/xanh; bước 4: nhắc và đặt lịch tái khám.│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   - % bệnh nhân check-in ≥ 5/7 ngày                         │
│   - % tái khám đúng hạn tăng từ ___ lên ___                 │
│   - 100% ca dấu hiệu đỏ được chuyển hướng cấp cứu/ĐD        │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

**Stress-test:**
- Cần phác đồ phục hồi chuẩn hóa cho từng loại phẫu thuật trước, việc soạn và duyệt phác đồ tốn thời gian bác sĩ hơn phần phần mềm.
- Luật cảnh báo quá nhạy sẽ làm điều dưỡng quá tải.
- Bằng chứng về giảm tái nhập viện không đồng đều, không nên hứa con số.

---

# 🗳️ Lựa chọn cho Deep-Dive

Chọn **Card #1 — Suất ăn thông minh cho bệnh nhân nội trú (pilot khoa PHCN)** vì:
- Toàn bộ quy trình nằm **trong bệnh viện**, dữ liệu (y lệnh, dị ứng, menu) thuộc quyền kiểm soát của Vinmec.
- Có **ranh giới an toàn rõ ràng** để thiết kế và kiểm thử (món đỏ = 0), phù hợp với phần prompt prototype.
- Baseline đo được trong 2 tuần ở một khoa (suất thừa, phút điều dưỡng, bữa bị bỏ).
- Là bước nền cho Card #3: dữ liệu chế độ ăn và lượng ăn trong viện có thể nối sang kế hoạch phục hồi sau xuất viện.
