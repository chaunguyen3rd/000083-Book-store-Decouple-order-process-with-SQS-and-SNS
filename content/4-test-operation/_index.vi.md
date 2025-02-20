---
title : "Kiểm tra hoạt động web"
date :  2025-02-11
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Trong bước này, chúng ta sẽ kiểm tra hoạt động của Web.

Bạn có thể tải xuống các tệp hình ảnh tại đây để thêm dữ liệu kiểm tra hoạt động của các dịch vụ.
    {{%attachments title="Images" pattern=".*\.(jpg|png)$"/%}}

1. Nhập các liên kết sau vào một tab mới trong trình duyệt web của bạn: ``http://www.DOMAIN``, thay thế tất cả DOMAIN bằng tên miền của bạn. Tất cả các liên kết này sẽ chuyển hướng đến đường dẫn mới, thay thế http bằng https.
    ![TestOperation](/images/temp/1/8.png?width=90pc)

2. Nhấp vào **Create new book**.
    - Nhập ID: ``1``.
    - Nhập tên sách: ``Java``.
    - Nhập tác giả: ``Jame Patterson``.
    - Nhập thể loại: ``IT``.
    - Nhập giá: ``10.98``.
    - Nhập mô tả: ``Hướng dẫn cho người mới bắt đầu học các kiến thức cơ bản về Java``.
    - Nhấp vào nút **Choose File** và chọn hình ảnh **LetGoBook.png** mà bạn vừa tải xuống.
    - Nhấp vào nút **Create**.
      ![TestOperation](/images/temp/1/69.png?width=90pc)
    - Nhấp vào nút **OK** khi cửa sổ bật lên xuất hiện.
      ![TestOperation](/images/temp/1/70.png?width=90pc)

3. Tạo sách mới như bước trước.
    - Nhấp vào **Create new book**.
    - Nhập ID: ``2``.
    - Nhập tên sách: ``Let's Go``.
    - Nhập tác giả: ``Alex Edwards``.
    - Nhập thể loại: ``IT``.
    - Nhập giá: ``15.8``.
    - Nhập mô tả: ``Hướng dẫn từng bước để tạo web nhanh, an toàn với Go``.
    - Nhấp vào nút **Choose File** và chọn hình ảnh **LetGoBook.png** mà bạn vừa tải xuống.
    - Nhấp vào nút **Create**.
      ![TestOperation](/images/temp/1/71.png?width=90pc)
    - Nhấp vào nút **OK** khi cửa sổ bật lên xuất hiện.
      ![TestOperation](/images/temp/1/70.png?width=90pc)

4. Quay lại trang chủ.
    - Nhấp vào **Home**.
    - Nhấp vào nút **Add to cart** để thêm cả 2 cuốn sách vào giỏ hàng.
    - Sau đó, nhấp vào biểu tượng **Cart** ở góc trên bên phải.
      ![TestOperation](/images/temp/1/72.png?width=90pc)

5. Tại trang **Cart Items**.
    - Nhấp vào nút **Checkout**.
      ![TestOperation](/images/temp/1/73.png?width=90pc)
    - Sau đó, nhấp vào nút **OK**.
      ![TestOperation](/images/temp/1/74.png?width=90pc)

6. Mở [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Nhấp vào **Queues** trên menu bên trái.
    - Nhấp vào hàng đợi **checkout-queue**.
      ![TestOperation](/images/temp/1/75.png?width=90pc)
    - Tại trang **checkout-queue**, nhấp vào nút **Send and receive messages**.
      ![TestOperation](/images/temp/1/76.png?width=90pc)
    - Tại trang **Send and receive messages**, nhấp vào nút **Poll for messages**.
      ![TestOperation](/images/temp/1/77.png?width=90pc)
    - Sau đó, nhấp vào tin nhắn hiển thị.
      ![TestOperation](/images/temp/1/78.png?width=90pc)
    - Kiểm tra cửa sổ bật lên **Message: ...** và nhấp vào nút **Done**.
      ![TestOperation](/images/temp/1/79.png?width=90pc)

7. Mở email mà bạn đã đăng ký để nhận thông báo.
    ![TestOperation](/images/temp/1/80.png?width=90pc)

8. Quay lại tab ứng dụng.
    - Nhấp vào **Orders** và kiểm tra các cuốn sách bạn đã thêm vào giỏ hàng.
      ![TestOperation](/images/temp/1/81.png?width=90pc)

9. Tiếp theo, lặp lại bước **5** để thêm một số đơn hàng khác theo ý muốn.

10. Mở tab ứng dụng.
    - Nhấp vào **Orders** và kiểm tra các cuốn sách bạn đã thêm vào giỏ hàng.
      ![TestOperation](/images/temp/1/82.png?width=90pc)
    - Nhấp vào nút **Handle** và sau đó nhấp vào nút **OK** trên cửa sổ bật lên.
      ![TestOperation](/images/temp/1/83.png?width=90pc)

11. Mở [AWS DynamoDB](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
    - Nhấp vào **Tables** trên menu bên trái.
    - Chọn bảng **OrdersTable**.
      ![TestOperation](/images/temp/1/86.png?width=90pc)
    - Tại trang **OrdersTable**, nhấp vào nút **Explore table items**.
      ![TestOperation](/images/temp/1/87.png?width=90pc)
    - Bạn có thể thấy hai cuốn sách trong đơn hàng mà bạn đã nhấp vào nút **Handle** trước đó.
      ![TestOperation](/images/temp/1/88.png?width=90pc)

12. Quay lại tab ứng dụng.
    - Nhấp vào **Orders** và kiểm tra các cuốn sách bạn đã thêm vào giỏ hàng.
    - Tiếp theo, nhấp vào nút **Delete** và sau đó nhấp vào nút **OK** trên cửa sổ bật lên.
      ![TestOperation](/images/temp/1/84.png?width=90pc)
    - Các mục đã xóa không còn hiển thị nữa.
      ![TestOperation](/images/temp/1/85.png?width=90pc)
