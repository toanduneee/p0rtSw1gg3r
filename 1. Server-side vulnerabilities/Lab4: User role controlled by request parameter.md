# Lab: Unprotected admin functionality with unpredictable URL

## Yêu cầu
> This lab has an admin panel at `/admin`, which identifies administrators using a forgeable cookie.
> <br> Solve the lab by accessing the admin panel, and using it to delete the user `carlos`. 
> <br> You can log in to your own account using the following credentials: `wiener:peter`

## Giải
* Đầu tiên, đăng nhập vào với account `wiener:peter`
<img width="957" height="433" alt="image" src="https://github.com/user-attachments/assets/0ec2e27b-306e-433c-b122-f681d87003f1" />

* Kiểm tra phần cookie của người dùng này, có thể thấy vai trò `Admin` đang được set là `false`:
<img width="400" alt="image" src="https://github.com/user-attachments/assets/7d2cd438-9380-4fb3-a12d-474bbeb13ce2" />

* Vậy nếu ta chỉnh nó thành `true` thì sao?
<img width="957" height="623" alt="image" src="https://github.com/user-attachments/assets/2e540b55-1ad0-4084-b3dc-f0d3a7578cb4" />

* Có thể thấy sau khi chỉnh thành `true` thì web này xác định người dùng `wiener` này đã là admin, và có hiện thêm mục nữa `Admin panel` (hay trong đề bài có nhắc đến là trang `/admin`.
<img width="957" height="281" alt="image" src="https://github.com/user-attachments/assets/3d2b3f24-2cc6-4759-8a19-aeb1fb587d22" />

* Xóa người dùng `carlos` là hoàn thành.
