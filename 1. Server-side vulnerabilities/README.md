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
### Lab: File path traversal, simple case
> [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab1.md)

# 2. Access control
* Access control (hay "Kiểm soát truy cập") là việc áp dụng các hạn chế đối với ai hay cái gì được quyền hành động hoặc truy cập tài nguyên. Trong một trang web, sự kiểm soát truy cập này sẽ phụ thuộc vào 2 yếu tố chính là Authentication (Xác thực) và Session Management (Quản lý phiên):
  * Authentication: Xác nhận rằng bạn chính là cái người mà họ nói. Ví dụ như FB, khi bạn đăng nhập thì cũng chính là việc bạn đang cho bên FB xác nhận lại là bạn có phải cái người dùng đó không, phải chủ tài khoản không. Có nhiều hình thức xác thực như kiểu: Vân tay, mã PIN, khuôn mặt, mã OTP,... Thì những cái này chính là Authentication.
  * Session management: Xác định các request HTTP tiếp theo được thực hiện bởi chính người dùng đó. Nói dễ hiểu thì là sau khi bạn đăng nhập FB rồi thì cái trang web đấy sẽ tạo ra 1 cái session của riêng bạn và nó sẽ ghi nhớ cái session đấy, khi đấy bạn sử dụng các chức năng của trang web thì nó sẽ ghi nhớ là "à! đây là bạn đang dùng chức năng này này. không cần phải hỏi lại mật khẩu nữa đâu nhỉ".
  * Access control: Cái này thì nó sẽ xác định xem xem là bạn có quyền, có được phép thực hiện những cái hành động mà bạn đang làm hay không. Kiểu mấy bài đăng trên FB set chỉ bạn bè, thì chỉ những người là bạn bè mới có quyền xem được bài đăng đấy, còn những người khác không có quyền xem
* Các kiểm soát truy cập bị lỗi rất phổ biến và thường là một lỗ hổng bảo mật nghiêm trọng. Việc thiết kế và quản lý các kiểm soát truy cập là một vấn đề phức tạp và biến đổi không ngừng, áp dụng các ràng buộc về kinh doanh, tổ chức và pháp lý vào việc triển khai kỹ thuật. Các quyết định thiết kế kiểm soát truy cập phải do con người đưa ra, nên khả năng xảy ra lỗi là rất cao

## Leo thang đặc quyền theo chiều dọc (gọi tắt là leo thang đặc quyền)
* Nếu một người dùng có thể giành được quyền truy cập vào một chức năng nào đấy mà cái chức năng đấy nó không cho phép người dùng kia quyền truy cập thì nó gọi là leo thang đặc quyền theo chiều dọc. Ví dụ, một người dùng bình thường có thể giành được quyền truy cập vào một trang của admin, có chức năng là xóa người dùng chẳng hạn, thì khi đó nó gọi là leo thang đặc quyền theo chiều dọc.

## Chức năng không được bảo vệ - Part 1
* Về cơ bản, những cái sự xuất hiện của leo thang đặc quyền chủ yếu là do không có bất kỳ sự bảo vệ nào được thực thi cho những chức năng nhảy cảm. Ví dụ, những cái chức năng của admin sẽ có ở trong trang của admintrator và không có ở trong trang của người dùng bình thường. Tuy nhiên, một người dùng có thể truy cập các chức năng quản trị bằng cách truy cập trực tiếp vào URL của trang quản trị (ví dụ: gõ website.com/admin vào thanh địa chỉ).
* Ví dụ, có cái web chứa các chức năng cho admin như này:
```
https://abcd.com/admin
```
* Nếu cái trang nhạy cảm này không có được sự bảo vệ thì với 1 người dùng bình thường, họ "vô tình" thử vào `/admin` và kết quả là họ vẫn vào và sử dụng được chức năng của admin.
* Trong một vài trường hợp, những cái URL thuộc về admin có thể được tiết lộ ở một vị trí khác, như file `robots.txt` chẳng hạn.
* Dù cho URL đó không được tiết lộ hay công khai ở bất cứ đâu thì những kẻ tấn công vẫn có thể sử dụng 1 cái wordlist để brute-force đường dẫn các cái web có chứa các chức năng nhạy cảm.

### Lab: Unprotected admin functionality
> [Link to lab](https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/access-control-apprentice/access-control/lab-unprotected-admin-functionality)
> 
> [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab2:%20Unprotected%20admin%20functionality.md)

## Chức năng không được bảo vệ - Part 2
* Trong một số trường hợp, những chức năng nhạy cảm này được che dấu bằng cách đưa ra những cái URL khó đoán hơn. Đó chính là ví dụ cho việc được gọi là "security by obscurity". Tuy nhiên, việc ẩn các chức năng nhạy cảm đấy không phải là một cơ chế kiểm soát truy cập hiệu quả vì người dùng vẫn có thể tìm ra được cái URL bị làm rối đấy bằng nhiều cách khác.
* Thử tưởng tượng một cái web có các chức năng của admin ở trong một trang có URL như này:
```
https://abcd.com/administrator-panel-yb556
```
* Kẻ tấn công không thể đoán được ngay lập tức. Tuy nhiên, trang web có thể vẫn leak cái URL cho người dùng. URL có thể bị tiết lộ trong đoạn mã JavaScript dùng để xây dựng giao diện người dùng dựa trên vai trò của họ:
```script
<script>
	var isAdmin = false;
	if (isAdmin) {
		...
		var adminPanelTag = document.createElement('a');
		adminPanelTag.setAttribute('href', 'https://abcd.com/administrator-panel-yb556');
		adminPanelTag.innerText = 'Admin panel';
		...
	}
</script>
```
* Cái script này thêm 1 cái link vào UI của người dùng nếu như người dùng đó là admin. Tuy nhiên, tất cả người dùng đều có thể nhìn thấy script chứa URL đó, bất kể họ có vai trò gì.

### Lab: Unprotected admin functionality with unpredictable URL
> [Link to lab](https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/access-control-apprentice/access-control/lab-unprotected-admin-functionality-with-unpredictable-url)
>
> [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab3:%20Unprotected%20admin%20functionality%20with%20unpredictable%20URL.md)

## Các phương pháp kiểm soát truy cập dựa trên tham số
* Một số ứng dụng xác định quyền truy cập hoặc vai trò của người dùng khi đăng nhập, và sau đấy lưu trữ lại những thông tin này ở một vị trí do người dùng kiểm soát. Đây có thể là:
	* Một trường ẩn (hidden field).
 	* Một cookie
  * Một tham số chuỗi truy vấn được thiết lập sẵn.
* Ứng dụng đưa ra quyết định kiểm soát truy cập dựa trên giá trị được gửi. Ví dụ:
```
https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1
```
* Cách tiếp cận này không an toàn vì người dùng có thể sửa đổi giá trị và truy cập vào các chức năng mà họ không được phép, chẳng hạn như các chức năng quản trị.

### Lab: User role controlled by request parameter
> [Link to lab](https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/access-control-apprentice/access-control/lab-user-role-controlled-by-request-parameter)
>
> [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab4%3A%20User%20role%20controlled%20by%20request%20parameter.md)

## Tấn công leo thang đặc quyền theo chiều ngang (Horizontal privilege escalation)
* Tấn công leo thang đặc quyền theo chiều ngang xảy ra khi một người dùng có thể truy cập vào tài nguyên của người dùng khác, thay vì chỉ truy cập vào tài nguyên cùng loại của chính họ. Ví dụ, nếu một nhân viên có thể truy cập hồ sơ của các nhân viên khác cũng như của chính mình, thì đây là leo thang đặc quyền theo chiều ngang.
* Các cuộc tấn công leo thang đặc quyền theo chiều ngang có thể sử dụng các phương thức khai thác tương tự như tấn công leo thang đặc quyền theo chiều dọc. Ví dụ, một người dùng có thể truy cập trang tài khoản của mình bằng URL sau:
```
https://insecure-website.com/myaccount?id=123
```
* Nếu kẻ tấn công sửa đổi giá trị tham số id thành của người dùng khác, họ có thể truy cập vào trang tài khoản của người dùng đó cùng với dữ liệu và các chức năng liên quan.

> Note: <br>
> Đây là một ví dụ về lỗ hổng tham chiếu đối tượng trực tiếp không an toàn (insecure direct object reference - IDOR). Loại lỗ hổng này phát sinh khi các giá trị tham số do người dùng kiểm soát được sử dụng để truy cập trực tiếp vào các tài nguyên hoặc chức năng.

* Trong một số ứng dụng, tham số có thể bị khai thác không có giá trị dễ đoán. Ví dụ, thay vì sử dụng một số tăng dần, một ứng dụng có thể sử dụng các định danh duy nhất toàn cục (GUID) để xác định người dùng. Điều này có thể ngăn kẻ tấn công đoán hoặc dự đoán định danh của người dùng khác. Tuy nhiên, GUID của những người dùng khác có thể bị lộ ở những nơi khác trong ứng dụng có đề cập đến người dùng, chẳng hạn như trong tin nhắn hoặc bài đánh giá của người dùng.

### Lab: User ID controlled by request parameter, with unpredictable user IDs 
> [Link to lab](https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/access-control-apprentice/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)
>
> [Link to solution](https://github.com/toanduneee/p0rtSw1gg3r/blob/main/1.%20Server-side%20vulnerabilities/Lab5:%20User%20ID%20controlled%20by%20request%20parameter,%20with%20unpredictable%20user%20IDs%20.md)

## Leo thang đặc quyền từ ngang sang dọc (Horizontal to vertical privilege escalation)
* Thông thường, một cuộc tấn công leo thang đặc quyền theo chiều ngang có thể được chuyển thành leo thang đặc quyền theo chiều dọc bằng cách xâm phạm một người dùng có đặc quyền cao hơn. Ví dụ, leo thang theo chiều ngang có thể cho phép kẻ tấn công đặt lại hoặc chiếm được mật khẩu của người dùng khác. Nếu kẻ tấn công nhắm mục tiêu vào một người dùng quản trị và xâm phạm tài khoản của họ, thì họ có thể giành được quyền truy cập quản trị và do đó thực hiện leo thang đặc quyền theo chiều dọc.
* Kẻ tấn công có thể giành được quyền truy cập vào trang tài khoản của người dùng khác bằng cách sử dụng kỹ thuật giả mạo tham số đã được mô tả cho leo thang đặc quyền theo chiều ngang:
```https://insecure-website.com/myaccount?id=456```
* Nếu người dùng mục tiêu là quản trị viên ứng dụng, thì kẻ tấn công sẽ có quyền truy cập vào trang tài khoản quản trị. Trang này có thể tiết lộ mật khẩu của quản trị viên hoặc cung cấp phương tiện để thay đổi nó, hoặc có thể cung cấp quyền truy cập trực tiếp vào các chức năng đặc quyền.

### Lab: User ID controlled by request parameter with password disclosure
> [Link to lab](https://portswigger.net/web-security/learning-paths/server-side-vulnerabilities-apprentice/access-control-apprentice/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure)
>
> Link to solution

# Authentication

# Server-side request forgery (SSRF)

# File upload vulnerabilities

# OS command injection

# SQL injection
