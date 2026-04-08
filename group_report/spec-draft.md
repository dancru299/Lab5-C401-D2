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

---

## Mini AI Spec — Cả team

**Sản phẩm:** Vinmec AI Symptom Triage
**Dành cho:** Bệnh nhân chưa biết chọn chuyên khoa (~30-40% lượt đặt lịch).

### Câu chuyện sản phẩm

> Chị Lan, 32 tuổi, vừa sinh con được 6 tuần. Chị muốn đặt lịch khám hậu sản nhưng vào trang Vinmec thấy hàng chục gói khám, hàng chục chuyên khoa và một bảng giá dài hàng trăm dòng. Chị gọi tổng đài — máy bận. Nhắn tin fanpage — 2 tiếng sau mới có người trả lời.
>
> Nếu có **Vinmec AI Chatbot**, chị chỉ cần gõ: *"Tôi vừa sinh xong 6 tuần, muốn khám lại"* — Chatbot phân tích, gợi ý ngay "Gói khám sức khỏe Sau sinh 6 tuần", kèm giá tham khảo và nút đặt lịch. Toàn bộ trong vòng dưới 30 giây, 24/7.

### Input / Output

| | Mô tả | Ví dụ |
|---|-------|-------|
| **Input** | Mô tả triệu chứng hoặc tình trạng sức khỏe bằng ngôn ngữ tự nhiên (tiếng Việt, tiếng lóng, từ bình dân) | *"Tôi bị đau đầu liên tục 3 ngày, chóng mặt"* / *"vừa đẻ mổ xong muốn tái khám"* |
| **Output** | Top 1-3 gợi ý chuyên khoa/gói khám phù hợp + mức giá tham khảo + disclaimer + nút hành động | *"Gợi ý: Khoa Ngoại Thần kinh (khám triệu chứng đau đầu kéo dài). Giá tham khảo: 350.000đ – 500.000đ/lượt. [Đặt lịch] [Gặp tư vấn viên]"* |

### Luồng xử lý (Flow)

```
User nhập triệu chứng / nhu cầu
        ↓
[1] Intent Detection — LLM nhận diện: 
    (a) Hỏi giá dịch vụ cụ thể  
    (b) Mô tả triệu chứng cần tư vấn
        ↓
[2a] Tra cứu bảng giá (RAG)        [2b] Symptom → Specialty Mapping (LLM)
     → Trích xuất giá từ DB              → Gợi ý Top 1-3 chuyên khoa
        ↓                                         ↓
[3] Tổng hợp kết quả: Gói khám + Giá tham khảo + Disclaimer
        ↓
[4] Hiện nút: [Đặt lịch ngay] / [Gặp tư vấn viên]
        ↓ (nếu user bấm "Gặp tư vấn viên")
[5] Escalate sang nhân viên CSKH (human handoff)
```

### Guardrails (Chốt chặn an toàn)

| # | Quy tắc | Lý do |
|---|---------|-------|
| 1 | **Không được đưa ra chẩn đoán bệnh** — chỉ gợi ý chuyên khoa/gói khám | Tránh vi phạm pháp lý y tế |
| 2 | **Không tự bịa số tiền** — giá phải 100% lấy từ tài liệu DB | Tránh gây hiểu lầm tài chính |
| 3 | **Luôn kèm disclaimer** rõ ràng trên mỗi câu trả lời | Bảo vệ bệnh viện & user |
| 4 | **Luôn hiện nút "Gặp tư vấn viên"** — không bao giờ để user bí | Đảm bảo có fallback human |
| 5 | **Không xử lý tình huống khẩn cấp y tế** — nếu phát hiện từ khóa cấp cứu ("ngất xỉu", "khó thở dữ dội",...) thì chuyển thẳng sang hotline cấp cứu | An toàn tính mạng ưu tiên số 1 |

### Chỉ số thành công (Success Metrics)

| Metric | Mục tiêu | Đo bằng cách nào |
|--------|----------|-----------------|
| Tỉ lệ gợi ý đúng chuyên khoa | ≥ 80% so với danh mục chuẩn | So sánh output AI vs. danh mục của bác sĩ trên 100 test case |
| Độ chính xác báo giá | 100% khớp tài liệu gốc (zero hallucination) | Human review ngẫu nhiên 20 query/tuần |
| Tỉ lệ escalate sang CSKH | ≤ 30% (chatbot tự xử lý được ≥ 70%) | Log hệ thống |
| Thời gian phản hồi | < 5 giây/query | Monitoring API latency |
