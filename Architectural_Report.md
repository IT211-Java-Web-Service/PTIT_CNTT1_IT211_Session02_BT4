 
# Báo Cáo Phân Tích Kiến Trúc Web Service
## Công ty Tài Chính - Lựa Chọn Giữa SOAP và REST

---

## Bối Cảnh

Công ty tài chính đang vận hành hệ thống **core banking legacy** đã hoạt động nhiều năm, đồng thời có kế hoạch xây dựng thêm các **microservices hiện đại** để mở rộng sản phẩm số. Bài toán đặt ra là lựa chọn giao thức Web Service phù hợp cho từng mảng hệ thống.

---

## Phần 2 - Báo Cáo Phân Tích Toàn Diện

### 2.1 Lý Do Lựa Chọn SOAP Cho Hệ Thống Legacy Core Banking

- **Tính hợp đồng chặt chẽ (Strict Contract):**
  - SOAP sử dụng WSDL (Web Services Description Language) để định nghĩa rõ ràng cấu trúc request/response.
  - Đảm bảo tính nhất quán dữ liệu giữa các hệ thống cũ vốn đã được xây dựng dựa trên hợp đồng này.
  - Mọi thay đổi schema đều được phát hiện sớm ở tầng compile/validation, tránh lỗi runtime.

- **Bảo mật cấp độ doanh nghiệp (WS-Security):**
  - SOAP hỗ trợ WS-Security — tiêu chuẩn bảo mật được thiết kế đặc thù cho môi trường tài chính, ngân hàng.
  - Cho phép mã hóa từng phần của message (không cần mã hóa toàn bộ), ký số (digital signature) trực tiếp trên XML body.
  - Hỗ trợ xác thực đa tầng: username/password token, X.509 certificate, SAML assertion.
  - Đáp ứng các tiêu chuẩn tuân thủ quốc tế như PCI-DSS, ISO 27001 mà các ngân hàng bắt buộc phải có.

- **Hỗ trợ giao dịch phân tán (WS-AtomicTransaction):**
  - Các nghiệp vụ tài chính như chuyển tiền liên ngân hàng yêu cầu tính toàn vẹn giao dịch (ACID).
  - SOAP hỗ trợ WS-AtomicTransaction và WS-ReliableMessaging để đảm bảo message không bị mất hoặc xử lý hai lần.
  - REST không có cơ chế tương đương ở tầng giao thức — phải tự xử lý ở tầng ứng dụng.

- **Khả năng truyền tải đa giao thức:**
  - SOAP không bị ràng buộc với HTTP — có thể chạy trên SMTP, FTP, JMS.
  - Phù hợp với các hệ thống legacy vốn không dùng HTTP thuần mà dùng message queue hoặc file transfer.

- **Tính stateful khi cần:**
  - Một số nghiệp vụ ngân hàng yêu cầu duy trì trạng thái phiên giao dịch qua nhiều bước.
  - SOAP hỗ trợ stateful thông qua WS-Session, trong khi REST thuần túy là stateless.

---

### 2.2 Lý Do Lựa Chọn REST Cho Microservices Hiện Đại

- **Hiệu suất và tài nguyên:**
  - REST sử dụng JSON — nhẹ hơn XML của SOAP từ 30–50% về kích thước payload.
  - Giảm băng thông, tăng tốc độ phản hồi — quan trọng với mobile app và web app hiện đại.
  - Không cần parse SOAP Envelope phức tạp, tiết kiệm CPU ở cả client và server.

- **Tốc độ phát triển nhanh:**
  - REST không yêu cầu WSDL hay sinh code tự động — developer có thể bắt đầu tích hợp chỉ với tài liệu API đơn giản.
  - Hỗ trợ tốt bởi mọi ngôn ngữ lập trình và framework hiện đại mà không cần thư viện đặc thù.
  - Thích hợp với phương pháp Agile và vòng lặp phát triển ngắn (sprint).

- **Tính stateless:**
  - Mỗi request REST mang đầy đủ thông tin xác thực (token), không cần server lưu session.
  - Dễ scale ngang (horizontal scaling) — thêm instance mới mà không cần đồng bộ session.
  - Phù hợp với kiến trúc container (Docker, Kubernetes) mà microservices thường dùng.

- **Bảo mật linh hoạt:**
  - REST dùng token-based authentication: JWT, OAuth 2.0, API Key.
  - Dễ tích hợp với Identity Provider bên thứ ba (Google, Auth0, Keycloak).
  - HTTPS đã đủ để mã hóa toàn bộ kết nối cho phần lớn use case của microservices.

- **Khả năng tích hợp rộng:**
  - REST là tiêu chuẩn de facto của Web API hiện đại — mọi bên thứ ba (fintech, partner, mobile SDK) đều hỗ trợ.
  - Dễ dàng tài liệu hóa với OpenAPI/Swagger — tự động sinh client SDK, test collection.
  - Hỗ trợ caching thông qua HTTP cache headers (ETag, Cache-Control) giúp giảm tải server.

---

### 2.3 Khuyến Nghị Cho Công Ty

- **Áp dụng kiến trúc Hybrid (SOAP + REST):**
  - Không cần chọn một trong hai — mỗi giao thức phù hợp với một lớp hệ thống khác nhau.
  - Dùng **SOAP** cho toàn bộ lớp giao tiếp với core banking legacy, hệ thống thanh toán liên ngân hàng, và các nghiệp vụ yêu cầu tuân thủ nghiêm ngặt.
  - Dùng **REST** cho toàn bộ microservices mới, API gateway, mobile backend, và tích hợp với đối tác fintech.

- **Triển khai API Gateway làm lớp trung gian:**
  - API Gateway đứng giữa client và hai lớp hệ thống, chịu trách nhiệm chuyển đổi REST ↔ SOAP khi cần.
  - Client hiện đại chỉ cần gọi REST API — Gateway tự động dịch sang SOAP để giao tiếp với legacy.
  - Giảm thiểu rủi ro khi thay đổi hệ thống legacy, đồng thời không làm gián đoạn trải nghiệm developer bên ngoài.

- **Lộ trình dài hạn:**
  - Không nên vội vã thay thế hệ thống SOAP legacy — chi phí và rủi ro rất cao.
  - Từng bước đóng gói (wrap) các SOAP service thành REST facade để hiện đại hóa dần.
  - Ưu tiên REST cho mọi tính năng mới — SOAP chỉ dùng khi bắt buộc tương thích với hệ thống cũ.

---

## Glossary - Tổng Hợp Khái Niệm Cốt Lõi

### SOAP (Simple Object Access Protocol)

| Khái niệm | Định nghĩa |
|-----------|-----------|
| **SOAP** | Giao thức trao đổi thông điệp dựa trên XML, được định nghĩa bởi W3C. Hoạt động độc lập với giao thức truyền tải. |
| **WSDL** | Web Services Description Language — tài liệu XML mô tả đầy đủ interface của SOAP service (operation, message, binding). |
| **SOAP Envelope** | Phần tử gốc của mọi SOAP message, bao gồm Header và Body. |
| **SOAP Header** | Phần tùy chọn chứa metadata: thông tin bảo mật, routing, transaction. |
| **SOAP Body** | Phần bắt buộc chứa nội dung chính của request hoặc response. |
| **SOAP Fault** | Cấu trúc chuẩn để trả về lỗi trong SOAP, tương đương HTTP error code của REST. |
| **WS-Security** | Tiêu chuẩn bảo mật cho SOAP: mã hóa XML, ký số, xác thực token. |
| **WS-AtomicTransaction** | Tiêu chuẩn quản lý giao dịch phân tán đảm bảo tính ACID qua nhiều service. |
| **WS-ReliableMessaging** | Đảm bảo message được gửi đúng thứ tự và không bị mất dù mạng không ổn định. |
| **Stateful** | Khả năng duy trì trạng thái phiên làm việc giữa nhiều request liên tiếp. |
| **XML** | Định dạng dữ liệu duy nhất SOAP hỗ trợ — verbose nhưng có schema validation chặt chẽ. |

### REST (Representational State Transfer)

| Khái niệm | Định nghĩa |
|-----------|-----------|
| **REST** | Phong cách kiến trúc phần mềm dựa trên các ràng buộc của HTTP, được Roy Fielding định nghĩa năm 2000. |
| **Resource** | Mọi thực thể trong hệ thống đều được biểu diễn là tài nguyên, truy cập qua URI duy nhất. |
| **HTTP Methods** | Các phương thức HTTP biểu đạt hành động: GET (đọc), POST (tạo), PUT (thay thế), PATCH (cập nhật một phần), DELETE (xóa). |
| **Stateless** | Mỗi request phải chứa đầy đủ thông tin để server xử lý, không phụ thuộc vào trạng thái phiên trước đó. |
| **JSON** | Định dạng dữ liệu phổ biến nhất của REST — nhẹ, dễ đọc, hỗ trợ tốt bởi mọi ngôn ngữ lập trình. |
| **HTTP Status Code** | Mã trạng thái chuẩn của HTTP biểu đạt kết quả xử lý: 200, 201, 204, 400, 401, 403, 404, 500. |
| **JWT** | JSON Web Token — token tự chứa thông tin xác thực, dùng phổ biến trong REST API. |
| **OAuth 2.0** | Framework ủy quyền chuẩn cho REST API, cho phép xác thực qua bên thứ ba. |
| **OpenAPI/Swagger** | Tiêu chuẩn tài liệu hóa REST API — tự động sinh UI test, client SDK. |
| **HATEOAS** | Hypermedia As The Engine Of Application State — ràng buộc REST nâng cao, response chứa các link đến action liên quan. |
| **Caching** | REST tận dụng HTTP cache (ETag, Cache-Control) để giảm tải server và tăng tốc độ phản hồi. |
| **Content Negotiation** | Cơ chế client và server thỏa thuận định dạng dữ liệu qua header `Accept` và `Content-Type`. |

---

## Tổng Kết

| Tiêu chí | SOAP (Legacy Core Banking) | REST (Microservices) |
|----------|---------------------------|----------------------|
| Định dạng dữ liệu | XML | JSON |
| Giao thức truyền tải | HTTP, SMTP, FTP, JMS | HTTP/HTTPS |
| Tính trạng thái | Stateful / Stateless | Stateless |
| Bảo mật | WS-Security, XML Signature | OAuth 2.0, JWT, HTTPS |
| Hiệu suất | Nặng hơn do XML overhead | Nhẹ, nhanh |
| Độ phức tạp | Cao | Thấp |
| Tốc độ phát triển | Chậm | Nhanh |
| Tích hợp bên thứ ba | Khó | Dễ |
| Phù hợp với | Nghiệp vụ tài chính phức tạp, hệ thống cũ | API hiện đại, mobile, fintech partner |