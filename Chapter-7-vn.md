## Chương 7: Thực thi tầng use-case

### Use-case

Use-case class sẽ sử dụng các `public method` của entity được định nghĩa ở tầng domain để thực hiện một use-case của hệ thống.

Ví dụ về use-case thay đổi trạng thái của `Task`

```TS
class TaskPostponeUsecase {
  constructor(private taskRepository: TaskRepository) {}

  execute(taskId: number) {
    const taskEntity: TaskEntity = this.taskRepository.findById(taskId);
    taskEntity.postpone();
    this.taskRepository.save(taskEntity);
  }
}
```

Việc làm này giúp tăng tính trừu tượng cho các method ở phía tầng domain khi tầng use-case chỉ cần quan tâm đến việc sử dụng method mà không cần biết cách triển khai của nó là gì.

> Việc sử dụng các public method của entity để triển khai use-case là nhiệm vụ chính của tầng use-case

### Return Value Class từ Usecase

Về việc truyền giá trị trả về từ tầng use-case xuống tầng presentation, ta có 2 cách làm như sau:

① Tạo một class chuyên dùng để chứa kiểu dữ liệu truyền xuống này

② Truyền nguyên domain object xuống tầng presentation

Về cách ① có ưu, nhược điểm như sau:

**Ưu điểm**
- Tránh việc tầng presentation có sự xuất hiện của các method chứa domain business logic
- Tránh việc sửa đổi tầng domain ảnh hướng đến tầng presentation

**Nhược điểm**
- Chi phí để chuyển đổi kiểu dữ liệu sẽ tăng

Cách ② có ưu và nhược điểm ngược lại hoàn toàn với cách ① trên.

Nếu thực thi cách thứ ② thì domain object cần có các `method phục vụ cho việc hiển thị`, từ đó sẽ làm cho domain object bị phình to, khó bảo trì. Vậy nên nếu áp dụng cách ② thì thay vì tốn công chuyển đổi dữ liệu thì cái giá phải trả cũng lớn không kém.

**Việc đặt tên cho return value class**

Bạn có thể đặt tên theo format **XXXDTO** - **DTO** là **Data Transfer Object**

![Screenshot 2024-05-02 at 23 20 54](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/fcd98f68-7f12-47b3-bdd1-2ee38f2b2138)

### QA - Phân chia class ở tầng use-case

Thông thường sẽ là `1 class - 1 public method`

Trong trường hợp cần xử lí chung thì ta sẽ tách xử lí chung thành 1 class riêng để các classes khác cũng có thể tham chiếu tới.

VD: converter class

### QA - Cách đặt tên class

Việc này sẽ tuỳ theo project mà có sự khác biệt.

Với các classes có nhiều method thì ta sẽ bỏ đi động từ theo kèm (VD: `TaskUsecase`)
Với các classes có 1 method thì ta vẫn sẽ giữ nguyên động từ (VD: `CreateTaskUsecase`)

Việc đặt tên nên được bắt đầu khi tiến hành thiết kế sơ đồ usecase (nghĩ ra usecase và nhiệm vụ cho usecase đó).
