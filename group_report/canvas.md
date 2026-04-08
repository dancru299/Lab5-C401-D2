# AI Product Canvas — template

Điền Canvas cho product AI của nhóm. Mỗi ô có câu hỏi guide — trả lời trực tiếp, xóa phần in nghiêng khi điền.

---

## Canvas

|   | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Câu hỏi guide** | User nào? Pain gì? AI giải quyết gì mà cách hiện tại không giải được? | Khi AI sai thì user bị ảnh hưởng thế nào? User biết AI sai bằng cách nào? User sửa bằng cách nào? | Cost bao nhiêu/request? Latency bao lâu? Risk chính là gì? |
| **Trả lời** | **User:** Người dùng ví MoMo thường xuyên, có ý thức quản lý tài chính nhưng lười ghi chép tay.<br>**Pain:** Nhập tay từng khoản chi tiêu rất phiền phức, dễ quên. Các app quản lý tài chính truyền thống phải tự phân loại, tự filter, khó hình dung tổng thể đang "đốt tiền" vào đâu nhiều nhất.<br>**AI giải quyết gì:** Tự động đọc hiểu ngữ cảnh giao dịch (VD: QR tại "Cơm Tấm Bụi" → tự tag "Ăn uống"). Cho phép user hỏi bằng ngôn ngữ tự nhiên để tổng hợp chi tiêu. | **Khi AI sai:** Moni classify nhầm loại chi tiêu làm user nhầm số tiền đã chi, kế hoạch ngân sách bị sai lệch.<br>**User phát hiện sai:** Đọc tổng kết tháng thấy số tiền vô lý, hoặc thấy nhãn (tag) dán sai ngữ cảnh giao dịch.<br>**User sửa bằng cách nào:** Vào chi tiết giao dịch → đổi category thủ công (3 bước). Chưa có cơ chế feedback nhanh ngay trong luồng chat. | **Cost:** Nếu chỉ ML classify: Rất rẻ (~$0.001/query). Nếu gọi LLM xử lý ngôn ngữ tự nhiên: ~vài chục đến vài trăm VNĐ/request, mật độ cao sẽ là bài toán chi phí lớn.<br>**Latency:** < 2s cho classify, < 5s cho NLP query.<br>**Risk chính:** Data Privacy — dữ liệu chi tiêu cá nhân rất nhạy cảm, rủi ro cao nếu bị rò rỉ hoặc dùng để train model mà không xin phép user. |

---

## Automation hay augmentation?

☐ Automation — AI làm thay, user không can thiệp
☑ Augmentation — AI gợi ý, user quyết định cuối cùng

Justify: Dữ liệu tài chính mang tính cá nhân và ngữ cảnh rất cao (ví dụ: chuyển khoản 500k cho bạn có thể là "Trả nợ", nhưng cũng có thể là "Nhờ mua đồ hộ" - AI không thể biết chắc 100% ngữ cảnh ngoài đời thực). Nếu để AI tự động hoàn toàn (Automation) mà không có cơ chế xác nhận/sửa lỗi dễ dàng, khi AI phân loại sai sẽ dẫn đến hỏng toàn bộ kế hoạch ngân sách của user. Do đó, AI chỉ nên đóng vai trò là "Trợ lý gợi ý" (Augmentation), dán sẵn các nhãn có xác suất đúng cao nhất, nhưng trao lại quyền "Edit/Duyệt" cuối cùng cho user một cách nhanh chóng ngay trong luồng chat.

---

## Learning signal

| # | Câu hỏi | Trả lời |
|---|---------|---------|
| 1 | User correction đi vào đâu? | **Personalization Rules:** Lưu thành luật riêng cho từng user (VD: "User này CK cho STK X thì luôn tag là Trả góp").<br>**Training Data Pipeline:** Correction trở thành label mới để fine-tune Classification model cho các giao dịch tương tự trong tương lai. |
| 2 | Product thu signal gì để biết tốt lên hay tệ đi? | **Explicit:** Tỷ lệ user ấn "Sửa nhãn" (Edit rate) / tổng giao dịch AI tự tag — tỷ lệ này giảm = AI đang tốt lên.<br>**Implicit:** Retention rate dùng Moni hàng tháng; Số tin nhắn chat/tuần trung bình. |
| 3 | Data thuộc loại nào? | ☑ **User-specific** (thói quen chi tiêu, tên người thân riêng của từng cá nhân)<br>☑ **Human-judgment** (nhãn sửa tay từ chính user đánh giá đúng/sai) |

**Có marginal value không?** (Model đã biết cái này chưa? Ai khác cũng thu được data này không?)
Có — data correction của user MoMo là **user-specific** nên chỉ MoMo mới thu được (người dùng không dùng MoMo thì MoMo không có data). Model language chung (GPT, Gemini) không có data này. Đây là lợi thế cạnh tranh bền vững của MoMo nếu xây được feedback loop tốt.

---

## Cách dùng

1. Điền Value trước — chưa rõ pain thì chưa điền Trust/Feasibility
2. Trust: trả lời 4 câu UX (đúng → sai → không chắc → user sửa)
3. Feasibility: ước lượng cost, không cần chính xác — order of magnitude đủ
4. Learning signal: nghĩ về vòng lặp dài hạn, không chỉ demo ngày mai
5. Đánh [?] cho chỗ chưa biết — Canvas là hypothesis, không phải đáp án

---

*AI Product Canvas — Ngày 5 — VinUni A20 — AI Thực Chiến · 2026*