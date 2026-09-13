# 📊 BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 — SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Nguyễn Thọ Đạt
> **Mã Sinh Viên / Mã Học viên:** 2A202602484  
> **Chủ đề Lựa chọn:** Trợ lý Học vụ & Tra cứu Lịch thi VinUni: Tra cứu điểm GPA, lịch thi và đặt lịch tư vấn học vụ với Cố vấn.  

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm |
| :--- | :---: | :--- |
| **1. Multi-step Reasoning** | 4 / 5 | Bài toán đòi hỏi chuỗi suy luận liên tiếp: tra cứu hồ sơ học vụ sinh viên (điểm GPA, trạng thái) ➔ nhận diện giảng viên cố vấn học thuật phụ trách ➔ tiến hành lập lịch hẹn tư vấn học vụ tương ứng với cố vấn đó. |
| **2. Tool Interaction** | 5 / 5 | Hệ thống bắt buộc kết nối thông qua giao thức MCP (Model Context Protocol) với các công cụ tra cứu cơ sở dữ liệu học vụ nội bộ (`academic_query`) và hệ thống đặt lịch (`schedule_appointment`). LLM thuần túy không thể tự có dữ liệu thời gian thực này. |
| **3. Dynamic Decision** | 4 / 5 | Quyết định bước tiếp theo phụ thuộc trực tiếp vào kết quả quan sát (Observation) từ MCP Server: Nếu tra cứu sinh viên thành công thì mới lấy thông tin cố vấn để đặt lịch; nếu trả về trạng thái `NOT_FOUND` (như TC05) thì dừng xử lý và phản hồi lịch sự, chống ảo giác (Anti-Hallucination). |
| **4. Long Horizon Goal** | 4 / 5 | Hệ thống phải duy trì mục tiêu tổng thể xuyên suốt vòng lặp ReAct (`Thought -> Action -> Observation -> Final Answer`), đảm bảo hoàn thành trọn vẹn cả hai yêu cầu (vừa tra cứu thông tin vừa đặt lịch hẹn) mà không bị đứt đoạn ngữ cảnh. |
| **TỔNG ĐIỂM AGENTIC FIT** | **17 / 20** | *Nếu tổng điểm > 12/20: Bài toán rất phù hợp triển khai Agentic System.* |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

> ⚠️ **YÊU CẦU NGHIỆM THU:** Mở tệp `.env` điền `GEMINI_API_KEY` (hoặc `OPENAI_API_KEY`) để kết nối LLM thật trước khi thực thi `python src/app.py --all`. Bài nộp chỉ dùng Mock Offline Provider sẽ không đạt điểm nghiệm thực tế.

Dán 1 đoạn trích xuất log tiêu biểu từ file `docs/trace_waterfall.json` sinh ra từ phản hồi LLM API thật:

```json
[
  {
    "step": 1,
    "query": "Hãy tra cứu thông tin học vụ của sinh viên SV2026001.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "academic_query",
    "arguments": {
      "student_id": "SV2026001"
    },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026001",
      "data": {
        "full_name": "Nguyễn Văn An",
        "class": "AI-K4",
        "gpa": 3.85,
        "email": "an.nv@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "PGS.TS Nguyễn Văn A"
      }
    },
    "latency_ms": 4469.05
  },
  {
    "step": 2,
    "query": "Hãy tra cứu thông tin học vụ của sinh viên SV2026001.",
    "action_type": "FINAL_ANSWER",
    "thought": "Gemini phản hồi trực tiếp bằng văn bản (không cần gọi công cụ).",
    "output": "Dưới đây là thông tin học vụ của sinh viên **SV2026001**:\n\n* **Họ và tên:** Nguyễn Văn An\n* **Mã sinh viên:** SV2026001\n* **Lớp:** AI-K4\n* **Điểm trung bình tích lũy (GPA):** 3.85\n* **Trạng thái học tập:** Đang học\n* **Email:** an.nv@vinuni.edu.vn\n* **Cố vấn học thuật:** PGS.TS Nguyễn Văn A\n\nNếu bạn cần hỗ trợ thêm thông tin hoặc muốn đặt lịch hẹn tư vấn với Cố vấn học thuật, vui lòng cho tôi biết!",
    "latency_ms": 8440.6
  },
  {
    "step": 1,
    "query": "Hãy đặt lịch hẹn với cố vấn học tập cho sinh viên SV2026001 vào lúc 10:00 ngày 15/09/2026 với PGS.TS Nguyễn Văn A.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "schedule_appointment",
    "arguments": {
      "datetime_str": "10:00 15/09/2026",
      "student_id": "SV2026001",
      "advisor_name": "PGS.TS Nguyễn Văn A"
    },
    "observation": {
      "status": "SUCCESS",
      "booking_id": "BK-SV2026001-99",
      "student_id": "SV2026001",
      "datetime": "10:00 15/09/2026",
      "advisor": "PGS.TS Nguyễn Văn A",
      "message": "Đặt lịch thành công cho sinh viên SV2026001 với PGS.TS Nguyễn Văn A vào lúc 10:00 15/09/2026."
    },
    "latency_ms": 3530.3
  }
]
```

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã điền API Key thật trong `.env` và xác nhận Agent chạy mượt mà trên LLM API thật (Google Gemini).
- **Tổng số Test Cases đã chạy thành công:** 5 / 5 test cases (TC01: Direct Query, TC02: Single Tool Query, TC03: Appointment Booking, TC04: Multi-step Reasoning, TC05: Edge Case Handling).
- **Số lượt gọi Tool qua MCP Server chính xác:** 4 lượt gọi (`academic_query` cho TC02 & TC05, `schedule_appointment` cho TC03 & TC04).
- **Kết quả đẩy Repo nộp bài:** [x] Đã hoàn thiện toàn bộ mã nguồn, cấu hình và báo cáo nghiệm thu, sẵn sàng Commit và Push lên GitHub cá nhân.

---

> ✅ **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân của bạn và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3!
