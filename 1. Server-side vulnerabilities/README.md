# Intro
Trong cái học phần này thì ta sẽ được giới thiệu về một loạt các lỗ hổng Server-side thường gặp. Nó sẽ rất tuỵt vời nếu như bạn là một người mới tìm hiểu về Web Security và muốn có cái nhìn tổng quát về các lỗ hổng hiện có, cũng như cách một kẻ tấn công có thể xác định và khai thác nó trong hệ thống thực.

# 1. Path traversal
* Path traversal cũng được biết đến như là directory traversal. Lỗ hổng này cho phép một kẻ tấn công có thể tùy ý đọc các file hiện có trên cái máy chủ mà cái web đấy đang chạy. Nó có thể là những:
  * Code của cái web app đấy hay data đang lưu trữ trong web đấy
  * Những cái chứng chỉ, tài liệu của hệ thống back-end
  * Những file nhạy cảm của hệ điều hành
  * ...
* Trong một số trường hợp, kẻ tấn công cũng có thể viết vào các file trong server, cho phép chúng chỉnh sửa chính data hay hành vi của trang web, và cuối cùng kiểm soát luôn toàn bộ server

## Đọc file tùy ý bằng path traversal
* Hãy thử tưởng tượng, một cái web shop mua bán sẽ thường dùng các hình ảnh để hiển thị các sản phẩm. Và nó có thể load các hình ảnh đấy bằng việc sử dụng HTML như này:
```html
<img src="/loadImage?filename=218.png">
```
* Thì cái URL `loadImage` nhận vào một tham số `filename` và sẽ trả về nội dung của cái file cụ thể đó. Những file ảnh được lưu trữ tại vị trí `/var/www/images/`. Để có thể trả về ảnh thì web đó gắn tên file được yêu cầu trong `filename` vào đường dẫn gốc trên và sử dụng một filesystem API để đọc nội dung của file. Nói cách khác, trang web đó sẽ đọc file từ đường dẫn kiểu như này:
```
/var/www/images/218.png
```
* Sự triển khai của ứng dụng web này không có sự phòng thủ nào để chống lại khỏi sự tấn công của path traversal. Và kết quả là kẻ tấn công có thể yêu cầu được đường dẫn URL truy xuất được file `/etc/passwd` từ file hệ thống của server.
* Nói một cách dễ hiểu thì nếu 1 trang web gọi đường dẫn với tham số `a=tên_file` thì việc 1 kẻ tấn công lợi dụng nó để gọi các file khác, mà hệ thống lại không có cách gì bảo vệ thì đây chính là 1 lỗ hổng tiềm ẩn.
```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```
* Và điều đó dẫn đến việc web đọc file như này:
```
/var/www/images/../../../etc/passwd
```
* Dãy `../` được phép sử dụng trong các đường dẫn file và nó có nghĩa là lùi lên 1 thư mục cha. Ví dụ, có `/var/www/images/../` thì lúc này nó có nghĩa là `/var/www/`. Vậy thì với 3 lần `../` thì nó làm sao? Tất nhiên là nó nhảy đến thư mục cha trước `/var/` và lúc đó nó sẽ thường là thư mục `root` với kết quả là 1 dấu `/`. Với việc thêm `/etc/passwd` ở sau 3 lần `../` thì lúc này có nghĩa là ở thư mục gốc, mở folder `etc`, rồi mở `passwd`.
* Trong hệ điều hành Unix-based thì đó là file tiêu chuẩn bao gồm nhiều thông tin của của người dùng đã đăng ký trên máy chủ, nhưng kẻ tấn công có thể truy vấn các file khác một cách tùy tiện với cùng tư duy, cách làm kia.
* Trên Windows thì cả `../` và `..\` đều khả dụng trong các chuỗi thư mục. Ví dụ:
```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```
## Lab: File path traversal, simple case
* [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab1.md)

# 2. Access control
* Access control (hay "Kiểm soát truy cập") là việc một ứng dụng web hạn chế ai hay cái gì 

# Authentication

# Server-side request forgery (SSRF)

# File upload vulnerabilities

# OS command injection

# SQL injection
