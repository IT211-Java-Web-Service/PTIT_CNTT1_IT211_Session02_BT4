 
# Báo cáo phân tích lựa chọn SOAP và REST cho hệ thống công ty tài chính

## 1. Bối cảnh

Công ty tài chính đang vận hành hai nhóm hệ thống chính:

- **Legacy core banking**: hệ thống lõi ngân hàng cũ, xử lý giao dịch quan trọng, yêu cầu bảo mật cao, tính ổn định cao và khả năng tích hợp với các hệ thống truyền thống.
- **Microservices mới**: các dịch vụ hiện đại phục vụ mobile app, web app, đối tác fintech, cần tốc độ phát triển nhanh, dễ mở rộng và hiệu suất tốt.

Vấn đề đặt ra là cần lựa chọn kiến trúc Web Service phù hợp giữa **SOAP** và **REST** cho từng nhóm hệ thống.

---

# Phần 1 - Phân tích logic

## 2. Tổng quan SOAP và REST

## 2.1. SOAP là gì?

SOAP, viết tắt của **Simple Object Access Protocol**, là một giao thức Web Service có cấu trúc chặt chẽ, thường sử dụng **XML** để trao đổi dữ liệu.

SOAP có thể hoạt động trên nhiều giao thức truyền tải như:

- HTTP
- SMTP
- FTP
- TCP

SOAP thường được dùng trong các hệ thống doanh nghiệp lớn, ngân hàng, bảo hiểm, tài chính, nơi cần tính chuẩn hóa, bảo mật và độ tin cậy cao.

## 2.2. REST là gì?

REST, viết tắt của **Representational State Transfer**, là một phong cách kiến trúc dùng phổ biến trong xây dựng API hiện đại.

REST thường hoạt động trên HTTP và sử dụng các phương thức chuẩn như:

- GET
- POST
- PUT
- PATCH
- DELETE

REST có thể hỗ trợ nhiều định dạng dữ liệu, nhưng phổ biến nhất là **JSON** vì nhẹ, dễ đọc và phù hợp với web/mobile.

---

# 3. So sánh SOAP và REST trong công ty tài chính

## 3.1. Hiệu suất

### SOAP

SOAP thường có hiệu suất thấp hơn REST vì:

- Dữ liệu chủ yếu dùng XML, có kích thước lớn hơn JSON.
- Cấu trúc message SOAP phức tạp hơn.
- Việc parse XML tốn nhiều tài nguyên hơn.
- Có nhiều lớp tiêu chuẩn bổ sung như WS-Security, WSDL, SOAP Envelope.

Tuy nhiên, trong hệ thống core banking, hiệu suất không phải lúc nào cũng là yếu tố duy nhất. Độ chính xác, bảo mật và tính toàn vẹn giao dịch thường quan trọng hơn tốc độ.

### REST

REST thường có hiệu suất tốt hơn vì:

- Dữ liệu JSON nhẹ hơn XML.
- Message đơn giản hơn.
- Tận dụng tốt HTTP cache, status code và URL resource.
- Phù hợp với mobile app, frontend và microservices.

Với các microservices mới, REST là lựa chọn tối ưu hơn vì yêu cầu tốc độ phản hồi nhanh, payload nhẹ và dễ tích hợp.

### Kết luận về hiệu suất

- **Legacy core banking**: SOAP vẫn phù hợp nếu ưu tiên tính ổn định và giao dịch an toàn hơn tốc độ.
- **Microservices mới**: REST phù hợp hơn vì nhanh, nhẹ và tối ưu tài nguyên.

---

## 3.2. Bảo mật

### SOAP

SOAP có thế mạnh về bảo mật nhờ hỗ trợ các tiêu chuẩn doanh nghiệp như:

- WS-Security
- XML Encryption
- XML Signature
- Username Token
- Security Assertion Markup Language, hay SAML

SOAP có thể bảo mật ở cấp độ message. Điều này có nghĩa là dữ liệu vẫn có thể được bảo vệ ngay cả khi đi qua nhiều hệ thống trung gian.

Đây là điểm rất quan trọng với core banking, vì dữ liệu tài chính cần đảm bảo:

- Tính bảo mật
- Tính toàn vẹn
- Tính xác thực
- Khả năng kiểm toán

### REST

REST thường dùng các cơ chế bảo mật hiện đại như:

- HTTPS
- OAuth2
- JWT
- API Key
- Token-based authentication

REST dễ triển khai bảo mật cho web app, mobile app và microservices. Tuy nhiên, REST chủ yếu bảo mật ở tầng truyền tải thông qua HTTPS. Nếu dữ liệu đi qua nhiều tầng trung gian, việc bảo mật message-level không mạnh bằng SOAP.

### Kết luận về bảo mật

- **Legacy core banking**: nên dùng SOAP vì hỗ trợ WS-Security và bảo mật cấp doanh nghiệp tốt hơn.
- **Microservices mới**: nên dùng REST với HTTPS, OAuth2 hoặc JWT vì dễ triển khai, nhẹ và phù hợp với kiến trúc hiện đại.

---

## 3.3. Độ phức tạp

### SOAP

SOAP có độ phức tạp cao hơn vì cần:

- WSDL để mô tả service.
- XML Schema để định nghĩa dữ liệu.
- SOAP Envelope cho message.
- Cấu hình WS-Security.
- Tool hoặc framework chuyên dụng để sinh client/server.

Điều này làm quá trình phát triển và bảo trì phức tạp hơn, nhưng đổi lại hệ thống có tính chuẩn hóa cao.

### REST

REST đơn giản hơn vì:

- Dùng HTTP trực tiếp.
- Dữ liệu JSON dễ đọc, dễ debug.
- Dễ test bằng Postman, curl hoặc browser.
- Dễ tích hợp với frontend/mobile.
- Không cần WSDL phức tạp.

REST phù hợp với đội phát triển cần tốc độ, sự linh hoạt và khả năng release nhanh.

### Kết luận về độ phức tạp

- **Legacy core banking**: chấp nhận độ phức tạp của SOAP để đổi lấy tính ổn định và chuẩn hóa.
- **Microservices mới**: REST phù hợp hơn vì đơn giản, dễ phát triển và dễ bảo trì.

---

## 3.4. Khả năng phát triển tính năng mới

### SOAP

SOAP ít linh hoạt hơn khi thay đổi API vì:

- Contract WSDL chặt chẽ.
- Thay đổi schema có thể ảnh hưởng client cũ.
- Chu kỳ phát triển thường dài hơn.
- Việc versioning phức tạp hơn.

Vì vậy SOAP không phù hợp nếu hệ thống cần thay đổi liên tục.

### REST

REST có khả năng phát triển nhanh hơn vì:

- API dễ thiết kế theo resource.
- Dễ versioning qua URL, ví dụ `/api/v1/accounts`.
- Dễ thêm endpoint mới.
- Phù hợp với Agile, Scrum và DevOps.
- Phù hợp với microservices độc lập.

### Kết luận về khả năng phát triển

- **Legacy core banking**: SOAP phù hợp nếu nghiệp vụ ít thay đổi, yêu cầu ổn định cao.
- **Microservices mới**: REST phù hợp hơn vì linh hoạt, dễ mở rộng và phát triển nhanh.

---

## 3.5. Khả năng tích hợp

### SOAP

SOAP phù hợp với các hệ thống doanh nghiệp cũ vì:

- Nhiều hệ thống legacy đã dùng XML và SOAP.
- WSDL giúp client biết rõ service cung cấp gì.
- Hỗ trợ nhiều giao thức ngoài HTTP.
- Có chuẩn rõ ràng cho enterprise integration.

Tuy nhiên, SOAP khó tích hợp với mobile app và web app hiện đại hơn REST.

### REST

REST dễ tích hợp với:

- Web frontend
- Mobile app
- Microservices
- Third-party API
- Cloud service
- Fintech partner

REST dùng JSON nên rất phù hợp với JavaScript, Android, iOS và các hệ thống hiện đại.

### Kết luận về khả năng tích hợp

- **Legacy core banking**: SOAP phù hợp hơn với hệ thống cũ và các tích hợp enterprise.
- **Microservices mới**: REST phù hợp hơn với hệ sinh thái hiện đại.