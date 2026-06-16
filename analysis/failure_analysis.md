# Báo cáo Phân tích Thất bại (Failure Analysis Report)

## 1. Tổng quan Benchmark

* **Tổng số cases:** 50
* **Tỉ lệ Pass/Fail:** 50/0
* **Điểm RAGAS trung bình:**

  * Faithfulness: 0.90
  * Relevancy: 0.80
* **Điểm LLM-Judge trung bình:** 4.5 / 5.0
* **Retrieval Metrics:**

  * Hit Rate: 1.00
  * MRR: 0.50
* **Agreement Rate:** 0.80 (80%)
* **Regression Decision:** APPROVE
* **Execution Time:** 5.007 giây
* **Estimated Cost:** 0.002625 USD

## 2. Phân nhóm lỗi (Failure Clustering)

| Nhóm lỗi                | Mức độ rủi ro | Nguyên nhân dự kiến                                                |
| ----------------------- | ------------- | ------------------------------------------------------------------ |
| Prompt Injection        | Trung bình    | Agent có thể bị ảnh hưởng bởi chỉ dẫn độc hại nếu thiếu guardrails |
| Goal Hijacking          | Trung bình    | Người dùng yêu cầu thực hiện nhiệm vụ ngoài phạm vi evaluation     |
| Out-of-Context          | Cao           | Agent có thể hallucinate nếu không được hướng dẫn từ chối trả lời  |
| Ambiguous Questions     | Trung bình    | Thiếu cơ chế yêu cầu người dùng làm rõ câu hỏi                     |
| Judge Conflict          | Thấp          | Các Judge có thể đánh giá khác nhau đối với các trường hợp mơ hồ   |
| Cost Inefficiency       | Thấp          | Agent có thể sử dụng quá nhiều token cho các tác vụ đơn giản       |
| Conflicting Information | Trung bình    | Khó xử lý khi nhiều nguồn cung cấp thông tin trái ngược nhau       |

## 3. Phân tích 5 Whys

### Case #1: Prompt Injection

1. **Symptom:** Agent bị yêu cầu bỏ qua hướng dẫn ban đầu và luôn trả về kết quả PASS.
2. **Why 1:** Prompt từ người dùng chứa chỉ dẫn độc hại.
3. **Why 2:** Agent chưa có cơ chế ưu tiên system instructions.
4. **Why 3:** Không có guardrails chuyên biệt chống prompt injection.
5. **Why 4:** Bộ test ban đầu tập trung vào câu hỏi thông thường.
6. **Root Cause:** Thiếu chiến lược phòng thủ đối với adversarial prompts.

---

### Case #2: Out-of-Context Question

1. **Symptom:** Agent được hỏi về thông tin không tồn tại trong tài liệu.
2. **Why 1:** Retriever không tìm thấy evidence phù hợp.
3. **Why 2:** Agent có xu hướng cố gắng đưa ra câu trả lời.
4. **Why 3:** Prompt chưa nhấn mạnh việc từ chối khi thiếu thông tin.
5. **Why 4:** Chưa có metric chuyên biệt đánh giá hallucination.
6. **Root Cause:** Thiếu cơ chế "I don't know" khi context không đầy đủ.

---

### Case #3: Judge Conflict

1. **Symptom:** Các Judge có thể đưa ra đánh giá khác nhau cho cùng một câu trả lời.
2. **Why 1:** Tiêu chí đánh giá giữa các Judge không hoàn toàn giống nhau.
3. **Why 2:** Một số câu trả lời nằm ở vùng chấp nhận được thay vì hoàn toàn đúng hoặc sai.
4. **Why 3:** Chưa có Judge thứ ba để phá vỡ thế cân bằng.
5. **Why 4:** Consensus logic hiện tại còn đơn giản.
6. **Root Cause:** Multi-Judge framework cần được hiệu chỉnh sâu hơn.

## 4. Kế hoạch cải tiến (Action Plan)

* [ ] Tích hợp Judge Models thực tế (ví dụ GPT và Claude thông qua OpenRouter).
* [ ] Bổ sung guardrails chống Prompt Injection và Goal Hijacking.
* [ ] Cập nhật System Prompt để nhấn mạnh việc chỉ trả lời dựa trên context.
* [ ] Bổ sung cơ chế từ chối trả lời khi thiếu bằng chứng ("I don't know").
* [ ] Thêm bước Reranking nhằm cải thiện chất lượng Retrieval.
* [ ] Mở rộng Golden Dataset với nhiều adversarial và multi-turn cases hơn.
* [ ] Cải thiện Consensus Engine bằng Judge thứ ba khi phát hiện xung đột.
