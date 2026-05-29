# AI Operational Pain Points - Xanh SM

| # | Quy trình nghiệp vụ | Lens | Pain point hiện tại | Tổn thất ước tính | AI Opportunity |
|---|---|---|---|---|---|
| 1 | Điều phối điểm đón/trả khách thủ công & route chưa tối ưu | Stakeholder Pain + Repetitive | Hệ thống dispatch thường chỉ tối ưu theo khoảng cách gần nhất thay vì traffic realtime + xác suất hủy chuyến + lịch sử demand. Tài xế phải gọi điện xác nhận vị trí nhiều lần. | ~8–15% thời gian xe chạy rỗng (deadhead time). Với 10,000 xe, mỗi xe lãng phí ~45 phút/ngày → thất thoát ~4,000–6,000 giờ vận hành/ngày. | AI dispatch engine dự đoán demand hotspot, ETA thực, xác suất hủy chuyến, dynamic pickup recommendation. |
| 2 | Kiểm tra & xử lý ticket CSKH sau chuyến đi | Time-consuming | Nhân viên phải đọc thủ công complaint: “tài xế đi vòng”, “đón sai điểm”, “điều hòa nóng”, “tính phí sai”. Ticket routing hiện phụ thuộc keyword đơn giản. | Một agent xử lý ~80–120 ticket/ngày. Với 300k chuyến/ngày, chỉ cần 1% complaint → ~3,000 ticket/ngày. Chi phí nhân sự + SLA chậm gây giảm retention. | LLM tự phân loại complaint, summarize call/chat, auto-propose refund policy, fraud scoring. |
| 3 | Đánh giá chất lượng tài xế qua camera & hành vi lái xe | AI-upgrade | Hiện chủ yếu reactive: chỉ xử lý khi khách report hoặc tai nạn xảy ra. Dữ liệu camera/dữ liệu telematics chưa được khai thác realtime. | Tai nạn nhẹ + phàn nàn driving behavior có thể làm giảm NPS toàn khu vực. Ước tính 3–5% khách hàng không quay lại sau trải nghiệm xấu. | Computer Vision + telematics AI phát hiện: thắng gấp, dùng điện thoại, ngủ gật, drift lane, vượt tốc độ. |
| 4 | Dự báo nhu cầu sạc & điều phối trạm sạc EV | Repetitive + Time-consuming | Tài xế thường tự chọn trạm sạc gần nhất → gây queue cục bộ giờ cao điểm. Điều phối năng lượng chưa tối ưu theo heatmap demand. | Mỗi tài xế mất thêm 15–40 phút/chu kỳ sạc vào peak hour. Với vài nghìn lượt sạc/ngày → thất thoát hàng chục nghìn giờ/tháng. | AI forecast occupancy cho trạm sạc, tối ưu routing theo pin còn lại + demand khu vực + traffic. |
| 5 | Kiểm duyệt gian lận khuyến mãi & tài khoản | Stakeholder Pain | Fraud ring tạo account ảo, self-booking, GPS spoofing để farm incentive. Rule-based detection thường phản ứng chậm. | Có thể thất thoát 1–3% ngân sách incentive/tháng. Với campaign lớn vài chục tỷ VND → leak rất đáng kể. | Graph AI + anomaly detection phát hiện cụm tài khoản bất thường, spoofed GPS, collusion giữa rider-driver. |

# PHASE 2 — QUICK PROBLEM CARDS

---

# QUICK PROBLEM CARD #1

| Field | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Hệ thống điều phối điểm đón tại Xanh SM thường gợi ý vị trí đón khách chưa chính xác, khiến tài xế phải gọi điện xác nhận nhiều lần và tăng tỷ lệ hủy chuyến. |
| **Công ty thành viên** | Xanh SM |
| **Ai đang đau (Actor)?** | Tài xế, khách hàng, đội vận hành điều phối |
| **Workflow thủ công hiện tại (3-5 bước)** | 1. Khách đặt xe → 2. Hệ thống chọn tài xế gần nhất → 3. Tài xế gọi khách xác nhận vị trí → 4. Khách/tài xế tìm nhau → 5. Chuyến bị delay hoặc hủy |
| **Bước tốn thời gian/lỗi nhất** | Xác nhận điểm đón giữa tài xế và khách (⏱ ~3–5 phút/chuyến trong giờ cao điểm) |
| **AI có thể hỗ trợ ở bước nào?** | AI dự đoán pickup point tối ưu dựa trên GPS history, traffic, heatmap và hành vi người dùng |
| **Đo thành công bằng gì?** | Giảm cancellation rate từ 12% → dưới 8%; giảm pickup confirmation time từ 5 phút → dưới 1 phút |
| **Tại sao rule-based chưa đủ tốt?** | GPS urban drift, traffic realtime và hành vi khách thay đổi liên tục tạo quá nhiều edge cases khó hard-code |
| **Human judgement hiện tại nằm ở đâu?** | Tài xế tự suy luận vị trí đón thực tế khi app chỉ sai lệch vài chục mét |
| **Failure risk nếu AI sai** | Đón sai vị trí, tăng hủy chuyến, giảm trải nghiệm khách hàng |
| **Annualized Cost Leakage (ước tính)** | ~4,000–6,000 giờ vận hành lãng phí/ngày trên quy mô 10,000 xe |
| **Quick Architecture** | Agent + Predictive AI |

---

# QUICK PROBLEM CARD #2

| Field | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Đội CSKH Xanh SM đang xử lý complaint thủ công khiến thời gian phản hồi chậm và khó scale khi volume ticket tăng cao. |
| **Công ty thành viên** | Xanh SM |
| **Ai đang đau (Actor)?** | Nhân viên CSKH, khách hàng, Operations Manager |
| **Workflow thủ công hiện tại (3-5 bước)** | 1. Khách gửi complaint → 2. Agent đọc ticket → 3. Agent kiểm tra lịch sử chuyến → 4. Soạn phản hồi → 5. Escalate nếu cần |
| **Bước tốn thời gian/lỗi nhất** | Đọc và phân loại complaint thủ công (⏱ ~7–10 phút/ticket) |
| **AI có thể hỗ trợ ở bước nào?** | LLM summarize complaint, auto classify issue và đề xuất phản hồi/refund |
| **Đo thành công bằng gì?** | Giảm Average Handling Time từ 10 phút → dưới 3 phút; tăng First Response SLA từ 75% → 95% |
| **Tại sao rule-based chưa đủ tốt?** | Complaint dạng ngôn ngữ tự nhiên, nhiều slang, typo, context ambiguity và mixed intent |
| **Human judgement hiện tại nằm ở đâu?** | Agent phải đọc context để xác định mức độ nghiêm trọng và hướng xử lý phù hợp |
| **Failure risk nếu AI sai** | Refund sai policy, escalation nhầm, phản hồi gây bức xúc khách hàng |
| **Annualized Cost Leakage (ước tính)** | ~3,000 ticket/ngày cần xử lý thủ công khi complaint rate chỉ ~1% |
| **Quick Architecture** | LLM Copilot |

---

# QUICK PROBLEM CARD #3

| Field | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Tài xế Xanh SM thường phải xếp hàng lâu tại trạm sạc do hệ thống chưa dự báo được nhu cầu sạc theo thời gian thực. |
| **Công ty thành viên** | Xanh SM |
| **Ai đang đau (Actor)?** | Tài xế EV, đội vận hành charging station |
| **Workflow thủ công hiện tại (3-5 bước)** | 1. Tài xế tự tìm trạm gần nhất → 2. Di chuyển đến trạm → 3. Phát hiện quá tải → 4. Xếp hàng hoặc đổi trạm → 5. Mất thời gian chờ |
| **Bước tốn thời gian/lỗi nhất** | Chờ queue tại trạm sạc giờ cao điểm (⏱ ~15–40 phút/lượt) |
| **AI có thể hỗ trợ ở bước nào?** | AI forecast occupancy và gợi ý routing theo battery level + demand hotspot |
| **Đo thành công bằng gì?** | Giảm average charging queue time từ 30 phút → dưới 10 phút; tăng charger utilization efficiency thêm 15% |
| **Tại sao rule-based chưa đủ tốt?** | Demand thay đổi theo thời tiết, giờ cao điểm, event và traffic realtime nên static threshold không đủ chính xác |
| **Human judgement hiện tại nằm ở đâu?** | Tài xế tự quyết định đổi trạm dựa trên kinh nghiệm cá nhân |
| **Failure risk nếu AI sai** | Tài xế hết pin giữa chuyến hoặc congestion nghiêm trọng tại trạm |
| **Annualized Cost Leakage (ước tính)** | Hàng chục nghìn giờ chờ sạc bị mất mỗi tháng |
| **Quick Architecture** | Predictive AI + Optimization Engine |# PHASE 2 — QUICK PROBLEM CARDS

---

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ Bài toán (1 câu):                                           │
│ Hệ thống điều phối điểm đón tại Xanh SM thường gợi ý        │
│ sai vị trí khách, khiến tài xế phải gọi xác nhận nhiều lần. │
│                                                             │
│ Công ty thành viên:                                         │
│ [ ] VinFast   [x] Xanh SM   [ ] Vinhomes   [ ] Vinmec      │
│ [ ] Khác: ________________________________________________ │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Tài xế                                                    │
│ - Khách hàng                                                │
│ - Đội vận hành dispatch                                     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Workflow thủ công hiện tại (3-5 bước):                      │
│                                                             │
│ 1. Khách đặt xe                                             │
│        ↓                                                    │
│ 2. System assign tài xế gần nhất                            │
│        ↓                                                    │
│ 3. Tài xế gọi xác nhận vị trí                               │
│        ↓                                                    │
│ 4. Khách/tài xế tìm nhau                                    │
│        ↓                                                    │
│ 5. Delay hoặc hủy chuyến                                    │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Bước tốn thời gian/lỗi nhất?                                │
│ Xác nhận điểm đón giữa tài xế và khách                      │
│ (⏱ ~3–5 phút/chuyến giờ cao điểm)                           │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ AI dự đoán pickup point tối ưu dựa trên GPS history,        │
│ traffic realtime và hành vi người dùng                      │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm cancellation rate từ 12% → dưới 8%                  │
│ - Giảm pickup confirmation time từ 5 phút → dưới 1 phút   │
│ - Giảm deadhead time ~10–15%                               │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Tại sao rule-based chưa đủ tốt?                             │
│ GPS urban drift + traffic realtime + edge cases vị trí      │
│ khiến hard-code rule hoạt động không ổn định                │
│                                                             │
│ Human judgement hiện tại nằm ở đâu?                         │
│ Tài xế tự suy luận vị trí đón thực tế                       │
│                                                             │
│ Failure risk nếu AI sai?                                    │
│ Đón sai khách, tăng hủy chuyến, giảm trải nghiệm            │
│                                                             │
│ Annualized Cost Leakage (ước tính):                         │
│ ~4,000–6,000 giờ vận hành lãng phí/ngày                     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Quick Architecture:                                         │
│ [ ] No AI   [ ] Rule   [ ] LLM   [x] Agent + Predictive AI │
└─────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ Bài toán (1 câu):                                           │
│ Đội CSKH Xanh SM xử lý complaint thủ công khiến SLA phản    │
│ hồi chậm và khó scale khi volume ticket tăng cao.           │
│                                                             │
│ Công ty thành viên:                                         │
│ [ ] VinFast   [x] Xanh SM   [ ] Vinhomes   [ ] Vinmec      │
│ [ ] Khác: ________________________________________________ │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Nhân viên CSKH                                            │
│ - Khách hàng                                                │
│ - Operations Manager                                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Workflow thủ công hiện tại (3-5 bước):                      │
│                                                             │
│ 1. Khách gửi complaint                                      │
│        ↓                                                    │
│ 2. Agent đọc ticket                                         │
│        ↓                                                    │
│ 3. Kiểm tra lịch sử chuyến                                  │
│        ↓                                                    │
│ 4. Soạn phản hồi                                            │
│        ↓                                                    │
│ 5. Escalate nếu cần                                         │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Bước tốn thời gian/lỗi nhất?                                │
│ Đọc và phân loại complaint thủ công                         │
│ (⏱ ~7–10 phút/ticket)                                       │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ LLM summarize complaint, auto classify issue và đề xuất     │
│ phản hồi/refund policy                                      │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm AHT từ 10 phút → dưới 3 phút                        │
│ - Tăng First Response SLA từ 75% → 95%                     │
│ - Giảm backlog ticket >24h thêm 60%                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Tại sao rule-based chưa đủ tốt?                             │
│ Complaint có slang, typo, mixed intent và context ambiguity │
│ khiến keyword/rule engine hoạt động thiếu chính xác         │
│                                                             │
│ Human judgement hiện tại nằm ở đâu?                         │
│ Agent phải tự đánh giá mức độ nghiêm trọng và hướng xử lý   │
│                                                             │
│ Failure risk nếu AI sai?                                    │
│ Refund sai policy hoặc phản hồi gây bức xúc khách hàng      │
│                                                             │
│ Annualized Cost Leakage (ước tính):                         │
│ ~3,000 ticket/ngày cần xử lý thủ công                       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Quick Architecture:                                         │
│ [ ] No AI   [ ] Rule   [x] LLM   [ ] Agent                 │
└─────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ Bài toán (1 câu):                                           │
│ Tài xế Xanh SM thường phải chờ lâu tại trạm sạc do hệ thống │
│ chưa dự báo chính xác nhu cầu sạc theo thời gian thực.      │
│                                                             │
│ Công ty thành viên:                                         │
│ [ ] VinFast   [x] Xanh SM   [ ] Vinhomes   [ ] Vinmec      │
│ [ ] Khác: ________________________________________________ │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Tài xế EV                                                 │
│ - Đội vận hành charging station                             │
│ - Khách hàng bị delay chuyến                                │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Workflow thủ công hiện tại (3-5 bước):                      │
│                                                             │
│ 1. Tài xế tự tìm trạm gần nhất                              │
│        ↓                                                    │
│ 2. Di chuyển đến trạm                                       │
│        ↓                                                    │
│ 3. Phát hiện queue quá tải                                  │
│        ↓                                                    │
│ 4. Xếp hàng hoặc đổi trạm                                   │
│        ↓                                                    │
│ 5. Mất thời gian chờ                                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Bước tốn thời gian/lỗi nhất?                                │
│ Chờ queue tại trạm sạc giờ cao điểm                         │
│ (⏱ ~15–40 phút/lượt)                                        │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ AI forecast occupancy và gợi ý routing theo battery level,  │
│ demand hotspot và traffic realtime                          │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Đo thành công bằng gì (Metric có số)?                       │
│ - Giảm queue time từ 30 phút → dưới 10 phút                │
│ - Tăng charger utilization efficiency thêm 15%             │
│ - Giảm số lần đổi trạm giữa chừng ~40%                     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Tại sao rule-based chưa đủ tốt?                             │
│ Demand thay đổi theo traffic, thời tiết và giờ cao điểm nên │
│ static threshold/rule khó dự báo chính xác                  │
│                                                             │
│ Human judgement hiện tại nằm ở đâu?                         │
│ Tài xế tự quyết định đổi trạm dựa trên kinh nghiệm cá nhân  │
│                                                             │
│ Failure risk nếu AI sai?                                    │
│ Congestion nghiêm trọng hoặc tài xế hết pin giữa chuyến     │
│                                                             │
│ Annualized Cost Leakage (ước tính):                         │
│ Hàng chục nghìn giờ chờ sạc bị mất mỗi tháng                │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│ Quick Architecture:                                         │
│ [ ] No AI   [ ] Rule   [ ] LLM   [x] Predictive AI         │
└─────────────────────────────────────────────────────────────┘