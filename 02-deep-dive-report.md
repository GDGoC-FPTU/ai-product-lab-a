
# DEEP-DIVE REPORT — VINMEC AI EMR ASSISTANT

### Member:
Đỗ Đức Anh 2A202600976
Đàm Xuân Giáp 2A202600740

## 1. Quyết định lựa chọn bài toán

### Bài toán được chọn

**Tự động hoá ghi chép hồ sơ bệnh án sau mỗi lượt khám tại Vinmec**

### Công ty thành viên

**Vinmec**

### Lý do lựa chọn

Nhóm chọn bài toán này để thực hiện Deep-Dive vì đây là một quy trình có tần suất lặp lại cao, tiêu tốn nhiều thời gian của bác sĩ và ảnh hưởng trực tiếp đến hiệu suất vận hành bệnh viện. Sau mỗi lượt khám, bác sĩ phải tự nhập thông tin vào hệ thống EMR như triệu chứng, tiền sử bệnh, chẩn đoán, chỉ định xét nghiệm, đơn thuốc và kế hoạch điều trị.

Bottleneck chính nằm ở bước ghi chép hồ sơ bệnh án sau khám, mất khoảng **15–20 phút/lượt khám**. Đây là tác vụ có nhiều dữ liệu ngôn ngữ tự nhiên, phù hợp để AI hỗ trợ bằng cách chuyển hội thoại bác sĩ - bệnh nhân thành bản nháp hồ sơ EMR có cấu trúc.

Tuy nhiên, do đây là lĩnh vực y tế có rủi ro cao, AI không được phép tự chẩn đoán, tự kê đơn hoặc tự lưu hồ sơ. AI chỉ đóng vai trò **trợ lý tạo bản nháp**, còn bác sĩ vẫn là người review và xác nhận cuối cùng.

---

# 2. Phase 3.2 — Problem Statement 6-field & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | **Bác sĩ lâm sàng tại Vinmec** là người trực tiếp thực hiện tác vụ hằng ngày. Sau mỗi lượt khám, bác sĩ phải tổng hợp lại thông tin từ cuộc hội thoại với bệnh nhân, ghi nhận triệu chứng, tiền sử, nhận định lâm sàng, chẩn đoán, chỉ định xét nghiệm, đơn thuốc và kế hoạch điều trị vào hệ thống EMR. |
| **2. Current Workflow** | Quy trình hiện tại gồm: **1)** Bác sĩ khám và hỏi bệnh nhân. **2)** Bác sĩ ghi nhớ hoặc ghi chú tạm các thông tin quan trọng. **3)** Sau khám, bác sĩ nhập tay triệu chứng, tiền sử, chẩn đoán và nhận định vào EMR. **4)** Bác sĩ kê đơn hoặc chỉ định xét nghiệm. **5)** Hồ sơ được lưu và chuyển sang bộ phận liên quan nếu cần. Công cụ chính đang dùng là hệ thống EMR nội bộ, ghi chú tạm và thao tác nhập liệu thủ công của bác sĩ. |
| **3. Bottleneck** | Bottleneck lớn nhất là bước **nhập tay hồ sơ bệnh án vào EMR sau mỗi lượt khám**. Bước này mất khoảng **15–20 phút/lượt**, dễ gây thiếu sót thông tin, sai cấu trúc hồ sơ, nhập liệu không nhất quán giữa các bác sĩ và làm giảm thời gian bác sĩ có thể dành cho bệnh nhân tiếp theo. |
| **4. Business Impact** | Tác động kinh doanh/vận hành gồm: **1)** lãng phí thời gian chuyên môn của bác sĩ cho tác vụ hành chính; **2)** giảm số lượt bệnh nhân có thể phục vụ mỗi ngày; **3)** kéo dài thời gian chờ của bệnh nhân; **4)** tăng nguy cơ thiếu dữ liệu trong bệnh án; **5)** giảm chất lượng dữ liệu phục vụ phân tích y tế sau này. Nếu một bác sĩ mất trung bình 15–20 phút nhập liệu sau mỗi ca, tổng thời gian hành chính bị rò rỉ trên toàn hệ thống có thể lên tới hàng trăm giờ bác sĩ/ngày. |
| **5. Success Metric** | Các chỉ số đo thành công gồm: **1)** Thời gian ghi chép EMR sau khám giảm từ **15–20 phút xuống dưới 3 phút/lượt**. **2)** Tỷ lệ bản nháp EMR được bác sĩ chấp nhận sau chỉnh sửa nhẹ đạt **≥ 80%**. **3)** Tỷ lệ hồ sơ thiếu trường thông tin quan trọng giảm ít nhất **50%**. **4)** 100% hồ sơ do AI tạo phải có trạng thái `DRAFT_ONLY` và được bác sĩ xác nhận trước khi lưu chính thức. |
| **6. Operational Boundary** | AI chỉ được phép tạo **bản nháp hồ sơ EMR** từ transcript cuộc khám. AI được phép tóm tắt triệu chứng, tiền sử, nội dung bác sĩ đã trao đổi, chẩn đoán đã được bác sĩ nêu, chỉ định xét nghiệm đã được bác sĩ nói và kế hoạch theo dõi. AI **tuyệt đối không được** tự đưa ra chẩn đoán mới, tự kê đơn, tự thêm chỉ định xét nghiệm, tự thay đổi nội dung điều trị, tự lưu hồ sơ chính thức hoặc đưa lời khuyên y tế trực tiếp cho bệnh nhân. Bác sĩ bắt buộc review, chỉnh sửa và xác nhận trước khi dữ liệu được ghi vào EMR. |

---

# 3.Future-State Flow & AI Fit

## AI Fit Matrix

```text
[ ] Rule / State-Machine
[x] LLM Feature
[ ] Agentic Loop
````

## 3.2. Lý do chọn LLM Feature

Giải pháp phù hợp nhất là **LLM Feature** vì bài toán cốt lõi là xử lý ngôn ngữ tự nhiên trong hội thoại khám bệnh.

LLM có thể hỗ trợ tốt các tác vụ:

* Chuyển transcript cuộc khám thành nội dung có cấu trúc.
* Tóm tắt triệu chứng, tiền sử, chẩn đoán và kế hoạch điều trị.
* Chuẩn hóa nội dung thành format EMR.
* Đánh dấu các phần chưa chắc chắn để bác sĩ kiểm tra.
* Sinh bản nháp hồ sơ bệnh án theo văn phong y khoa.

Không chọn **Rule / State-Machine** vì hội thoại giữa bác sĩ và bệnh nhân rất đa dạng, khó bao phủ bằng luật cố định. Bệnh nhân có thể mô tả triệu chứng bằng nhiều cách khác nhau, không theo template cố định.

Không chọn **Agentic Loop** ở giai đoạn đầu vì đây là lĩnh vực y tế có rủi ro cao. AI không nên tự ra quyết định nhiều bước, tự gọi tool để lưu hồ sơ, kê đơn hoặc thay đổi chỉ định. Giai đoạn prototype chỉ nên giới hạn ở mức LLM tạo bản nháp và bác sĩ duyệt.

---

## Future-State Flow

```text
┌────────────────────────────────────┐
│ Bước 1                             │
│ Bác sĩ khám và hỏi bệnh nhân       │
│                                    │
│ Human Step                         │
│ Input: Hội thoại khám bệnh         │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│ 🔵 Bước 2                          │
│ Speech-to-Text chuyển âm thanh     │
│ cuộc khám thành transcript         │
│                                    │
│ AI Step                            │
│ Output: Transcript cuộc khám       │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│ 🔵 Bước 3                          │
│ LLM trích xuất thông tin y khoa    │
│ từ transcript                      │
│                                    │
│ AI Step                            │
│ Output: Structured Medical Data    │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│ 🔵 Bước 4                          │
│ LLM tạo bản nháp hồ sơ EMR         │
│ theo format chuẩn                  │
│                                    │
│ AI Step                            │
│ Output: EMR Draft                  │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│ 🟢 Bước 5                          │
│ Bác sĩ review, chỉnh sửa và        │
│ xác nhận nội dung                  │
│                                    │
│ Human-in-the-loop                  │
│ Output: Approved EMR Draft         │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│ Bước 6                             │
│ Lưu hồ sơ chính thức vào EMR       │
│                                    │
│ Chỉ thực hiện sau khi bác sĩ duyệt │
└────────────────────────────────────┘
```

---



## Các bước AI xử lý

```text
🔵 AI Step 1 — Speech-to-Text
Chuyển âm thanh cuộc khám thành transcript văn bản.

🔵 AI Step 2 — Medical Information Extraction
LLM trích xuất các trường thông tin:
- Lý do đến khám
- Triệu chứng chính
- Thời gian xuất hiện triệu chứng
- Tiền sử bệnh
- Dị ứng nếu có
- Thuốc đang dùng nếu có
- Nhận định của bác sĩ
- Chẩn đoán đã được bác sĩ nêu
- Chỉ định xét nghiệm / cận lâm sàng
- Kế hoạch điều trị / theo dõi

🔵 AI Step 3 — EMR Draft Generation
LLM tạo bản nháp hồ sơ bệnh án theo cấu trúc:
- Chief Complaint
- History of Present Illness
- Past Medical History
- Assessment
- Plan
- Doctor Notes
- Missing Information
- Needs Doctor Review
```

---

## Human-in-the-loop

Bác sĩ là người kiểm soát cuối cùng. AI không được tự động lưu bất kỳ nội dung nào vào EMR nếu chưa có xác nhận của bác sĩ.

```text
🟢 Bác sĩ cần review:
- Transcript có bị nghe sai không?
- Triệu chứng có bị thiếu hoặc hiểu sai không?
- Chẩn đoán có đúng là nội dung bác sĩ đã nói không?
- Chỉ định xét nghiệm / đơn thuốc có bị AI tự thêm không?
- Có thông tin nhạy cảm nào cần loại bỏ hoặc chỉnh sửa không?
- Các trường bị đánh dấu LOW_CONFIDENCE có cần nhập tay không?
```

Chỉ sau khi bác sĩ bấm **Confirm**, hệ thống mới cho phép lưu bản ghi vào EMR.

---

## Fallback Plan

```text
↩️ Fallback 1 — Transcript không rõ
Nếu chất lượng âm thanh thấp, nhiều tiếng ồn hoặc transcript thiếu đoạn,
hệ thống trả về trạng thái LOW_CONFIDENCE_TRANSCRIPT và yêu cầu bác sĩ
kiểm tra hoặc nhập tay phần còn thiếu.

↩️ Fallback 2 — LLM không chắc chắn
Nếu LLM không chắc về triệu chứng, chẩn đoán hoặc chỉ định,
hệ thống không tự điền chắc chắn mà đánh dấu NEEDS_DOCTOR_REVIEW.

↩️ Fallback 3 — Nội dung vượt ranh giới
Nếu prompt hoặc người dùng yêu cầu AI tự chẩn đoán, tự kê đơn,
tự thêm xét nghiệm hoặc tự lưu hồ sơ, hệ thống phải từ chối và trả về
DOCTOR_APPROVAL_REQUIRED.

↩️ Fallback 4 — Ca bệnh nhạy cảm/rủi ro cao
Với cấp cứu, sản khoa rủi ro cao, tâm thần, pháp lý hoặc thông tin mâu thuẫn,
hệ thống chỉ được tóm tắt transcript và yêu cầu bác sĩ tự hoàn thiện EMR.

↩️ Fallback 5 — Lỗi hệ thống
Nếu Speech-to-Text hoặc LLM lỗi, quy trình quay lại phương án cũ:
bác sĩ nhập EMR thủ công.
```

---

## Boundary Rules

```text
AI ĐƯỢC PHÉP:
[✓] Chuyển lời nói thành transcript.
[✓] Tóm tắt nội dung cuộc khám.
[✓] Trích xuất thông tin y khoa đã xuất hiện trong transcript.
[✓] Sinh bản nháp EMR có cấu trúc.
[✓] Đánh dấu phần thiếu hoặc không chắc chắn.
[✓] Gợi ý trường nào cần bác sĩ kiểm tra lại.

AI KHÔNG ĐƯỢC PHÉP:
[✗] Tự đưa ra chẩn đoán mới.
[✗] Tự kê đơn thuốc.
[✗] Tự thêm chỉ định xét nghiệm.
[✗] Tự thay đổi kế hoạch điều trị.
[✗] Tự lưu hồ sơ chính thức vào EMR.
[✗] Tự gửi lời khuyên y tế trực tiếp cho bệnh nhân.
[✗] Che giấu mức độ không chắc chắn.
```

---

# Phase 5 — EVALUATE

## AI Readiness Checklist

| Checklist                                                                   | Đánh giá           | Lý do                                                                                                                                                                                                                                                |
| --------------------------------------------------------------------------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?**                      | [ ] Chưa hoàn toàn | Cần có transcript cuộc khám đã được ẩn danh, mẫu hồ sơ EMR chuẩn và dữ liệu đối chiếu do bác sĩ xác nhận. Nếu chưa có dữ liệu thật, nhóm có thể bắt đầu bằng dữ liệu giả lập nhưng vẫn cần kiểm thử với bác sĩ thật hoặc người có chuyên môn y khoa. |
| **2. Rủi ro khi AI sai có nằm trong tầm kiểm soát qua HITL hoặc Fallback?** | [x] Có             | AI chỉ tạo bản nháp, không được tự lưu hồ sơ, tự kê đơn hoặc tự chẩn đoán. Bác sĩ bắt buộc review và xác nhận trước khi ghi vào EMR. Các trường hợp không chắc chắn sẽ được đánh dấu `LOW_CONFIDENCE` hoặc `NEEDS_DOCTOR_REVIEW`.                    |
| **3. Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?**                | [x] Có khả năng    | Bác sĩ có động lực sử dụng nếu hệ thống thực sự giảm thời gian nhập liệu từ 15–20 phút xuống dưới 3 phút. Tuy nhiên, cần thiết kế giao diện review nhanh, không làm bác sĩ mất thêm thao tác.                                                        |

---

## Quyết định cuối cùng

```text
[x] GO — Bắt đầu xây dựng Prototype
[ ] NOT YET — Cần tích lũy thêm dữ liệu/xác lập baseline
[ ] NO-GO — Không khả thi / Rule-based tốt hơn
```

## Justification

Nhóm đề xuất quyết định **GO**, nhưng chỉ triển khai ở phạm vi **prototype hẹp**, chưa triển khai toàn hệ thống.

### Luận điểm 1 — Bài toán có bottleneck rõ ràng

Bước nhập EMR thủ công mất khoảng **15–20 phút/lượt khám**, là một bottleneck cụ thể và đo được. Nếu AI giúp giảm thời gian này xuống dưới **3 phút/lượt**, bác sĩ sẽ tiết kiệm được đáng kể thời gian hành chính sau mỗi ca khám.

### Luận điểm 2 — Tác vụ phù hợp với năng lực của LLM

Dữ liệu đầu vào là hội thoại khám bệnh và ghi chú lâm sàng, tức là dữ liệu ngôn ngữ tự nhiên. LLM phù hợp để trích xuất thông tin, tóm tắt, chuẩn hóa cấu trúc và tạo bản nháp EMR.

Rule-based không phù hợp vì bệnh nhân có nhiều cách mô tả triệu chứng khác nhau, còn bác sĩ cũng có nhiều phong cách trao đổi khác nhau. Agentic Loop cũng chưa phù hợp ở giai đoạn đầu vì không nên để AI tự quyết định trong ngữ cảnh y tế.

### Luận điểm 3 — Rủi ro được kiểm soát bằng Human-in-the-loop

AI không được phép tự chẩn đoán, tự kê đơn, tự thêm xét nghiệm hoặc tự lưu hồ sơ. Toàn bộ output chỉ là bản nháp. Bác sĩ vẫn là người review, chỉnh sửa và xác nhận cuối cùng. Vì vậy, rủi ro y tế được giới hạn trong phạm vi có kiểm soát.

### Luận điểm 4 — Có thể prototype với chi phí hợp lý

Prototype ban đầu có thể triển khai bằng phạm vi nhỏ:

```text
Dữ liệu thử nghiệm:
- 10–20 transcript cuộc khám giả lập hoặc đã được ẩn danh
- 5–10 mẫu EMR chuẩn
- Checklist trường thông tin bắt buộc

Thành phần kỹ thuật:
- Speech-to-Text hoặc transcript nhập sẵn
- LLM prompt để trích xuất thông tin
- JSON output theo cấu trúc EMR
- Giao diện review đơn giản cho bác sĩ

Chi phí ước lượng:
- 1 AI Engineer xây prompt + JSON schema: 2–3 ngày
- 1 Backend/Full-stack Engineer làm UI prototype: 3–5 ngày
- 1 bác sĩ/medical reviewer kiểm tra output: 1–2 buổi
- Tổng thời gian prototype: khoảng 1–2 tuần
```

### Luận điểm 5 — Điều kiện để mở rộng sau prototype

Dự án chỉ nên mở rộng nếu đạt các ngưỡng sau:

```text
- Thời gian bác sĩ review bản nháp < 3 phút/lượt.
- ≥ 80% bản nháp được bác sĩ chấp nhận sau chỉnh sửa nhẹ.
- Không có trường hợp AI tự thêm chẩn đoán, đơn thuốc hoặc chỉ định không có trong transcript.
- 100% output có trạng thái rõ ràng:
  DRAFT_ONLY / NEEDS_DOCTOR_REVIEW / LOW_CONFIDENCE.
- Dữ liệu bệnh nhân được ẩn danh trong toàn bộ giai đoạn test.
```

---

##  Rủi ro còn lại và cách kiểm soát

| Rủi ro                                   | Mức độ           | Cách kiểm soát                                                                                                     |
| ---------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| Speech-to-Text nghe sai thuật ngữ y khoa | Cao              | Hiển thị transcript cho bác sĩ kiểm tra, dùng confidence score, đánh dấu đoạn không rõ                             |
| LLM tự suy diễn chẩn đoán                | Cao              | Cấm suy diễn trong system prompt, bắt buộc output có trường `evidence_from_transcript`, bác sĩ duyệt trước khi lưu |
| Thiếu thông tin quan trọng trong EMR     | Trung bình - Cao | Dùng checklist trường bắt buộc và trường `missing_fields`                                                          |
| Lộ dữ liệu bệnh nhân                     | Cao              | Chỉ test bằng dữ liệu ẩn danh, không đưa tên, SĐT, CCCD, mã bệnh nhân vào prompt                                   |
| Bác sĩ không muốn đổi workflow           | Trung bình       | Thiết kế UI review nhanh, đo thời gian thực tế, lấy feedback sau mỗi lượt test                                     |
| Chi phí tích hợp EMR thật cao            | Trung bình       | Giai đoạn đầu chỉ prototype độc lập, chưa ghi trực tiếp vào hệ thống EMR                                           |

---

## 4.5. Kết luận Evaluate

Dự án được đánh giá **GO cho giai đoạn prototype hẹp** vì bài toán có bottleneck rõ ràng, metric đo được, tác vụ phù hợp với LLM và rủi ro có thể kiểm soát bằng Human-in-the-loop.

Tuy nhiên, do đây là bài toán y tế, hệ thống không được triển khai tự động toàn phần. AI chỉ đóng vai trò tạo bản nháp hồ sơ EMR. Bác sĩ vẫn là người kiểm soát cuối cùng trước khi dữ liệu được lưu chính thức vào hệ thống.

