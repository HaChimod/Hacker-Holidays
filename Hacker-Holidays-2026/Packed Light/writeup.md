Bước 1: Initial Analysis
Bước đầu tiên là mở file traffic.pcapng bằng Wireshark. Do đề bài nhắc đến lưu lượng mạng bất thường, mình tập trung vào các giao thức tầng ứng dụng.
<img width="991" height="470" alt="1" src="https://github.com/user-attachments/assets/8944f42a-e304-4727-a59b-831dd3f7423d" />

Ở đây ta thấy địa chỉ ip local giao tiếp tuần tự với 1 ip public trong mạng và có lấy 1 file python
Bước 2: Export HTTP Objects
Trong các object xuất hiện có một file Python.
Sau khi export, thu được đoạn mã:
<img width="733" height="788" alt="2" src="https://github.com/user-attachments/assets/61fbdf07-9e17-4d8e-984a-d1fc1f475280" />

Ta thấy đây là file key logger, được mã hóa bằng xor. 
Khóa được tạo từ:
p1 = "H0t3lSt@ff0Nly"
p2 = "K3epS3cr3t!"
Thay vì gửi trong URL hay POST body, malware giấu dữ liệu trong HTTP Cookie:
hotel_sess_state=<Base64(XOR(character))>
Bước 3: Lấy dữ liệu được giấu bằng Tshark
<span style="color: #ff0000">
tshark -r traffic.pcapng \
-Y "http.cookie contains hotel_sess_state" \
-T fields \
-e http.cookie
</span>
<img width="862" height="538" alt="3" src="https://github.com/user-attachments/assets/11532a0f-fe8f-4d7a-8d35-5802d49fc65e" />
Bước 4: Giải mã lại dữ liệu bằng python để lấy flag.
Do malware sử dụng: 
Plaintext
↓
XOR
↓
Base64
↓
Cookie

Ta thực hiện ngược lại:
Cookie
↓
Base64 Decode
↓
XOR cùng key
↓
Original Character
<img width="896" height="782" alt="4" src="https://github.com/user-attachments/assets/7a671767-20a1-40d3-9542-ff0d0a6e3c7b" />


