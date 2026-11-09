┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #08 (bản 3: pilot khoa PHCN)             │
│                                                             │
│ Bài toán (1 câu): Bệnh nhân nội trú khoa PHCN tự chọn món   │
│ trong menu bệnh viện qua hệ thống đề xuất dựa trên chỉ định │
│ ăn và hồ sơ; món không phù hợp bị chặn/cảnh báo; bữa ăn     │
│ được điều phối theo lịch tập và đối chiếu tại giường.       │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   Bệnh nhân nội trú PHCN và người nhà (ít lựa chọn, bỏ bữa  │
│   vì đang tập, khó thao tác khi tay yếu). Phụ: điều dưỡng   │
│   và bếp (xử lý đổi giờ/đổi món).                           │
│   [cần xác nhận: số giường nội trú PHCN, số ngày nằm TB]    │
│                                                             │
│ Workflow thủ công hiện tại (giả thuyết, cần xác nhận):      │
│   1. Bác sĩ chỉ định chế độ ăn                              │
│   ──> 2. Bệnh nhân nhận suất cố định hoặc chọn từ thực đơn  │
│   ──> 3. Điều dưỡng chuyển cho bếp, giao theo giờ cố định   │
│   ──> 4. Bệnh nhân đi tập, bỏ lỡ hoặc ăn nguội, không ai    │
│         ghi nhận lượng ăn                                   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 và 4 (giả thuyết:   │
│ giờ ăn xung đột lịch tập, không ghi nhận lượng ăn).         │
│   ⏱ [baseline: ___ % bữa bị bỏ/ăn nguội do lịch tập;        │
│      ___ % suất thừa; ___ phút điều dưỡng/ngày cho ăn uống; │
│      đo 2 tuần ở 1 cơ sở]                                   │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│   Bước 2: luật phân loại đỏ/vàng/xanh; xếp hạng đề xuất     │
│   theo khẩu vị và nhu cầu đạm (LUẬT/ML nhẹ); LLM chỉ diễn   │
│   đạt và hội thoại. Nuốt khó: khóa cấp độ kết cục, không    │
│   dùng LLM quyết định.                                      │
│   Bước 3: đồng bộ lịch tập → dời/giữ ấm suất (LUẬT/điều     │
│   phối).                                                    │
│   Bước 4 (giai đoạn sau): ghi nhận lượng ăn, cảnh báo ăn    │
│   kém liên tiếp cho chuyên gia dinh dưỡng.                  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Lượng ăn:  % bệnh nhân đạt mục tiêu đạm/năng lượng do     │
│              chuyên gia dinh dưỡng đặt, tăng từ ___ lên ___ │
│   Điều phối: % bữa bị bỏ/ăn nguội do lịch tập giảm ≥ ___%   │
│   Lãng phí: % suất thừa giảm ≥ 20% (ngưỡng giả thuyết)      │
│   An toàn:  món đỏ đặt thành công = 0; suất sai dị ứng/kết  │
│             cấu/chống chỉ định do lỗi hệ thống = 0          │
│   Hài lòng: điểm hài lòng ăn uống tăng từ ___ lên ___       │
│   Dùng:     ≥ ___% bệnh nhân/người nhà đặt qua hệ thống     │
│   Dừng/mở rộng: sau 6-8 tuần; món đỏ lọt qua do lỗi hệ      │
│   thống thì dừng và rà soát                                 │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
│   Rule: thẻ dinh dưỡng + kết cấu theo món, ánh xạ chỉ định  │
│   → đỏ/vàng/xanh, định tuyến đơn, đồng bộ lịch tập, đối     │
│   chiếu tại giường.                                         │
│   LLM (hẹp): giải thích, hội thoại, nhận khẩu vị.           │
│   ML (sau): xếp hạng theo lịch sử ăn hết, ghi nhận lượng ăn.│
└─────────────────────────────────────────────────────────────┘
