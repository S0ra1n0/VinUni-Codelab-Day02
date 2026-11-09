# 03 — AI Log: Nhật ký dùng AI làm thought-partner

**Công cụ:** Claude (Claude Code) làm trợ lý về ý tưởng và sửa code; Gemini (`gemini-3.5-flash`) làm model cho prompt prototype.

---

## 1. AI đã giúp gì?

| Giai đoạn | AI giúp                                                         | Giá trị thực tế                                                                                                                    |
| --------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Đọc đề    | Tóm tắt README, worksheet, rubric và **đọc cả `autograder.py`** | Phát hiện điều README không nói: autograder kiểm tra đủ 4 file trên **từng branch**, nên branch cá nhân cũng phải có `02` và `04`. |
| Chia việc | Đề xuất vai trò theo từng gate G1–G4 và lịch họp chốt           | Nhóm 6 người có người chịu trách nhiệm rõ cho từng phần điểm.                                                                      |
| Phase 1   | Brainstorm 5 bài toán Xanh SM theo 4 lens                       | Có danh sách nhanh, tránh trùng với file ví dụ.                                                                                    |
| Phase 2   | Tạo template card, làm mẫu Card #1, rồi điền Card #2–3          | Học được cấu trúc card tốt: số phút từng bước, 🔴/🔄, metric "từ A xuống B".                                                       |
| Phase 3–5 | Soạn report, vẽ sơ đồ bằng Mermaid                              | Tiết kiệm thời gian trình bày, tập trung vào nội dung.                                                                             |
| Phase 4   | Chạy prototype và đọc kết quả                                   | Có bằng chứng thật (2/2 test Passed) thay vì đoán.                                                                                 |

---

## 2. AI sai hoặc có rủi ro ở đâu?

1. **Số liệu không có nguồn (rủi ro hallucination).** Khi brainstorm, AI đưa ra các con số như "150 vụ/ngày", "~20% vụ hoàn tiền sai". Đây là ước lượng, không phải số liệu thật của Xanh SM. Nếu chép thẳng vào report thì sẽ thành số bịa.
   → **Cách sửa:** gắn nhãn _"giả định"_ cho mọi con số và ghi rõ phép tính (VD: 150 × 20 phút = 50 giờ công). Ở Phase 5, điều kiện GO là phải thu baseline thật trong 2 tuần đầu.

2. **AI hiểu sai ưu tiên lúc đầu.** Câu trả lời đầu tiên chia việc cho nhóm 5 người và nói nhiều về code `.py`, trong khi nhóm có 6 người và trọng tâm là các file `.md` (60 điểm nhóm).
   → **Cách sửa:** nói rõ bối cảnh: _"Quan trọng là các file .md, nhóm 6 người"_. Bài học: phải đưa ràng buộc ngay từ prompt đầu.

3. **Output quá dài.** Card #1 do AI viết rất chi tiết nhưng dài, khó đọc như một "quick card".
   → **Cách sửa:** yêu cầu rút gọn 2 lần. Lần 2 AI cắt các ý trùng lặp nhưng giữ những phần ăn điểm (số phút, 🔴/🔄, metric có số).

4. **Template bị hỏng định dạng.** AI dùng `___` làm chỗ trống; công cụ format Markdown trong editor tự biến chúng thành ký hiệu in đậm `**_ … _**`.
   → **Cách sửa:** AI đổi chỗ trống sang `…` để không bị format lại.

5. **Model Gemini "nói quá" trong prototype.** Ở test pin 2%, bản nháp có `[DRAFT_ONLY]` nhưng lại viết _"Chúng tôi đã kích hoạt lệnh điều xe sạc lưu động"_ — khẳng định một hành động chưa ai duyệt. Test vẫn Passed vì bộ kiểm tra chỉ tìm từ khóa.
   → **Cách sửa:** thêm ranh giới cho bài toán của nhóm: không được viết "đã tìm thấy đồ" khi tài xế chưa xác nhận. Bài học: test kiểu tìm từ khóa chưa đủ, phải đọc output thật.

---

## 3. Mình đã điều chỉnh cách làm việc với AI thế nào?

- **Đưa bối cảnh và ràng buộc ngay từ đầu:** số người, file nào quan trọng, độ dài mong muốn.
- **Bắt AI tách "sự thật" và "giả định":** mọi con số phải có nguồn hoặc phép tính.
- **Kiểm chứng bằng cách chạy thật:** chạy prototype, render thử sơ đồ Mermaid trước khi chụp.
- **Giữ quyền quyết định:** AI đề xuất chọn Card #1 và GO scope hẹp, nhưng nhóm vẫn phải họp để chốt bài toán và quyết định cuối.

---

## 4. Tự đánh giá

AI tăng tốc rất nhiều ở phần trình bày, đọc tài liệu và tìm chi tiết dễ bị bỏ sót (như cách autograder chấm). Nhưng AI **không biết quy trình thật** của Xanh SM: mọi con số và bước trong workflow vẫn cần người có hiểu biết thực tế xác nhận. Vai trò đúng của AI trong buổi lab là _thought-partner để phản biện và trình bày_, không phải nguồn sự thật.
