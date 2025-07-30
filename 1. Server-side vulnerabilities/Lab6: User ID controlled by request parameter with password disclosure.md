# Lab: User ID controlled by request parameter with password disclosure

## Yêu cầu
>  This lab has user account page that contains the current user's existing password, prefilled in a masked input. <br>
> To solve the lab, retrieve the administrator's password, then use it to delete the user `carlos`. <br>
> You can log in to your own account using the following credentials: `wiener:peter`

## Giải
- Đăng nhập acc `wiener:peter`
- Có thể thấy phần tham số `id` trong URL như này:
```
https://0000000.web-security-academy.net/my-account?id=wiener
```
- Vậy thì đăng nhập vào tài khoản admin thì thay `wiener` thành `administrator` là được
<img width="957" height="912" alt="image" src="https://github.com/user-attachments/assets/2d412166-e9ef-4427-be21-8d20b8da9d6e" />

- Có thể thấy đã vào được tài khoản của người dùng có username là administrator, bây giờ ta cần xem mật khẩu
- Vì nó là 1 phần có sẵn của trang web rồi thì có thể cùng `Ctrl + U` để xem source của trang
<img width="805" height="81" alt="image" src="https://github.com/user-attachments/assets/371b24cf-f490-4d31-b886-f977a3c392dd" />

- Dễ dàng thấy trường `password` cùng với phần `value` chính là giá trị bị ẩn đấy
- Bây giờ đăng xuất rồi đăng nhập lại vào tài khoản admin với pw này
<img width="957" height="675" alt="image" src="https://github.com/user-attachments/assets/fc0d31e6-e75a-452d-8419-9c5e514ba03f" />

- Và phần Admin panel đã xuất hiện, vào và xóa tài khoản `carlos` để hoàn thành lab
