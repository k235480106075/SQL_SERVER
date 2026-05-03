Bước 1: cài đặt SLQ_SERVER
<img width="1919" height="1002" alt="image" src="https://github.com/user-attachments/assets/cc91daf6-8914-44c3-b265-05f395c96e9a" />

Bước 2: Cấu hình cho SQL Server làm việc ở cổng động (Dynamic Port), TCP: enable+active yes cho 127.0.0.1, chọn cổng động là 3xxxx với xxxx là 4 số cuối của mã số sv, (nếu cổng này đã mở sẵn trước đó bởi 1 ứng dụng khác thì chọn cổng là 4xxxx hoặc 5xxxx)
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/8370132a-d562-46cd-b486-a87d77f004b4" />

Bước 3: Kiểm tra xem service SQL Server có đang running và mở đúng cổng đã chọn hay không?
Sử dụng lệnh trên cmd: netstat -ano | findstr LISTENING để liệt kê các cổng mà máy tính đang mở,
Nếu thấy dòng: TCP 0.0.0.0:xxxxx với xxxxx là cổng đã chọn ở bước 2 là OK.
<img width="1917" height="1079" alt="image" src="https://github.com/user-attachments/assets/780b7430-965a-4254-817e-d88f1b26fbe4" />

Bước 4: Cài đặt SQL Server Management Studio Link tải SSMS: https://learn.microsoft.com/en-us/ssms/install/install + Bước 5: Chạy phần mềm ssms để Đăng nhập vào SQL Server bằng 2 cách: Windows Authentication và SQL Server Authentication.
Servername: localhost,xxxxx (với xxxxx là cổng đã chọn ở bước 2)
Link tải SSMS: https://learn.microsoft.com/en-us/ssms/install/install
<img width="1913" height="1027" alt="image" src="https://github.com/user-attachments/assets/4f448865-43f1-4a65-84fe-9c04ac73307a" />

Bước 6: Sử dụng giao diện đồ hoạ của ssms: Tạo cơ sở dữ liệu mới (create database) với tên tuỳ ý, chọn Path (nơi lưu trữ db) cho file lưu dữ liệu và file lưu log ở ổ đĩa khác với ổ C. mở path đã chọn xem 2 file đã tạo ra.
<img width="1128" height="625" alt="image" src="https://github.com/user-attachments/assets/815a1ef3-734a-4d2f-bfa1-7a25a71e656e" />

Bước 7:Sử dụng giao diện đồ hoạ của ssms: Tạo bảng dữ liệu (create and design table) với tên bảng tuỳ ý, có các trường dữ liệu phù hợp với dữ liệu của file data mẫu (CSV), với Khoá chính (Primary Key) là trường masv
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/b1dcbf07-d845-41f6-8441-4e954515454a" />

Bước 8: Sử dụng giao diện đồ hoạ của ssms: Tìm cách import dữ liệu từ file mẫu vào trong bảng vừa tạo.
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/17e98be0-f26a-4a6a-89c5-f4f9c2cf80e6" />

Bước 9: Mở cửa sổ mới để gõ lệnh trong ssms: GÕ lệnh để kiểm tra xem số dòng của bảng dữ liệu sau khi import, kết quả ok sẽ khoảng 12020 dòng.
<img width="1906" height="1014" alt="image" src="https://github.com/user-attachments/assets/bbb0e3c6-b3ac-4cd2-9ade-9140064419a1" />

Bước 10: Trong cửa sổ mới để gõ lệnh: Gõ lệnh để thêm (insert) 1 row vào bảng, với dữ liệu là thông tin cá nhân của sv đang làm bài (mỗi sv sẽ luôn khác nhau ở bước này).
<img width="1918" height="1075" alt="image" src="https://github.com/user-attachments/assets/defe4c09-1f46-40bc-9a82-096924f83398" />

Bước 11: Trong cửa sổ mới để gõ lệnh: Gõ lệnh để cập nhật(update) trường noisinh thành 'Sao Hoả' cho những dòng có noisinh và diachi đều là NULL.
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/a731d7ef-f403-4bf7-b005-74a615e30348" />

Bước 12: Sử dụng giao diện đồ hoạ của ssms: Tạo bảng SaoHoa gồm những sinh viên có nơi sinh ở 'Sao Hoả', keyword gợi ý: sử dụng 1 câu lệnh: SELECT + INTO
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/5d0ef4e6-ce30-4acb-8e56-b09050d78241" />

Bước 13: Trong cửa sổ mới để gõ lệnh: Gõ lệnh xoá (delete) trong bảng SaoHoa những sinh viên cùng họ với em, vd em họ nguyễn thì xoá những sv họ nguyễn.
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/c7c7ef01-696b-46da-aebd-06c4a651cf29" />

Bước 14: Sử dụng giao diện đồ hoạ của ssms: Xuất toàn bộ kết quả của các bước 6,7,8,9,10,11,12,13 ra file dulieu.sql , keyword gợi ý: sử dụng tính năng GEN SCRIPT struct+data cho database
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/58b2a44b-71b3-49fd-8175-d7ce19cefc69" />

Bước 15: Sử dụng giao diện đồ hoạ của ssms: Xoá csdl đã tạo, sau khi xoá thành công, kiểm tra tại path (path chọn ở bước 6) xem còn tồn tại 2 file của bước 6 không?
<img width="1917" height="1079" alt="image" src="https://github.com/user-attachments/assets/aeb25f0e-36cc-4253-a51d-34f6c7409a84" />
<img width="283" height="462" alt="image" src="https://github.com/user-attachments/assets/85a77649-18fc-4bfb-b4cc-7be41bba848c" />

Bước 16: Tạo cửa sổ mới để gõ lệnh: mở file dulieu.sql của bước 14, chạy toàn bộ các lệnh này. REFRESH lại cây liệt kê các database => kiểm chứng kết quả được tạo ra tương đương với các bước 6,7,8,9,10,11,12,13.
<img width="1919" height="1078" alt="image" src="https://github.com/user-attachments/assets/e55fe505-07b2-40c8-8cb4-c273dfcb368c" />
Bước 17: upload file dulieu.sql lên github repository của em (repository mà em đang edit file README.md)
<img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/5458f830-2e34-491d-b034-96f02a1814b7" />

