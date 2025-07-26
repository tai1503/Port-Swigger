## 1. Thông tin bài lab
- Tên lab: **Web shell upload via extension blacklist bypass**
- Loại lỗ hổng: File upload -> RCE (Remote Code Execution)
- Link bài lab: [Web shell upload via extension blacklist bypass](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass)
- **Mục tiêu**: Upload webshell vào thư mục thực thi mã PHP, chiếm quyền điều khiển server và đọc nội dung tệp tin bí mật _**/home/carlos/secret**_
## 2. Phân tích ban đầu
**Chức năng ứng dụng:** 
- Sau khi đăng nhập tài khoản người dùng, người dùng có thể update email, upload ảnh avatar của người dùng.

![img1](./img/lab1/img1.png)

- Sau khi upload, ứng dụng thông báo đã upload thành công và đường dẫn lưu file ảnh upload.

![img2](./img/lab1/img2.png)

**Đặt giả thuyết**
-	Kiểm tra xem liệu ta có thể upload và thực thi một file php bằng cách tạo một file tên _test.php_ với nội dung `<?php phpinfo(); ?>` và upload lên server.
- Không thể upload file .php lên server

img

- Ở đây, ta sử dụng file **.htaccsess** để thay đổi config trong nội bộ thư mục chứa file đó và các thư mục con cho phép server xử lý các file theo mong muốn của ta. Sau đó upload file chứa mã khai thác theo config của ta để thực hiện RCE.

**Kiểm tra giả thuyết**
- Ta upload file **.htaccess** cấu hình cho server xử lý các file có đuôi **.test** xử lý như một file **.php**

img

- File **.htaccess** đã được upload thành công.

img

- Sau đó, ta upload một file có đuôi **.test** chữa mã PHP để thử xem liệu nó có thể thực thi mã PHP hay không?
- Ta có thể thấy file **.test** đã được thực thi. Vậy khi ta upload file **.test** thì có thể thực hiện RCE.

img
img

## 3. Tiến hành khai thác
- Upload file **shell.test** để thực hiện đọc tệp tin bí mật với nội dung: `<?php echo system($_GET['x']) ?>`

img

- Truyền tham số *'x'* để đọc nội dung tệp tin bí mật `x=cat%20/home/carlos/secret`

img
