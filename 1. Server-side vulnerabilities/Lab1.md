# Lab: File path traversal, simple case

## Yêu cầu:
> This lab contains a path traversal vulnerability in the display of product images.
> To solve the lab, retrieve the contents of the `/etc/passwd` file.

## Giải:
- Nhiệm vụ của chúng ta là tìm cách để mở nội dung trong file `/etc/passwd`.
- Lướt qua thì có thể thấy đây là 1 web shop bán đồ
- Và như ở lý thuyết trước nói thì web này tại url `image` có tham số `filename` để gọi lên các hình ảnh có chưa trong 1 thư mục nào đó.
<img width="958" height="374" alt="image" src="https://github.com/user-attachments/assets/d3514cb8-b365-4f7a-837a-496c4970bfed" />

- Vậy để có thể file `/etc/passwd` thì chỉ cần đơn giản là thay đổi giá trị của `filename`
<img width="600" alt="image" src="https://github.com/user-attachments/assets/2630d6b7-b3b6-431b-b1bf-6754138f8ad1" />

- Có thể thấy hành vi này có tác động được đến, nhưng không tìm thấy file
- Vậy như lý thuyết trước, ta có thể sử dụng `../` để lùi lại thư muc cha
<img width="600" alt="image" src="https://github.com/user-attachments/assets/a78372bb-40fd-4048-b8c7-c9b3633e94a5" />

- Và ta đã có thể mở được file passwd
