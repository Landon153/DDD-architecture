## Chương 3: Phương pháp mô hình hoá DDD

### Aggregate (Kết tập) là gì ?

Kết tập là một tập hợp các object có liên quan đến nhau chặt chẽ về mặt dữ liệu mà ta bắt buộc phải đảm bảo điều đó.

### Các quy tắc khi thiết kế và thực thi

Có 2 quy tắc chính ở đây:

① Việc đảm bảo tính thống nhất về mặt dữ liệu phải được đưa vào một kết tập duy nhất

② Bắt buộc chỉ có duy nhất 1 transaction

Với quy tắc ① ta xét ví dụ sau: các CLB ở trường học sẽ có các thành viên tham gia, khi số lượng thành viên đạt tới 5 thì trạng thái của CLB sẽ trở thành "full người" còn nhỏ hơn hoặc 4 sẽ là "còn chỗ".

![Screenshot 2024-05-01 at 21 49 19](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/f4597436-3e8f-4a6c-9a5b-a209e82b619f)


Lúc này trạng thái của CLB và số lượng thành viên trong CLB sẽ có mối liên hệ chặt chẽ.

Vậy nên `Club` và `Member` sẽ được cho vào cùng một "kết tập" còn `Student` sẽ thuộc về một kết tập khác.

Trong một kết tập, Object cha sẽ được gọi là `Root Aggregate` hoặc `Kết tập nguồn`.

![Screenshot 2024-05-01 at 22 05 18](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/5c7a63c7-9748-45a3-a717-1eea4250c501)

Với quy tắc thứ ② **ta phải lấy từ repository bằng một đơn vị kết tập và truyền vào repository cũng bằng một đơn vị kết tập**. Mục đích chính ở đây là để đảm bảo toàn vẹn về mặt dữ liệu của kết tập.

Như ở ví dụ trên nếu ta cho phép xoá từng thành viên trong CLB thì trạng thái của CLB sẽ trở thành `NOT_FULL`, điều này làm ảnh hưởng đến toàn vẹn dữ liệu.

Vậy nên mọi thao tác với `Member` đều phải thông qua kết tập `Club`.

### Cách xác định giới hạn của kết tập

Không có một công thức chuẩn nào cho việc xác định giới hạn của kết tập cả.

> Hãy cân nhắc giữa nhiều quan điểm và tìm ra giải pháp cân bằng nhất có thể

Về cơ bản của 2 quan điểm như sau:

**Nhấn mạnh vào tính toàn vẹn của dữ liệu**

Nếu nhấn mạnh vào tính toàn vẹn của dữ liệu thì ta có thể cân nhắc về tính toàn vẹn giữa các kết tập với nhau. Tuy nhiên việc triển khai điều này lại rất khó và tốn kém. Vậy nên việc triển khai tính toàn vẹn **trong một kết tập** hoặc **giữa nhiều kết tập** là phụ thuộc vào người thiết kế

**Phạm vi hợp lí của transaction**

Như đã nói ở trên, ta sẽ lấy về kết tập, update nó chỉ trong phạm vi 1 transaction. Vậy nên nếu phạm vi transaction quá lớn sẽ dẫn đến thời gian DB bị lock sẽ kéo dài.

Với ví dụ về CLB nói trên, nếu `Club` là con của object `School` thì khi ta update một `Club` thì `School` và các `Club` khác sẽ bị lock và không thể update được.

![Screenshot 2024-05-01 at 21 54 01](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/7f17b146-2ff0-4938-afaa-481125ad91d3)

### Giới hạn ngữ cảnh

Giới hạn ngữ cảnh có thể hiểu như sau:

> Là việc làm rõ giới hạn mà ta sẽ áp dụng cho một model cụ thể

2 ví dụ tiêu biểu ở đây đó là team, sub-system

Cùng lấy một ví dụ cụ thể hơn đó là việc **Chia sẻ Model**
Giả sử bạn đang build một hệ thống EC với sự tham gia của các bên:
- Dev
- Team bán hàng
- Team vận chuyển
- Team kế toán

Bạn đang thiết kế model `Product`, thế nhưng có một vấn đề đó là mỗi team sẽ có cách hiểu về `Product` khác nhau

![Screenshot 2024-05-01 at 21 56 41](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/7b191c0d-e11f-4459-baa5-9d9f74ae1a2c)

> Do đó khi quy mô của project ngày một lớn thì sẽ rất khó để tạo được một model thống nhất cho toàn bộ hệ thống

Như cuốn sách nổi tiếng của Evans về DDD có viết:

> Việc tạo dựng một model thống nhất cho toàn bộ hệ thống là việc bất khả thi cũng như gây tốn kém nhiều chi phí.

Do đó với DDD hãy **định nghĩa rõ ràng phạm vi áp dụng của model và trong từng phạm vi hãy dùng một model và một ngôn ngữ thống nhất**

Phạm vi được định nghĩa một cách rõ ràng này được gọi là `Giới hạn ngữ cảnh`.

Do đó với ví dụ về hệ thống EC phía trên ta sẽ chia thành 2 ngữ cảnh:
- Ngữ cảnh bán hàng
- Ngữ cảnh vận chuyển

![Screenshot 2024-05-01 at 21 58 09](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/c37ac11e-ce63-4587-a547-504071f576e9)

Với từng ngữ cảnh riêng ta sẽ sử dụng duy nhất `một model` và `một ngôn ngữ` trong ngữ cảnh đó mà thôi

### Suy nghĩ về DB

Khi thiết kế sơ đồ domain model, ta sẽ dễ dàng bị ảnh hưởng bởi những liên tưởng tới cấu trúc của DB. Để tránh điều này hãy nghĩ rằng bạn đang sử dụng NoSQL và mọi thứ trong hệ thống của bạn là object.

### Kết tập và Transaction

Khi tiến hành thiết kế sơ đồ domain model, hãy tránh những yếu tố kĩ thuật và chỉ tập trung vào việc thiết kế một sơ đồ mang tính trừu tượng cao. Nhưng khi thiết kế kết tập, hãy chú ý tới transaction.
