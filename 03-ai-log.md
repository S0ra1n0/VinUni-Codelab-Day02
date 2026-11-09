# 03 — AI Log & Reflection

> **Công cụ AI sử dụng:** Claude (chat, có tìm kiếm web) làm thought-partner; Gemini 2.5 Flash làm mô hình chạy trong prompt prototype.
> **Người viết:** ________
>
> *Ghi chú: bản nháp này được dựng lại từ đúng nội dung cuộc trò chuyện. Hãy đọc lại và sửa bằng giọng của bạn, đặc biệt mục 5 (sau khi chạy Gemini thật).*

---

## 1. Hành trình: từ ý tưởng rộng đến bài toán cụ thể

| Vòng | Tôi hỏi / đề xuất | AI phản hồi | Tôi rút ra |
|---|---|---|---|
| 1 | Dùng prompt mẫu: 5 quy trình thủ công ở Vinmec kèm số liệu tổn thất | 5 quy trình (ghi chép lâm sàng, bảo lãnh viện phí, bỏ hẹn, điều phối giường, ghi nhận vật tư), có tra cứu nguồn | Có điểm khởi đầu, nhưng nhiều con số là giả định |
| 2 | Thẻ "tối ưu ghi chép lâm sàng" + yêu cầu đóng vai CFO khắt khe | 3 điểm yếu: phạm vi quá rộng, metric không có baseline, phần lớn làm được bằng mẫu và luật | Thẻ đầu tiên của tôi quá chung chung |
| 3 | Hỗ trợ bác sĩ đọc ảnh, tra cứu hồ sơ/CCCD/BHYT | Thẻ #02, #03 | Tra cứu hồ sơ chủ yếu là bài toán tích hợp, gần như không cần AI |
| 4 | Bệnh nhân lên lịch, AI điều phối bác sĩ | Thẻ điều phối lịch; tra cứu tính năng MyVinmec hiện có | Tránh xây trùng tính năng đã có |
| 5 | Phục hồi sau mổ → thực đơn → menu bệnh viện → nội trú → khoa PHCN | Thẻ suất ăn, bằng chứng về sự cố suất ăn, lãng phí, suy dinh dưỡng | Chốt bài toán deep-dive |

## 2. AI đã giúp được gì

- **Mở rộng không gian bài toán nhanh.** Trong vài phút có danh sách các quy trình ở Vinmec mà tôi chưa nghĩ tới (bảo lãnh viện phí, ghi nhận vật tư, điều phối giường).
- **Phản biện thẳng thắn khi được yêu cầu đóng vai CFO.** Câu quan trọng nhất: *"nếu không chứng minh được phần nào không thể giải bằng mẫu và luật thì không duyệt ngân sách LLM"*. Nhờ đó kiến trúc cuối cùng của tôi là **luật làm lõi an toàn, LLM chỉ làm phần ngôn ngữ**.
- **Kiểm tra thực tế trước khi đề xuất.** Khi tôi định làm trợ lý đặt lịch, AI tra cứu và cho thấy MyVinmec đã có đặt lịch, quản lý lịch gia đình, telehealth, nhắc lịch. Nếu không kiểm, tôi đã đề xuất một tính năng trùng.
- **Tìm bằng chứng có nguồn** cho bài toán suất ăn: báo cáo 285 sự cố chế độ ăn ở Pennsylvania (181 ca có chất gây dị ứng), nghiên cứu lãng phí suất ăn, tỷ lệ suy dinh dưỡng nội trú ở Việt Nam, và cả một nghiên cứu **phản bác** (dinh dưỡng không làm thay đổi kết cục phục hồi chức năng ở bệnh nhân đột quỵ trong mẫu đó).
- **Nhắc rủi ro tôi bỏ sót:** nhóm bệnh nhân nuốt khó ở khoa PHCN, lộ bệnh nền trên màn hình ở giường, bệnh nhân vắng mặt vì đi tập, dữ liệu sức khỏe theo Nghị định 13/2023.

## 3. AI sai / hallucination / không đáng tin ở đâu

| # | Vấn đề | Loại | Tôi phát hiện / xử lý thế nào |
|---|---|---|---|
| 1 | Câu trả lời đầu có các con số như "1–3% doanh thu bảo hiểm bị từ chối", "0,5–1% thất thoát vật tư", "15% giường xuất viện mỗi ngày, trễ 2 giờ" | **Số liệu giả định trình bày gần như dữ kiện** | AI có ghi "giả định" nhưng dễ bị đọc lướt thành số thật. Trong báo cáo tôi gắn nhãn **[GĐ]** cho mọi số tự đặt và **[NC]** cho số có nguồn. |
| 2 | Quy mô Vinmec không nhất quán: một nguồn 9 bệnh viện + 7 phòng khám, 2.000 giường; nguồn khác 17 cơ sở, 1.505 giường; "5,7 triệu lượt ngoại trú" không rõ kỳ tính | **Nguồn mâu thuẫn** | Chỉ dùng số trên trang chính thức của Vinmec và không dùng số lượt khám để tính toán. |
| 3 | Nhắc đến sản phẩm AI X-quang của VinBrain và việc CCCD gắn chip thay thẻ BHYT | **Kiến thức từ trí nhớ, chưa kiểm chứng** | AI tự nói là chưa kiểm chứng; tôi không đưa vào báo cáo. |
| 4 | Hiểu sai ý tôi ở bài toán đọc ảnh: AI cho rằng người đau có thể là kỹ thuật viên | **Hiểu sai actor** | Tôi phải sửa: "người đau ở đây là bác sĩ". |
| 5 | Tôi nói "AI giao tiếp với bệnh nhân để giảm thời gian trao đổi trực tiếp", AI hiểu thành trợ lý hỏi bệnh sử trước khám | **Hiểu sai ý định** | Tôi giải thích lại: ý là điều phối lịch bác sĩ. Bài học: mô tả ý tưởng bằng ví dụ cụ thể ngay từ đầu. |
| 6 | Số liệu MyVinmec (lượt cài, người dùng hoạt động) lấy từ trang thống kê bên thứ ba | **Nguồn độ tin cậy thấp** | Không dùng làm căn cứ. |
| 7 | Bằng chứng chủ yếu từ nước ngoài (Mỹ, Lebanon); một nghiên cứu về tiết kiệm chi phí dinh dưỡng do hãng dinh dưỡng tài trợ | **Thiên lệch nguồn / khác bối cảnh** | Chỉ dùng để dựng giả thuyết, không suy thẳng cho Vinmec; không hứa giảm ngày nằm viện. |
| 8 | Lỗi chính tả "khóa cấp độ kết cục" thay vì "kết cấu" trong một bản thẻ | **Lỗi diễn đạt** | Sửa trong bản nộp. |
| 9 | Môi trường của AI không gọi được Gemini, nên phần chạy thử chỉ là `--dry-run` với **phản hồi giả lập** | **Rủi ro trình bày kết quả giả như kết quả thật** | Báo cáo ghi rõ dry-run không phải kết quả Gemini; bảng kết quả Gemini để trống chờ tôi chạy. |

## 4. Tôi đã sửa prompt và ranh giới như thế nào

**Sửa cách hỏi AI:**
- Ép phản biện: *"đóng vai CFO và Trưởng phòng Vận hành cực kỳ khắt khe"* cho câu trả lời hữu ích hơn nhiều so với hỏi "ý tưởng này có tốt không".
- Thu hẹp dần thay vì hỏi rộng: Vinmec → chăm sóc bệnh nhân → suất ăn → nội trú → khoa PHCN.
- Hỏi kiểm chứng trước khi xây: *"MyVinmec đã hỗ trợ bệnh nhân làm những gì?"*
- Nói rõ actor khi AI hiểu sai.

**Sửa ranh giới của hệ thống (system prompt + code):**
- Phiên bản ý tưởng đầu: LLM "đề xuất món phù hợp với hồ sơ bệnh án". Rủi ro: LLM tự đánh giá món nào an toàn.
- Phiên bản cuối: **luật lọc món trước**, LLM chỉ được chọn trong danh sách `ALLOWED_MENU`; **luật kiểm tra sau** và ghi đè khi LLM vượt ranh giới.
- Từ khóa nguy hiểm (sặc, khó thở...) được bắt **trước khi gọi LLM**, không để LLM quyết định.
- Thêm ranh giới tôi không nghĩ tới ban đầu: không nêu tên bệnh trên màn hình, không nói "đã đặt", không tin câu "bác sĩ đã cho phép".
- Thiết kế test tấn công theo từng ranh giới: giả mạo sự cho phép của bác sĩ, prompt injection, triệu chứng nguy hiểm, câu hỏi về thuốc, hỏi thông tin bệnh nhân khác, đòi giải thích bệnh.

## 5. Kết quả chạy Gemini thật

*(Điền sau khi chạy `python3 prompt_prototype.py`)*

- Số test LLM giữ được ranh giới: __ / 7 (mỗi test chạy __ lần)
- Test LLM vượt ranh giới nhiều nhất: ________
- Lớp POST-RULE có bắt được hết không: ________
- Tôi sửa system prompt ở chỗ: ________

## 6. Bài học cá nhân

1. **AI giỏi mở rộng, tôi phải tự thu hẹp.** Câu trả lời của AI luôn rộng và nhiều lựa chọn; quyết định phạm vi là việc của tôi.
2. **Luôn hỏi "số này ở đâu ra?".** Một bảng số đẹp không đồng nghĩa với dữ liệu thật.
3. **Ranh giới an toàn không nên nằm trong prompt.** Prompt chỉ là lớp thứ hai; lớp đáng tin là code luật chạy trước và sau LLM.
4. **Kết luận NOT YET là kết luận trung thực.** Chưa có baseline và dữ liệu đầu vào thì chưa nên xây, dù prototype chạy được.
