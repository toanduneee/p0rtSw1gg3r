# Lab: User ID controlled by request parameter, with unpredictable user IDs

## Yêu cầu
>  This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs. <br>
> To solve the lab, find the GUID for `carlos`, then submit his API key as the solution. 
> <br> You can log in to your own account using the following credentials: `wiener:peter`

## Giải
* Đăng nhập tài khoản `wiener:peter`
<img width="957" height="490" alt="image" src="https://github.com/user-attachments/assets/0a6e7ba2-1bf1-4fe7-bba9-d83c343fa9bf" />

* Về `Home` xong tìm bài đăng của `carlos`
<img width="945" height="719" alt="image" src="https://github.com/user-attachments/assets/1c7e1b73-2786-4bd7-a538-01405ade5d23" />

* Vào tài khoản của `carlos` xem xem thì có thể thấy phần `userId` bị lộ ngay trong chính URL
```
https://0000000000.web-security-academy.net/blogs?userId=b868b236-8283-429a-87a0-db1765dc15bc
```
* Lấy phần `userId` đấy rồi về lại phần `My account`, sửa đổi phần `id` trong URL của mình thành `userId` kia của carlos
```
https://0000000000.web-security-academy.net/my-account?id=b868b236-8283-429a-87a0-db1765dc15bc
```
<img width="958" height="392" alt="image" src="https://github.com/user-attachments/assets/3bb5453f-8987-40d6-81e5-5d03edc91773" />

