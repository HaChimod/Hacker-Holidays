Bước 1: Đăng ký tài khoản vào trang web
<img width="1486" height="839" alt="image" src="https://github.com/user-attachments/assets/cc695951-1f0b-4dff-84f9-04bb38f39d7a" />
Ở đây ta thấy hiện tại tài khoản của chúng ta có 0 ponzi và ở dưới ta thấy khi có được 150 ponzi ta sẽ có thể mở được whale vault và nhận được phần thưởng, có thể nó là flag mà ta cần tìm.
Bước 2: Ở phần gợi ý ta thấy có tag burpsuite nên ta tiến hành sử dụng ứng dụng này. Ta bắt request /claim để sử dụng vì nghi ngờ cần exploit ở đây vì sau 24h mới reset 1 lần nên nếu bypass được thì mình sẽ nhận được reward.
<img width="1921" height="838" alt="image" src="https://github.com/user-attachments/assets/5c9f0e86-9562-439e-b02c-ea8dcb6b9853" />
Ở đây ta thấy nó có session id của mình nên ta sẽ chuyển sang repeater và tạo group để nhận ponzi.
Trước khi dùng repeater thì ta tắt intercept ở tab proxy (drop request /claim để tránh bị nhận trước khi sử dụng repeater) đi và gửi bằng lựa chọn sendgroup (parallel) để có thể gửi hết tất cả request trong 1 lần
<img width="1929" height="918" alt="image" src="https://github.com/user-attachments/assets/f9b30541-0dba-44e9-9123-7feedaecc94c" />
Ta tạo 8 tab để nhận ( Thực tế chỉ cần tầm 3 tab để đủ 150 ponzi)
<img width="2099" height="858" alt="image" src="https://github.com/user-attachments/assets/dc275ebb-79cf-4705-89cc-fed4c6c686bd" />
Ở đây ta thấy là đã nhận được và số dư đã update
Bước 3: Quay lại trang web và thấy đã được update số dư và đủ điều kiện để nhận reward
Ta nhấn vào open vault và nhận được flag: THM{t0w3l_0n_th3_sunb3d_d0ubl3_sp3nt}
<img width="1336" height="490" alt="image" src="https://github.com/user-attachments/assets/261a52fb-3ac5-413f-bfe5-456053364554" />

