# Lab: File path traversal, simple case

## Yêu cầu:
> This lab has an unprotected admin panel.
> 
> Solve the lab by deleting the user `carlos`. 

## Giải:
* Để giải quyết bài này thì ta cần phải tìm được trang admin panel, xong rồi xóa đi user `carlos`.
* Trong lý thuyết được nhắc đến thì có nói đến `/robots.txt`
* File `robots.txt` dễ hiểu thì là file văn bản cơ bản được đặt trong thư mục gốc của trang web có tác dụng điều hướng cho mấy con bot đi crawl dữ liệu của google, được phép hay không được phép lấy dữ liệu ở một trang được liệt kê trong đấy. [Thông tin thêm](https://developers.google.com/search/docs/crawling-indexing/robots/intro?hl=en)
* Thử duyệt đến trang `/robots.txt` thì ta sẽ thấy được như này:
<img width="1045" height="203" alt="ảnh" src="https://github.com/user-attachments/assets/da0c93e1-5520-4d66-84bb-e8b47a48854b" />

* Có thể thấy có 1 trang `/administrator-panel` và khả năng cao đây chính là trang panel chúng ta đang tìm.
<img width="1765" height="587" alt="ảnh" src="https://github.com/user-attachments/assets/7a32c083-6779-4524-8f5b-624df7bb8f8d" />

* Xóa `carlos` là xong lab.
