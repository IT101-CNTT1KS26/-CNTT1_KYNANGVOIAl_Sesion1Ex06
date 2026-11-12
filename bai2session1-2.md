# GIẢI MÃ HIỆN TƯỢNG "AI QUÊN THÔNG TIN" TRONG PHIÊN CHAT CSKH — MARTX

## PHẦN 1: GIẢI THÍCH HÌNH TƯỢNG "CHIẾC CỐC NƯỚC" (CONTEXT WINDOW)
Để giải đáp thắc mắc của nhân viên vì sao AI lại "mất trí nhớ ngắn hạn" ở Lượt 42, chúng ta có thể hình dung **Context Window (Cửa sổ ngữ cảnh/Bộ nhớ RAM của AI)** giống như **một chiếc cốc nước có dung tích giới hạn** (ví dụ: cố định ở một mức dung lượng token nhất định).

* **Cơ chế hoạt động:** Mỗi một lượt nhắn tin qua lại (gồm câu hỏi của khách hàng và câu trả lời của AI) giống như việc **đổ nước hoặc thả các khối thông tin vào trong cốc**.
* **Giai đoạn đầu phiên (Lượt 1):** Khách hàng cung cấp tên Trần Văn B và mã đơn hàng `#MTX-9988`. Thông tin này được thả vào đáy cốc. Lúc này cốc còn trống tới 95%, AI ghi nhớ rất rõ và phản hồi chính xác.
* **Giai đoạn phình to dữ liệu (Qua 40 lượt chat dông dài):** Do nội dung tư vấn rau củ kéo dài liên tục, các câu trả lời dài dòng tích lũy dần lên đến 32.000 Tokens, khiến **chiếc cốc bị đầy tràn 100%**.
* **Hiện tượng Truncation (Tràn cốc & Cắt gọt):** AI có một giới hạn vật lý về bộ nhớ ngắn hạn. Khi cốc đã đầy mà tiếp tục nhận thông tin mới ở các lượt sau, **lượng nước cũ ở đáy cốc (chính là thông tin định danh ở Lượt 1) buộc phải bị đẩy văng ra ngoài** để nhường chỗ cho dữ liệu mới tràn vào.

Vì vậy, đến Lượt 42, khi khách hàng hỏi về việc hoàn tiền, **Lượt 1 đã biến mất hoàn toàn khỏi "tầm nhìn" của AI**. AI không hề chủ quan "quên", đơn giản là dữ liệu đó đã bị xóa khỏi RAM tạm thời để tránh tràn bộ nhớ, dẫn đến việc gọi nhầm tên thành "chị Mai" và đòi xin lại mã đơn hàng.



## PHẦN 2: PHƯƠNG ÁN QUẢN LÝ RAM BỘ NHỚ (CHỐNG TRÀN CỐC)

Để khắc phục triệt để tình trạng này và giữ cho luồng tư vấn luôn chính xác, chuyên nghiệp, chúng ta cần áp dụng đồng thời 2 giải pháp từ phía người dùng và hệ thống:

### 1. Phương án từ phía Người dùng (Thao tác vận hành thực tế)

* **Quy tắc làm mới phiên chat (Refresh Session):** Khi cuộc hội thoại chuyển sang một chủ đề mới hoặc khi số lượng trao đổi vượt quá 15–20 lượt, nhân viên CSKH nên chủ động tóm tắt lại vấn đề cốt lõi hoặc hướng dẫn khách hàng chuyển sang một khung chat mới nếu cần thiết.
* **Neo giữ thông tin (Re-injecting Context):** Trong các phiên tư vấn dài, định kỳ sau mỗi 10 lượt chat, nhân viên nên lồng ghép nhắc lại mã đơn hàng hoặc tên khách hàng vào câu lệnh tư vấn (ví dụ: *"Đối với đơn hàng #MTX-9988 của anh B, em xin thông tin tiếp về..."*). Thao tác này giúp thông tin định danh luôn nằm ở phần trên của "chiếc cốc", không bị đẩy văng mất.

### 2. Phương án từ phía Thiết kế Hệ thống (Giải pháp kỹ thuật tự động)

* **Ứng dụng RAG & Cơ sở dữ liệu khách hàng (Vector DB / Database Mapping):** Thay vì bắt AI phải nhớ mọi thứ qua lịch sử chat thô (Raw Chat History) dễ bị tràn cốc, hệ thống khung chat CSKH nên tích hợp lấy dữ liệu ngầm. Khi khách hàng kết nối, hệ thống tự động ghim (pin) Tên và Mã đơn hàng vào lớp **System Prompt** của mọi lượt gọi AI, đảm bảo AI luôn đọc được thông tin định danh bất kể lịch sử chat có dài đến đâu.
* **Cơ chế Tóm tắt tự động (Conversation Summarization):** Tích hợp tính năng kỹ thuật tự động thu gọn lịch sử. Khi phiên chat đạt ngưỡng 20 lượt, hệ thống ngầm sẽ dùng một mô hình phụ để tóm tắt lại các ý chính của 20 lượt trước (ví dụ: *"Khách tên Trần Văn B, đơn #MTX-9988 đang thắc mắc về rau củ"*), sau đó xóa bớt lịch sử thô và chỉ giữ lại bản tóm tắt này trong "chiếc cốc" của AI. Nhờ đó, RAM luôn được giải phóng mà không làm mất thông tin quan trọng.