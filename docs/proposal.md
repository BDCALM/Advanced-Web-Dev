# Đề xuất dự án: TrọThật – Chi phí thật của phòng trọ

**CSC13114 · PA#1** · **Repository:** [https://github.com/BDCALM/Advanced-Web-Dev]

| Thành viên | MSSV | Vai trò chính |
|---|---|---|
| Nguyễn Lê Thế Vinh | 23120190 | Frontend, người dùng |
| Phạm Quang Vinh | 23120202 | Backend, bản đồ |
| Nguyễn Văn Bình Dương | 23120242 | Dữ liệu, LLM |

## 1. Problem and users

- **Người dùng:** tân sinh viên từ tỉnh vào TP.HCM tháng 8–9, tìm phòng dưới 3 triệu/tháng gần trường qua các nhóm Facebook tìm trọ.
- **Vấn đề:** Tân sinh viên từ tỉnh tìm phòng dưới 3 triệu qua nhóm Facebook không biết chi phí thật mỗi tháng, vì các khoản phí nằm rải rác hoặc bị bỏ ngỏ trong bài đăng.
- **Hiện nay:** đọc hàng trăm bài, gọi 10–20 chủ trọ hỏi "điện nước tính sao, cọc mấy tháng", ghi chú tay, mất khoảng 5–7 giờ (ước tính, kiểm chứng qua phỏng vấn ở CP1).

## 2. The LLM feature, and the cost of it being wrong

**Tính năng:** người dùng dán chữ bài đăng hoặc ảnh chụp (ảnh qua bước OCR riêng, người dùng sửa chữ trước). Với **8 khoản cố định** (phòng, điện, nước, wifi, rác, gửi xe, dịch vụ, cọc), LLM trả về số tiền, đơn vị, nhãn *Rõ / Chưa rõ / Không nhắc đến* và **nguyên văn câu gốc** làm bằng chứng. Tổng chi phí và khoảng cách do code tính.

**Khi LLM sai:**
- **Ai bị hại:** tân sinh viên tin thẻ kết quả và thuê phòng tưởng rẻ.
- **Thiệt hại bao nhiêu:** chi phí thật cao hơn khoảng 500.000đ/tháng, tức khoảng 6.000.000đ cho hợp đồng 12 tháng.
- **Hoàn tác được không:** gần như không. Rời phòng sớm thì mất tiền cọc, thường 1–2 tháng tiền phòng.

| Loại lỗi | Hậu quả | Mức độ | Mục tiêu (chưa đo) |
|---|---|---|---|
| Bịa khoản phí hoặc mức giá | Phòng trông rẻ hơn thật | Nghiêm trọng | ≤ 1% số khoản |
| Bỏ sót khoản có trong bài | Tổng thấp hơn thật | Nghiêm trọng | Tìm đủ ≥ 90% |
| Trích đúng câu, sai số tiền | Tổng sai | Nghiêm trọng | ≤ 2% số khoản |
| Báo "chưa rõ" thừa | Thêm một cuộc gọi | Nhẹ | Theo dõi |

**Lớp kiểm tra bằng code (không dùng AI):** code soát từng khoản LLM trả về. Gặp một trong ba trường hợp sau thì app bỏ con số của LLM và gắn nhãn *Chưa rõ – cần hỏi chủ trọ*:
- câu trích LLM đưa ra không có nguyên văn trong bài (LLM bịa);
- số tiền LLM đưa ra không có trong câu trích (LLM đọc sai số);
- bài có từ khóa ("điện", "xe", "cọc"…) nhưng LLM báo *Không nhắc đến* (LLM bỏ sót).

Khi còn khoản chưa rõ, app hiện *"Tối thiểu 2.800.000đ, còn 2 khoản chưa rõ: điện, gửi xe"* thay cho một con số tổng.

**Làm sao biết LLM sai:** 150 bài đăng thật do nhóm gán nhãn (50 dev, 100 test khóa trước khi viết prompt, 20 bài hai người gán nhãn để đo đồng thuận) và nút **"Báo sai"** cho người dùng.

## 3. Scope: in and out

**Làm:** nhập chữ hoặc ảnh (OCR, sửa chữ); che số điện thoại trước khi lưu và gửi LLM; trích xuất 8 khoản và 3 lớp kiểm tra; chi phí tối thiểu kèm giả định; khoảng cách tới trường và bản đồ; so sánh 2–4 phòng; nút "Báo sai"; PWA; chỉ TP.HCM.

**Không làm:** chatbot; tự crawl Facebook; thông báo phòng mới; đánh giá chủ trọ; so sánh giá khu vực (thiếu dữ liệu đã xác minh); chủ trọ đăng bài; thanh toán online; tỉnh ngoài TP.HCM.

## 4. Plan and ownership

| CP | Ngày | Chủ trì | Việc của từng người | Kết quả |
|---|---|---|---|---|
| 1 | 13/10 | Dương | **Dương:** gom, gán nhãn 150 bài, khóa bộ test. **T.Vinh:** phỏng vấn 5 tân sinh viên. **Q.Vinh:** tạo repo, thử Goong và OCR. | Commit bộ test; ghi chú phỏng vấn |
| 2 | 20/10 | Q.Vinh | **Q.Vinh:** NestJS, PostGIS, che số điện thoại. **T.Vinh:** mockup, gán nhãn độc lập 20 bài. **Dương:** đo đồng thuận, prompt OCR. | API lưu bài; mockup |
| 3 | 27/10 | Dương | **Dương:** prompt trích xuất, 3 lớp kiểm tra, chạy bộ test. **Q.Vinh:** API chi phí và khoảng cách. | Báo cáo 4 loại lỗi |
| 4 | 03/11 | T.Vinh | **T.Vinh:** PWA dán bài, sửa chữ OCR, thẻ kết quả, nút "Báo sai". **Dương:** chỉnh prompt. | Luồng chạy trên điện thoại |
| 5 | 10/11 | Q.Vinh | **Q.Vinh:** bản đồ, so sánh phòng, deploy thử. **Dương:** chạy bộ test lần 2. **T.Vinh:** test E2E. | Link bản thử |
| 6 | 17/11 | T.Vinh | **T.Vinh:** 5 sinh viên dùng thật. **Dương:** báo cáo độ chính xác cuối. **Q.Vinh:** deploy chính thức. | Link live; báo cáo |

## 5. Risks

**Sinh viên không chịu dán bài vào app.** *Tuần này (T.Vinh):* 5 tân sinh viên gửi bài qua Zalo, nhóm trả kết quả làm tay theo định dạng thẻ, xem họ có gửi bài thứ hai không; nếu không, chuyển sang chia sẻ ảnh thẳng từ điện thoại.

**LLM bỏ sót hoặc bịa khoản phí.** *Tuần này (Dương):* gán nhãn 20 bài có ca khó ("đ/n", "3k5", nhiều phòng, giá theo người), chạy prompt đầu tiên, đo tỉ lệ bỏ sót và bịa; bỏ sót trên 10% thì làm kiểm tra từ khóa trước giao diện.

## 6. Technology choices

- **LLM `gemini-3.5-flash-lite` (Google):** đọc được ảnh nên dùng cho cả OCR và trích xuất. $0.30/1M token vào, $2.50/1M ra ([giá](https://ai.google.dev/gemini-api/docs/pricing)): khoảng $0.0017/bài, OCR khoảng $0.002/ảnh, cả học kỳ (khoảng 3.000 lượt) dưới $10.
- **Goong:** tìm địa chỉ hẻm Việt Nam tốt hơn OSM, khoảng $0.006/phòng có cache ([giá](https://goong.io/pricing-plans/)). **MapLibre:** bản đồ miễn phí.
- **NestJS + PostgreSQL/PostGIS:** tính khoảng cách ngay trong database. **React (Vite) PWA:** sinh viên dùng điện thoại, cài như app.
