## Chương 2: Từ modeling đến implementation

### Sơ đồ usecase

Là sơ đồ mô tả các hành động của hệ thống nhằm đáp ứng yêu cầu từ phía người dùng.
Lấy ví dụ với ứng dụng quản lí task, ta sẽ có sơ đồ usecase như sau:

![Screenshot 2024-05-01 at 21 11 49](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/871c0a6e-2867-4019-9f2e-a874c1a6978a)

`Sơ đồ use-case` sẽ được tạo ra trước `sơ đồ domain model`. Vậy tại sao ta lại cần sơ đồ use-case. Sau đây là lí do:
- Nếu ta không rõ được các use-cases thì việc thiết kế model sẽ trở nên khó khăn. Ở ví dụ trên nếu đứng ở góc độ của người làm việc thì quá trình quản lí task sẽ khác so với góc độ quản lí task của người quản lí (manager)

Ngoài ra ta cũng cần phải quyết định scope của `sơ đồ domain model`, lí dó là vì nếu không quyết định scope của domain model thì có thể dẫn tới tình trạng phạm vi của vấn đề sẽ trở nên quá rộng và quá trình thiết kế domain model sẽ không đem lại hiệu quả gì cả.

Khi bắt đầu ta có thể bắt đầu domain model với scope nhỏ, sau đó ta sẽ dần dần scale scope của nó lên cũng được. Khi đó ta cũng cần phải scale scope của sơ đồ use-case lên để tạo sự đồng bộ.

### Sơ đồ domain model

Về bản chất đây là sơ đồ được đơn giản hoá từ sơ đồ class. Nó sẽ chứa các yếu tố sau:
- Các properties tiêu biểu của object, không nhất thiết phải viết ra các methods
- Ghi rõ các rules cũng như business logic
- Biểu thị tính liên quan giữa các objects
- Định nghĩa tính đa dạng
- Định nghĩa phạm vi của Aggregate
- Ghi ví dụ cụ thể để dễ hình dung

Sau khi thiết kế Aggregate xong ta có thể quyết định cách thiết kế cũng như triển khai các Repositories.

> Bất kể với sơ đồ use-case hay sơ đồ domain model thì khi tiến hành trao đổi ý kiến với các thành viên khác trong team, ta đều nên viết ra white board để mọi người có thể cùng nhau trao đổi ý kiến và hoàn thành các sơ đồ trên.

Với sơ đồ use-case về ứng dụng quản lí task, ta có thể đưa ra sơ đồ domain model như sau:
![Screenshot 2024-05-01 at 21 18 37](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/a046a6c0-af0d-42c5-a744-9b107867b93f)

Ta có thể thấy rằng 1 user có thể có 0..n task và cũng có thể thấy được các rules khi khởi tạo hoặc thay đổi user, task.

### Cách ghi chép các rules / business logic

Với các rules hay business logic thì khi ta có ý tưởng hay một sáng kiến nào đó về chúng, hãy viết ra dưới dạng các gạch đầu dòng.

Tiêu biểu ở đây ta có thể viết dưới dạng `sơ đồ chuyển trạng thái`. Trình bày dưới dạng sơ đồ sẽ dễ hiểu hơn so với viết chữ rất nhiều. Với những trường hợp như vậy ta có thể kết hợp giữa `sơ đồ chuyển trạng thái` với `sơ đồ domain model`.

### Cách ghi chép Aggregate

Khi ghi chép để biểu thị các Aggregate (kết tập), ta cần thể hiện được mối liên hệ giữa các objects, do đó việc phân biệt các object ở trong / ngoài kết tập là rất quan trọng do chúng sẽ có cách biểu thị khác nhau.
- Trong cùng một kết tập thì ta sẽ dùng kí hiệu composition để trỏ đến object được tham chiếu
- Khác kết tập thì ta sẽ dùng kí hiệu `association` để trỏ đến object được tham chiếu

**Lưu ý**: 2 kí hiệu `association`, `composition` đều là kí hiệu dùng trong UML. (Ref: https://github.com/tuananhhedspibk/TeckStack/issues/95)

### DDD Amenia

Đây là thuật ngữ nói về việc model hầu như không chứa bất kì business logic nào cả.

### Sử dụng Onion Architecture

Ở đây ta định nghĩa theo từng tầng (tầng nào sẽ dùng để triển khai class nào).

![Screenshot 2024-05-01 at 21 26 17](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/69f72072-9277-4698-83b5-4179ee477465)


### DDD Amenia Code

```TS
class TaskEntity {
  private id: number;
  private status: TaskStatus;
  private name: string;
  private dueDate: Date;
  private postponeCount: number;
}
// Ở đây giả sử mỗi property đều có các getter và setter tương ứng
// VD: setStatus, getStatus, setName, getName, ...
```

Thoạt nhìn ta thấy class này khá OK, nhưng nếu nhìn vào use-case có sử dụng entity class này như dưới đây ta có thể thấy sẽ phát sinh một số vấn đề khác.

```TS
class TaskCreateUsecase {
  execute(name: string, dueDate: Date) {
    if (!name || !dueDate) {
      throw new UsecaseError();
    }

    const task = new Task();
    task.setStatus(TaskStatus.UNDONE);
    task.setName(name);
    task.setDueDate(dueDate);
  }
}
```

Mọi thứ vẫn rất OK, thế nhưng nếu có một class như sau:

```TS
class EvilTaskUsecase {
  execute() {
    task.setStatus(TaskStatus.DONE);
    task.setName('');
  }
}
```

Thì sẽ phát sinh 2 vấn đề:

**1. Dữ liệu bị ghi đè "không thương tiếc"**
Nguyên nhân là do các setter đều là public method, nên từ các use-case khác ta vẫn có thể thay đổi dữ liệu của entity.

**2. Việc truy vết, đọc code sẽ khó khăn**
Giả sử nếu ta muốn giải quyết vấn đề xung đột dữ liệu nêu trên, ta cần phải truy vết việc tham chiếu đến các setter ở rất nhiều chỗ. Từ đó dẫn đến chi phí đọc code, bảo trì code sẽ tăng lên.

Không những thế từ code để hiểu được model cũng sẽ rất khó nếu viết model như trên.

### Nên viết domain knowledge ở tầng nào ?

Như ví dụ trên ta thấy nếu viết domain knowledge ở tầng use-case sẽ phát sinh những vấn đề khá nghiêm trọng. Do đó thay vì viết ở tầng use-case như hình bên dưới

![Screenshot 2024-05-01 at 21 31 46](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/16f625c6-02ac-4cd9-b55d-6e573f606aac)


Hãy tập trung viết chúng vào tầng domain

![Screenshot 2024-05-01 at 21 32 51](https://github.com/tuananhhedspibk/tuananhhedspibk.github.io/assets/15076665/12e554cd-174c-4540-b0fb-cc244ed8f107)


Nếu viết domain knowledge vào tầng domain thì tầng use-case trông sẽ như thế này.

```TS
class TaskCreateUseCase {
  private taskRepository: TaskRepository;

  execute(name: string, dueDate: Date) {
    const taskEntity = new TaskEntity(name, dueDate);
    taskRepository.save(taskEntity);
  }
}
```

```TS
class TaskPostponeUseCase {
  private taskRepository: TaskRepository;

  execute(id: number) {
    const taskEntity = taskRepository.findById(id);
    taskEntity.postpone();
  }
}
```

Lúc này code ở tầng use-case đã trở nên trừu tượng hơn rất nhiều. Bản thân nó đã:
- `ẨN ĐI` cách thực thi (HOW)
- `CHỈ BIỂU THỊ` làm cái gì (WHAT)

Code của entity trong tầng domain sẽ trông như thế này.

```TS
class TaskEntity {
  private id: number;
  private taskStatus: TaskStatus;
  private name: string;
  private postponeCount: number;
  private dueDate: Date;

  constructor(name: string, dueDate: Date) {
    this.name = name;
    this.dueDate = dueDate;
    this.taskStatus = TaskStatus.UNDONE;
    this.postponeCount = 0;
  }

  postpone() {
    // implementation
  }
}
```

Lúc này mọi public setter đều bị khai trừ, nên ta sẽ tránh được việc thiết lập các giá trị không mong muốn.

### Nguyên tắc cơ bản khi thiết kế Object ở tầng Domain

Có 2 nguyên tắc cơ bản sau:

① Ghi mọi domain knowledge vào object

② Chỉ cho phép những instances chuẩn chỉ được tồn tại

Với nguyên tắc ②, bất cứ khi nào lưu dữ liệu vào DB, ta cũng cần xác thực xem dữ liệu của domain model object có bị xung đột hay không. Để thực hiện việc xác thực này ta có 2 cách sau:

① Áp đặt điều kiện khi tạo instance

② Áp đặt điều kiện chỉnh sửa

Với ① ta sẽ tiến hành tạo instance thông qua `constructor` hoặc `factory method`, bằng cách này ta có thế áp đặt các điều kiện về mặt dữ liệu khi tạo instance từ đó tránh được tình trạng xung đột dữ liệu.
Cần tránh tình trạng sử dụng `default constructor` (các constructor này thường không setup bất cứ giá trị gì cho các property) vì khi đó nó sẽ không đảm bảo được việc tránh xung đột dữ liệu cho instance.

Với ② ta sẽ áp đặt các điều kiện về dữ liệu đối với các hàm chuyển trạng thái của entity instance. Khi đó ta có thể công khai các hàm này ra bên ngoài.
Với các setter thì hoàn toàn ngược lại, chúng vi phạm quy tắc về tránh xung đột dữ liệu. Ở ví dụ trên đó là method `postpone`

Việc tuân thủ 2 nguyên tắc trên đây sẽ giúp:
- Tăng tính dễ đọc cho code
- Tăng khả năng bảo trì cho code

### Quản lí Ubiquitous Language như thế nào ?

Ubiquitous được tạo ra trong quá trình tạo Sơ đồ Domain Model. Vậy nên thay vì định nghĩa chúng ở một văn bản riêng, hãy định nghĩa chung với sơ đồ domain model để dễ dàng cập nhật và bảo trì.
