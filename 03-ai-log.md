


# 📝 AI Log & Reflection

## 1. Tôi đã dùng AI để làm gì?

Trong buổi lab này, tôi đã sử dụng AI như một **thought-partner** để hỗ trợ quá trình tìm kiếm, phân tích và hoàn thiện bài toán AI Product Scoping cho Vin Smart Future.

Đầu tiên, tôi dùng AI để brainstorm các pain point vận hành trong hệ sinh thái Vingroup, đặc biệt ở các mảng Vinmec, Xanh SM và Vinhomes. AI giúp tôi mở rộng góc nhìn từ một ý tưởng ban đầu thành nhiều bài toán cụ thể hơn, ví dụ: tự động ghi chép hồ sơ bệnh án sau khám tại Vinmec, dự đoán cụm cầu cho tài xế Xanh SM, và phân loại phản hồi 1–2 sao của cư dân Vinhomes.

Sau đó, tôi dùng AI để hỗ trợ viết **Quick Problem Cards** cho 3 bài toán. AI giúp tôi cấu trúc lại mỗi card theo đúng format yêu cầu: actor, workflow thủ công, bottleneck, bước AI có thể tham gia, metric đo thành công và quick architecture.

Ở phần nhóm, tôi tiếp tục dùng AI để phát triển bài toán Vinmec thành **Deep-Dive Report**. AI giúp tôi viết rõ hơn phần Problem Statement 6-field, bao gồm:

- Actor / Operator
- Current Workflow
- Bottleneck
- Business Impact
- Success Metric
- Operational Boundary

AI cũng hỗ trợ tôi vẽ Future-State Flow bằng text-diagram, xác định AI Fit là **LLM Feature**, bổ sung Human-in-the-loop và viết các tình huống Fallback khi hệ thống AI trả về kết quả không chắc chắn.

Ngoài ra, tôi dùng AI để tạo sơ đồ workflow hiện tại cho file `04-workflow-diagram`, sau đó yêu cầu chỉnh lại khi bản đầu bị lỗi font. AI đã tạo lại sơ đồ không dùng emoji để tránh lỗi hiển thị và đảm bảo vẫn có đủ các thành phần: bước tuần tự, handoff, thời gian, tổng thời gian và bottleneck.

---

## 2. AI đã sai hoặc chưa tốt ở điểm nào?

Một điểm AI làm chưa tốt là ban đầu AI có xu hướng viết phần giải pháp quá “đẹp” và hơi lý tưởng hóa, trong khi bài toán Vinmec thuộc lĩnh vực y tế có rủi ro cao. Nếu không kiểm soát kỹ, AI có thể mô tả hệ thống như thể có thể tự động tạo và lưu hồ sơ bệnh án hoàn chỉnh mà không nhấn mạnh đủ vai trò phê duyệt của bác sĩ.

Ví dụ, trong bản nháp ban đầu, phần AI Solution chủ yếu tập trung vào việc dùng Voice-to-Text và LLM để tạo bản nháp EMR, nhưng chưa nhấn mạnh đủ rằng AI **không được tự chẩn đoán, không được tự kê đơn, không được tự thêm chỉ định xét nghiệm và không được tự lưu hồ sơ chính thức**.

Một điểm chưa tốt khác là khi tạo sơ đồ workflow bằng hình ảnh, bản đầu tiên dùng emoji như 🔴, 🔄, ⏱ nên có thể bị lỗi font hoặc hiển thị không đúng khi xuất sang file. Điều này làm sơ đồ chưa phù hợp để nộp trực tiếp, dù nội dung logic vẫn đúng.

Ngoài ra, AI cũng có xu hướng đưa ra các con số ước tính như “tiết kiệm hàng trăm giờ bác sĩ/ngày” hoặc “~800 giờ/ngày” mà chưa có dữ liệu thật để chứng minh. Những con số này có thể dùng như giả định scoping ban đầu, nhưng nếu đưa vào báo cáo chính thức thì cần ghi rõ là ước tính hoặc cần baseline thực tế từ Vinmec.

---

## 3. Tôi đã sửa đổi và kiểm soát lại như thế nào?

Tôi đã điều chỉnh lại nội dung để bài làm bám sát yêu cầu của lab hơn, đặc biệt là phần `02-deep-dive-report.md`.

Thứ nhất, tôi yêu cầu AI sửa lại báo cáo để chỉ tập trung vào các phần bắt buộc: quyết định lựa chọn bài toán, Problem Statement 6-field, Future-State Flow & AI Fit, Human-in-the-loop, Fallback và Evaluate. Việc này giúp báo cáo không bị lan man và đúng rubric hơn.

Thứ hai, tôi bổ sung Operational Boundary rõ ràng cho bài toán Vinmec. Cụ thể, AI chỉ được phép:

- Chuyển lời nói thành transcript.
- Tóm tắt nội dung cuộc khám.
- Trích xuất thông tin đã xuất hiện trong transcript.
- Sinh bản nháp EMR có cấu trúc.
- Đánh dấu phần không chắc chắn để bác sĩ kiểm tra.

AI tuyệt đối không được:

- Tự đưa ra chẩn đoán mới.
- Tự kê đơn thuốc.
- Tự thêm chỉ định xét nghiệm.
- Tự thay đổi kế hoạch điều trị.
- Tự lưu hồ sơ chính thức.
- Tự gửi lời khuyên y tế trực tiếp cho bệnh nhân.

Thứ ba, tôi yêu cầu thêm phần Human-in-the-loop rõ ràng. Trong quy trình tương lai, bác sĩ bắt buộc phải review, chỉnh sửa và xác nhận trước khi hồ sơ được lưu vào EMR. Điều này giúp giảm rủi ro khi AI nghe sai transcript hoặc suy luận sai nội dung y khoa.

Thứ tư, tôi bổ sung Fallback Plan cho các tình huống lỗi như:

- Transcript không rõ.
- LLM không chắc chắn.
- Nội dung vượt ranh giới.
- Ca bệnh nhạy cảm hoặc rủi ro cao.
- Speech-to-Text hoặc LLM bị lỗi.

Cuối cùng, với lỗi sơ đồ workflow, tôi yêu cầu AI vẽ lại phiên bản không dùng emoji, thay bằng nhãn chữ rõ ràng như `BOTTLENECK` và `HANDOFF`. Cách này giúp file ổn định hơn khi xuất sang `.png`, `.pdf` hoặc `.pptx`.

---

## 4. Bài học rút ra

Qua buổi lab này, tôi nhận ra AI rất hữu ích trong việc brainstorm, cấu trúc hóa ý tưởng và biến một pain point ban đầu thành một báo cáo có format rõ ràng. Tuy nhiên, AI không nên được dùng như nguồn quyết định cuối cùng, đặc biệt với các bài toán có rủi ro cao như y tế.

Điểm quan trọng nhất là phải luôn kiểm tra lại:

- AI có đang vượt quá phạm vi được phép không?
- Metric có thực tế và đo được không?
- Có Human-in-the-loop chưa?
- Có Fallback khi AI sai chưa?
- Có phân biệt rõ giữa bản nháp AI và quyết định chính thức của con người chưa?

Đối với bài toán Vinmec, tôi học được rằng một giải pháp AI tốt không nhất thiết phải là hệ thống tự động hoàn toàn. Trong bối cảnh y tế, giải pháp phù hợp hơn là **LLM Feature có ranh giới chặt chẽ**, trong đó AI hỗ trợ giảm tải hành chính, còn bác sĩ vẫn giữ quyền kiểm soát cuối cùng.

