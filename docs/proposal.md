**Đề xuất dự án: TrọThật – Chi phí thật của phòng trọ**  
**Nhóm ** **s** **inh viên thực hiện:**   
1. Nguyễn Lê Thế Vinh (MSSV: 23120190)  
2. Phạm Quang Vinh (MSSV: 23120202)  
3. Nguyễn Văn Bình Dương (MSSV: 23120242)  
**1. Problem and users**  
- **Người dùng:** Tân sinh viên từ các tỉnh chuyển vào TP.HCM trong giai đoạn tháng 8–9, có nhu cầu tìm phòng trọ dưới 3.000.000 VNĐ/tháng gần trường đại học thông qua các hội nhóm Facebook.  
- **Vấn đề:** Tân sinh viên từ tỉnh tìm phòng dưới 3 triệu qua nhóm Facebook không biết chi phí thật mỗi tháng, vì các khoản phí nằm rải rác hoặc bị bỏ ngỏ trong bài đăng.  
- **Hiện nay họ làm gì:** Đọc hàng trăm bài đăng nhập nhằng trên điện thoại, tự gọi điện cho khoảng 10-20 chủ trọ để hỏi cụ thể "điện nước tính sao, cọc mấy tháng, có phí quản lý không", và ghi chú thủ công ra sổ tay hoặc ứng dụng note trên điện thoại (ước tính mất 5-7 giờ cho mỗi lần lọc danh sách).  
**2. The LLM feature, and the cost of it being wrong**  
**Mô tả tính năng LLM:**  
   
 Người dùng dán nội dung văn bản của bài đăng tìm trọ (hoặc tải ảnh lên để chạy qua một bước OCR lấy chữ trước). LLM được giao một nhiệm vụ duy nhất: trích xuất các khoản phí (tiền phòng, điện, nước, wifi, rác, gửi xe, cọc). Mỗi khoản phí phải được gắn nhãn (Rõ ràng / Chưa rõ / Không nhắc đến) và bắt buộc phải trích xuất **nguyên văn câu gốc trong bài** để làm bằng chứng (Ground Truth).  
**Hậu quả khi LLM sai (Ai bị hại, thiệt hại bao nhiêu, có hoàn tác được không?):**  
- **Ai bị hại:** Tân sinh viên tin vào báo cáo của app và quyết định thuê lầm phòng.  
- **Thiệt hại bao nhiêu:** Chi phí thực tế mỗi tháng có thể dội lên khoảng 500.000đ. Nhân với hợp đồng 12 tháng, sinh viên mất oan khoảng 6.000.000đ.  
- **Hoàn tác được không:** Gần như không thể. Hủy hợp đồng sớm đồng nghĩa với việc mất trắng tiền cọc (từ 1 đến 2 tháng tiền phòng).  
**Bốn loại lỗi của LLM và mục tiêu kiểm soát:**  
| | | | |  
|-|-|-|-|  
| **Loại lỗi** | **Hậu quả** | **Mức độ** | **Mục tiêu (chưa đo)** |   
| **Bịa khoản phí hoặc mức giá** | Phòng trông rẻ hơn thực tế | Nghiêm trọng | Tối đa 1% |   
| **Bỏ sót khoản có trong bài** | Tổng chi phí thấp hơn thực tế | Nghiêm trọng | Tìm đủ ít nhất 90% khoản phí |   
| **Trích đúng câu nhưng sai số tiền/đơn vị** | Sai tổng chi phí | Nghiêm trọng | Tối đa 2% |   
| **Báo "chưa rõ" thừa** | Người dùng tốn thêm 1 cuộc gọi hỏi | Nhẹ | Theo dõi tần suất |   
   
**Thiết kế giảm thiểu rủi ro từ hệ thống (Bằng chứng kiểm tra bằng Code):**  
- Code sẽ so khớp chuỗi (string matching) câu trích xuất của LLM với văn bản gốc. Nếu không khớp, khoản phí đó bị loại và đánh dấu là "Không nhắc đến" để chặn lỗi LLM tự bịa số liệu.  
- **Giao diện an toàn:** Nếu có khoản phí bị thiếu, hệ thống tuyệt đối không hiển thị một con số tổng duy nhất. Giao diện sẽ hiển thị: *"Tối thiểu 2.800.000đ/tháng. Còn 2 khoản chưa rõ: Tiền điện, Gửi xe"*, kèm theo danh sách các câu hỏi gợi ý để sinh viên tự đi hỏi chủ trọ.  
**3. Scope: in and out**  
**In-scope (Sẽ làm trong học kỳ này):**  
- Xử lý đầu vào: Nhận văn bản hoặc ảnh chụp màn hình (tách bước OCR xử lý ảnh riêng để người dùng kiểm tra chữ trước khi đưa cho LLM, tránh lỗi bịa chữ).  
- Tiền xử lý dữ liệu: Tự động che/xóa số điện thoại (PII) của chủ trọ trước khi lưu vào DB hoặc gửi API cho LLM.  
- Tính năng trích xuất bằng LLM: Bóc tách phí, gán nhãn trạng thái và trích xuất câu gốc.  
- Tính toán logic (Backend code): Chuẩn hóa đơn vị, nhân với định mức tiêu thụ sinh viên giả định (hiển thị rõ giả định này trên UI) để ra chi phí ước tính.  
- Bản đồ: Hiển thị vị trí phòng trọ, tính khoảng cách và thời gian di chuyển đến trường đại học (Goong API).  
- So sánh: Đặt 2 hoặc nhiều bài đăng cạnh nhau trên cùng một hệ quy chiếu chi phí/khoảng cách.  
**Out-of-scope (Không làm):**  
- *Không* làm tính năng chatbot hỏi đáp với AI về phòng trọ.  
- *Không* tự động crawl dữ liệu bài đăng từ Facebook.  
- *Không* làm tính năng thông báo khi có phòng mới.  
- *Không* làm tính năng đánh giá (review) chủ trọ.  
- *Không* làm tính năng so sánh giá với mặt bằng chung của khu vực (do không đủ lượng dữ liệu mẫu được xác minh trong 1 học kỳ).  
**4. Plan and ownership**  
*Vì dự án chỉ có 1 thành viên, Nguyễn Văn Bình Dương chịu trách nhiệm toàn bộ các hạng mục.*  
| | | | |  
|-|-|-|-|  
| **Checkpoint** | **Ngày dự kiến** | **Công việc & Phân công** | **Kết quả kiểm chứng được (Deliverable)** |   
| **CP1** | 13/10/2026 | **Dương:** Thu thập bộ dữ liệu 150 bài đăng thật, làm sạch và chia tập (50 train/100 test). Phỏng vấn 5 người dùng. Viết IA#1 (Đặc tả). | Kho dữ liệu 150 bài đã khóa file; Báo cáo phỏng vấn 5 user; File IA#1. |   
| **CP2** | 20/10/2026 | **Dương:** Thiết kế UI/UX mockup (Figma). Viết script tiền xử lý: che số điện thoại và tích hợp OCR cơ bản. | Mockup Figma hoàn thiện; Script che PII và OCR chạy thành công trên terminal. |   
| **CP3** | 27/10/2026 | **Dương:** Viết prompt LLM, thiết lập hàm so khớp chuỗi kiểm tra câu trích. Chạy test và gán nhãn độc lập (để kiểm tra độ đồng thuận). | Báo cáo độ chính xác trên 100 bài test (đo lường 4 loại lỗi LLM); API LLM hoạt động. |   
| **CP4** | 03/11/2026 | **Dương:** Xây dựng backend tính toán tổng chi phí dựa trên kết quả LLM và giả định. Tích hợp Goong API tính khoảng cách. | API trả về JSON chứa chi phí tổng và khoảng cách từ địa chỉ cho trước. |   
| **CP5** | 10/11/2026 | **Dương:** Lên giao diện Web/PWA, ghép nối Frontend với Backend. Thiết kế tính năng hiển thị "Tối thiểu... còn x khoản chưa rõ". | Ứng dụng PWA chạy trên localhost, nhập text ra kết quả giao diện hoàn chỉnh. |   
| **CP6** | 17/11/2026 | **Dương:** Triển khai (Deploy), kiểm thử thực tế. Làm tính năng so sánh 2 bài đăng cạnh nhau. Viết báo cáo cuối kỳ. | Link live của ứng dụng; Source code hoàn chỉnh; Báo cáo nghiệm thu dự án. |   
   
**5. Risks**  
**Rủi ro 1: Rủi ro về hành vi người dùng (Product Risk)**  
- **Vấn đề:** Sinh viên cảm thấy phiền phức, lười copy/paste từng bài đăng hoặc tải ảnh lên app và quay về cách đọc chay cũ.  
- **Biện pháp giảm thiểu (Bắt đầu tuần này):** Áp dụng Wizard of Oz / Manual testing. Yêu cầu 5 tân sinh viên gửi bài đăng cho tôi qua Zalo, tôi trả lại bảng tính tay chi tiết. Đo lường xem họ có chủ động gửi bài thứ 2, thứ 3 cho tôi hay không để đánh giá nhu cầu thực tế trước khi code.  
**Rủi ro 2: Rủi ro kỹ thuật LLM (LLM Miss/Hallucination Risk)**  
- **Vấn đề:** LLM bỏ sót thông tin phí có trong bài (khiến chi phí rẻ hơn thực tế) hoặc bịa ra text không có thật để đánh lừa hệ thống.  
- **Biện pháp giảm thiểu (Bắt đầu tuần này):** Xây dựng bộ test cứng gồm 100 bài ngay từ đầu. Chủ động đưa các "ca khó" vào bộ test: viết tắt ("đ/n", "đ 3k5"), bài đăng gộp nhiều phòng, giá theo đầu người thay vì theo phòng. Áp dụng thuật toán so khớp chuỗi (Exact String Matching) trên code bắt buộc nội dung LLM trích xuất phải nằm gọn trong string bài đăng gốc.  
**6. Technology choices**  
- **Mô hình LLM: Gemini 1.5 Flash (Google)**. Lý do: Rẻ ($0.075/1M token input văn bản), tốc độ phản hồi cực nhanh, hoàn toàn đáp ứng tốt cho task trích xuất thông tin hẹp này.  
- **Dịch vụ Bản đồ: Goong API + MapLibre**. Lý do: Nhà cung cấp nội địa, định vị địa chỉ tiếng Việt/ngõ hẻm chính xác hơn và chi phí rẻ hơn đáng kể so với Google Maps API.  
- **Backend & Database: Node.js + PostgreSQL (PostGIS)**. Lý do: PostGIS là tiêu chuẩn công nghiệp hiệu quả nhất để lưu trữ tọa độ và query tính toán khoảng cách không gian.  
- **Frontend: PWA (Next.js/React)**. Lý do: Người dùng trải nghiệm giống app native trên điện thoại nhưng không cần phải đưa lên App Store/Google Play, giảm rào cản cài đặt cho người dùng mới.  