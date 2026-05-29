
Phase 1


| # | Subsidiary (VinFast/Xanh SM...) | Lens   | Mô tả ngắn bài toán |
|---|---------------------------------|--------|---------------------|
| 1 | Vinhomes                        | Lặp lại |Xử lý yêu cầu bảo trì từ cư dân thủ công — hotline → ghi tay → điều phối kỹ thuật → cập nhật Excel. ~80–150 ticket/ngày/khu, tốn ~12 phút/ticket. Tổng ~3.200 giờ/tháng trên 20 khu. |
| 2 | VinFast                         | Lặp lại |Kiểm tra lỗi ngoại quan (paint defect, panel gap) trên dây chuyền lắp ráp bằng mắt người — mỗi xe cần 2–3 QC kiểm thủ công ~8 phút/xe. Với sản lượng ~400 xe/ngày, tổng ~1.600–2.400 giờ QC/tháng, tỷ lệ bỏ sót lỗi ~3–5%. |
| 3 | VinMec                          |   Tốn thời gian     |Bác sĩ ghi chép hồ sơ bệnh án (EMR) thủ công sau mỗi ca khám — trung bình 15–20 phút/bệnh nhân chỉ để nhập liệu. Với ~500 lượt khám/ngày/bệnh viện, toàn hệ thống 8 bệnh viện tiêu tốn ~1.000 giờ bác sĩ/ngày vào tác vụ hành chính. |
| 4 |  Xanh SM                               |  AI-upgrade      |Hệ thống điều phối chuyến chưa dự đoán được cụm cầu giờ cao điểm — tài xế thường bị điều về vùng thưa khách sau khi trả khách. Tỷ lệ xe chạy không (deadhead) ước ~28–35%, mỗi % deadhead giảm = ~2 tỷ VNĐ/năm doanh thu tăng thêm. |
| 5 |     VinUni                            |  Tốn thời gian      |Giảng viên chấm bài tự luận, tiểu luận thủ công — mỗi bài tốn 20–45 phút, một khóa học ~80 sinh viên = 60–60 giờ chấm/kỳ/môn. Toàn trường ~200 môn học/kỳ, tổng thời gian chấm ước ~10.000 giờ giảng viên/học kỳ không sinh ra giá trị giảng dạy trực tiếp. |


Phase 2

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                      │
│                                                                             │
│ Bài toán (1 câu):                                                          │
│ Tự động hoá ghi chép hồ sơ bệnh án sau mỗi lượt khám tại Vinmec            │
│                                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                 │
│                    [x] Vinmec   [ ] Khác                                   │
│                                                                             │
│ Ai đang đau (Actor)?                                                       │
│ Bác sĩ lâm sàng – phải nhập liệu thủ công vào hệ thống EMR sau mỗi ca khám │
│ làm giảm thời gian dành cho bệnh nhân.                                     │
│                                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                                     │
│                                                                             │
│  1. Khám bệnh & hỏi bệnh nhân                                              │
│             ↓                                                              │
│  2. Nhập tay triệu chứng + chẩn đoán EMR (15-20 phút/lượt)                 │
│             ↓                                                              │
│  3. Kê đơn & chỉ định xét nghiệm                                           │
│             ↓                                                              │
│  4. Lưu hồ sơ & chuyển khoa                                                │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                           │
│ ➜ Bước 2 (⏱ 15-20 phút/lượt)                                               │
│                                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                      │
│ ➜ Bước 2: Voice-to-Text + LLM tạo bản nháp EMR tự động                     │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                      │
│                                                                             │
│ • Thời gian ghi chép: 15-20 phút → < 3 phút                                │
│ • Tỷ lệ tự động hoá hồ sơ: > 80%                                           │
│ • Tiết kiệm ~800 giờ bác sĩ/ngày toàn hệ thống                             │
│                                                                             │
│ Quick Architecture                                                         │
│                                                                             │
│ Consultation Audio                                                         │
│          │                                                                  │
│          ▼                                                                  │
│   Speech-to-Text                                                           │
│          │                                                                  │
│          ▼                                                                  │
│      Medical LLM                                                           │
│          │                                                                  │
│          ▼                                                                  │
│   Structured EMR Draft                                                     │
│          │                                                                  │
│          ▼                                                                  │
│ Doctor Review & Confirm                                                    │
│                                                                             │
│ AI Stack:                                                                  │
│ [ ] No AI   [ ] Rule-based   [x] LLM   [ ] Agent                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                      │
│                                                                             │
│ Bài toán (1 câu):                                                          │
│ Tự động điều phối & dự đoán cụm cầu cho tài xế Xanh SM                     │
│                                                                             │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes                 │
│                    [ ] Vinmec   [ ] Khác                                   │
│                                                                             │
│ Ai đang đau (Actor)?                                                       │
│ Tài xế Xanh SM — sau khi trả khách bị điều về vùng thưa, phải chờ         │
│ 15–25 phút cho chuyến tiếp.                                                │
│                                                                             │
│ Trung tâm điều phối — điều xe thủ công dựa trên bản đồ tĩnh, không         │
│ dự báo được nhu cầu theo thời gian thực.                                   │
│                                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                                     │
│                                                                             │
│  1. Tài xế trả khách                                                       │
│             ↓                                                              │
│  2. Hệ thống gợi ý điểm đón gần nhất                                       │
│     nhưng không xét nhu cầu thực tế                                        │
│     ⏱ Chờ 15–25 phút/chuyến                                                │
│             ↓                                                              │
│  3. Tài xế di chuyển theo gợi ý                                            │
│             ↓                                                              │
│  4. Nhận hoặc bỏ lỡ chuyến mới                                             │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                           │
│ ➜ Bước 2 — Gợi ý điều phối không dựa trên dự báo demand                    │
│                                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                      │
│ ➜ Bước 2 — Demand Forecasting + Agent điều phối tài xế                     │
│                                                                             │
│ Cách AI hỗ trợ:                                                            │
│ Demand-forecasting model dự đoán cụm cầu trong 15 phút tới dựa trên:       │
│ lịch sử chuyến, thời tiết, sự kiện, khung giờ và vị trí hiện tại.          │
│                                                                             │
│ Agent tự động đẩy gợi ý di chuyển tối ưu cho tài xế thay vì chỉ chọn       │
│ điểm đón gần nhất.                                                         │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                      │
│                                                                             │
│ • Tỷ lệ xe chạy không / deadhead: 28–35% → dưới 18%                        │
│ • Mỗi 1% deadhead giảm ≈ +2 tỷ VNĐ doanh thu/năm                           │
│ • Thời gian chờ chuyến tiếp theo: 15–25 phút → dưới 10 phút                │
│                                                                             │
│ Quick Architecture                                                         │
│                                                                             │
│ Trip History + Weather + Events + Driver Location                          │
│                         │                                                   │
│                         ▼                                                   │
│              Demand Forecasting Model                                      │
│              LSTM / XGBoost / Time-series ML                               │
│                         │                                                   │
│                         ▼                                                   │
│              Hotspot Demand Prediction                                     │
│                         │                                                   │
│                         ▼                                                   │
│              Dispatch Optimization Agent                                   │
│                         │                                                   │
│                         ▼                                                   │
│              Driver Reposition Suggestion                                  │
│                         │                                                   │
│                         ▼                                                   │
│              More Trips, Less Waiting, Lower Deadhead                      │
│                                                                             │
│ AI Stack:                                                                  │
│ [ ] No AI   [ ] Rule-based   [ ] LLM   [x] Agent                           │
└─────────────────────────────────────────────────────────────────────────────┘
```
````markdown
```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                      │
│                                                                             │
│ Bài toán (1 câu):                                                          │
│ Tự động phân loại & soạn thảo phản hồi đánh giá 1–2 sao cho cư dân         │
│ Vinhomes                                                                    │
│                                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes                 │
│                    [ ] Vinmec   [ ] Khác                                   │
│                                                                             │
│ Ai đang đau (Actor)?                                                       │
│ Nhân viên CSKH BQL khu đô thị — phải đọc từng review, xác minh sự việc,   │
│ soạn phản hồi riêng lẻ và chờ cấp trên duyệt.                              │
│                                                                             │
│ Dễ bỏ sót các review khẩn trong giờ cao điểm, đặc biệt với đánh giá        │
│ 1–2 sao liên quan đến bảo trì, an ninh, vệ sinh hoặc dịch vụ cư dân.       │
│                                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                                     │
│                                                                             │
│  1. Nhân viên đọc review mới trên nhiều kênh                               │
│             ↓                                                              │
│  2. Phân loại chủ đề & soạn phản hồi thủ công                              │
│     ⏱ 20–35 phút/review                                                    │
│             ↓                                                              │
│  3. Gửi duyệt cấp trên                                                     │
│             ↓                                                              │
│  4. Đăng phản hồi lên kênh                                                  │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất?                                           │
│ ➜ Bước 1 + 2 — Đọc, phân loại và soạn phản hồi thủ công                    │
│                                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                                      │
│ ➜ Bước 1 + 2 — LLM phân loại sentiment, chủ đề và sinh phản hồi nháp       │
│                                                                             │
│ Cách AI hỗ trợ:                                                            │
│ LLM tự động phân loại sentiment và chủ đề review như:                      │
│ bảo trì, an ninh, vệ sinh, tiện ích, thái độ phục vụ, phí dịch vụ.         │
│                                                                             │
│ Sau đó hệ thống sinh bản nháp phản hồi đúng tone Vinhomes; nhân viên       │
│ CSKH chỉ cần review, chỉnh sửa nếu cần và bấm gửi.                         │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                      │
│                                                                             │
│ • Thời gian soạn phản hồi: 20–35 phút → dưới 3 phút                        │
│ • Tỷ lệ phản hồi trong 2h: ~40% → 95%+                                     │
│ • Google rating tăng kỳ vọng: +0.2 đến +0.3 điểm                           │
│                                                                             │
│ Quick Architecture                                                         │
│                                                                             │
│ Reviews from Google / App / Hotline / Social Channels                      │
│                         │                                                   │
│                         ▼                                                   │
│              Review Ingestion Pipeline                                     │
│                         │                                                   │
│                         ▼                                                   │
│              LLM Sentiment & Topic Classifier                              │
│                         │                                                   │
│                         ▼                                                   │
│              Priority Detection                                            │
│              1-star / 2-star / urgent issue                                │
│                         │                                                   │
│                         ▼                                                   │
│              LLM Response Draft Generator                                  │
│                         │                                                   │
│                         ▼                                                   │
│              CSKH Review & Manager Approval                                │
│                         │                                                   │
│                         ▼                                                   │
│              Publish Response                                              │
│                                                                             │
│ AI Stack:                                                                  │
│ [ ] No AI   [ ] Rule-based   [x] LLM   [ ] Agent                           │
└─────────────────────────────────────────────────────────────────────────────┘
```
````
