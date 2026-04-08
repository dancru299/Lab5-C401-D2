# SPEC draft

## Track: B. Vinmec

## Thành viên nhóm
1. Vũ Duy Linh
2. Đậu Văn Quyền
3. Hoàng Ngọc Anh
4. Nguyễn Hoàng Việt
5. Nguyễn Anh Đức

## Problem statement
Khách hàng/Bệnh nhân có nhu cầu tra cứu bảng giá dịch vụ y tế của hệ thống bệnh viện Vinmec (khám bệnh, nội trú, cận lâm sàng, xét nghiệm, sinh đẻ...). 
**Pain point:** 
- Người dùng thường không biết rõ mình cần khám chuyên khoa nào hay sử dụng dịch vụ gì để phù hợp với tình trạng thể chất (VD: vừa sinh đẻ xong thì cần gói khám/phục hồi nào, có triệu chứng ho sốt thì khám khoa nào).
- Bảng giá thường rất dài, phức tạp, chứa nhiều thuật ngữ chuyên môn khó hiểu đối với người không có chuyên môn.
- Tra cứu thủ công mất thời gian.
- Gọi tổng đài thì gặp tình trạng máy bận vào giờ cao điểm, nhắn tin fanpage thì tốn thời gian chờ đợi phản hồi.
**Giải pháp AI:** 
Xây dựng AI Chatbot đóng vai trò "Tư vấn viên Y tế ảo", hỗ trợ bệnh nhân bằng ngôn ngữ tự nhiên:
1. **Tư vấn & Gợi ý:** Phân tích triệu chứng hoặc tình trạng người dùng cung cấp (VD: "tôi vừa đẻ xong được 1 tháng") để tư vấn và gợi ý các dịch vụ/gói khám phù hợp nhất (VD: "Gói khám sức khỏe sau sinh").
2. **Tra cứu & Báo giá:** Hiểu các từ ngữ bình dân, đối chiếu danh mục chuẩn, từ đó báo giá và giải thích rõ các khoản phí (Bảo hiểm, phụ phí...) một cách nhanh chóng 24/7.

## Canvas draft

| | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Trả lời** | **User:** Bệnh nhân/Người nhà không biết nên khám gói nào và cần biết giá để chuẩn bị tài chính.<br>**Pain:** Không có chuyên môn y khoa để chọn dịch vụ; Tra cứu bảng giá mất thời gian, tổng đài bận.<br>**AI giúp gì:** AI phân tích triệu chứng/tình trạng để tư vấn gói khám phù hợp (VD: "vừa đẻ -> Gói khám sau sinh") và tự động trả về đúng mức giá, phụ phí liên quan. | **Rủi ro khi sai:** AI gợi ý sai gói dịch vụ chuyên khoa làm khách khám nhầm, trễ nải chữa bệnh, hoặc báo sai giá dẫn đến chuẩn bị thiếu tiền.<br>**Cách user nhận biết & xử lý:** Phát hiện sai khi đến viện gặp bác sĩ có chỉ định khác. Luôn kèm disclaimer "Gợi ý mang tính tham khảo ban đầu, tư vấn khám bệnh cuối cùng phụ thuộc vào bác sĩ", kèm nút "Gặp tư vấn viên". | **Cost:** Sử dụng mô hình phân tích ngữ nghĩa kết hợp RAG nhẹ trên PDF/DB (rẻ, ~ $0.01 - $0.02/query).<br>**Latency:** < 3-5s.<br>**Risk chính:** Triệu chứng user kể quá chung chung hoặc chồng chéo nhiều khoa (VD: Đau nửa đầu có thể do stress, do thần kinh, do tim mạch...) khiến AI hoặc gợi ý quá nhiều gói khám, hoặc hallucination tự bịa giá. |

**Auto hay aug?**
Augmentation — AI đóng vai trò tra cứu thay cho nhân viên và gợi ý thông tin nhanh. Các quyết định liên quan đến chẩn đoán, hẹn lịch và tính tiền cuối cùng vẫn do hệ thống chính thức và nhân viên tư vấn xác nhận.

**Learning signal:**
- Người dùng vote Like/Dislike (👍/👎) trên câu trả lời báo giá.
- Tỉ lệ người dùng bấm nút "Chuyển cho nhân viên CSKH" (Tỉ lệ này càng thấp chứng tỏ AI trả lời càng tốt).
- Dữ liệu correction từ nhân viên CSKH khi họ 'take over' (tiếp quản) cuộc gọi/chat đó.

## Hướng đi chính
- **Prototype:** Xây dựng hệ thống Chatbot 2 luồng (Tư vấn + Tra cứu). Khách hàng nhập triệu chứng/nhu cầu -> LLM phân tích và mapping tới Gói khám/Chuyên khoa phù hợp -> Query DB để lấy bảng giá -> Trả về Gợi ý dịch vụ + Báo giá chi tiết.
- **Eval:** 
  - *Precision của hệ gợi ý:* Tỉ lệ phân tích, chẩn đoán sơ bộ và gợi ý đúng chuyên khoa/gói theo danh mục (≥ 80%).
  - *Precision của báo giá:* Khớp ngoạm 100% tài liệu gốc dựa trên RAG, tuyệt đối không tự sinh ra số liệu.
- **Main failure mode:** 
  - Người dùng cung cấp triệu chứng mâu thuẫn hoặc quá chung chung khiến hệ thống gợi ý một lúc 5-6 gói dịch vụ khác nhau gây loãng thông tin.
  - Tên dịch vụ, thuật ngữ bệnh lý gõ sai chính tả nặng gãy chuỗi truy xuất RAG. 

## ROI — 3 kịch bản

| Kịch bản | Mô tả | Lợi ích ước tính |
|----------|-------|-----------------|
| **Tốt nhất (Best case)** | Chatbot giải quyết được 70% yêu cầu tra cứu giá & tư vấn gói khám. Giảm tải mạnh cho tổng đài và fanpage. | Giảm ~70% chi phí nhân sự CSKH phục vụ tra cứu giá; Trả lời 24/7 không cần OT; Tăng trải nghiệm khách hàng, giảm thời gian chờ từ 10 phút → < 30 giây. |
| **Trung bình (Base case)** | Chatbot đúng ~50% trường hợp. Phần còn lại escalate sang nhân viên. | Giảm ~40-50% tải tổng đài; Cost RAG/LLM thấp (~$0.01-0.02/query) so với lương 1 nhân viên CSKH (~15-20 triệu VNĐ/tháng cho ~2.000-3.000 query/tháng). Break-even rõ ràng. |
| **Tệ nhất (Worst case)** | Chatbot hallucination báo sai giá nhiều, khách hàng phàn nàn. Phải tắt hoặc rollback. | Mất niềm tin khách hàng; Chi phí fix model + PR xử lý khủng hoảng; Cần thêm 1 vòng human review trước khi re-deploy. |

**Kết luận ROI:** Ngay ở base case, chi phí vận hành chatbot (hosting + API calls) thấp hơn đáng kể so với chi phí nhân sự phục vụ cùng lượng query. Việc xây guardrail chặt (không tự bịa giá, luôn có fallback "gặp tư vấn viên") là cách kiểm soát worst case.

## Phân công

*Dựa theo 6 phần chính của SPEC (Canvas, User Stories 4 paths, Eval metrics, Failure modes, ROI, Mini AI spec):*

- **Vũ Duy Linh:** Chịu trách nhiệm hoàn thiện **Problem Statement + Canvas** & Lên khung **Mini AI spec** (Cấu trúc giải pháp hệ thống: Nhận diện Intent, Gợi ý dựa trên Triệu chứng, Trích xuất bảng giá qua RAG).
- **Đậu Văn Quyền:** Phân tích **User stories 4 paths** (Kịch bản khi AI tư vấn đúng, AI gợi ý lan man quá nhiều gói (không chắc), AI chẩn đoán sai khoa trầm trọng, và khi User mất niềm tin).
- **Hoàng Ngọc Anh:** Xác định các **Eval metrics** (Chỉ số đo lường độ phân loại intent và trích xuất mức giá) và phân tích **ROI** (Đánh đổi chi phí/lợi ích giữa hệ thống cũ và hệ thống AI mới).
- **Nguyễn Hoàng Việt:** Phân tích chi tiết **Failure modes** (Trường hợp AI fail như gợi ý khám lung tung do "bài toán đa triệu chứng") và đề xuất các fallback, guardrails để giải quyết.
- **Nguyễn Anh Đức:** Phụ trách **Prototype research & Prompt test** (Viết prompt nhận diện trạng thái lâm sàng cơ bản của bệnh nhân và test thử trên một bộ mẫu Bảng giá Vinmec).
