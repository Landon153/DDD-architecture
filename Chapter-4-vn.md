## Chương 4: Các nguyên tắc cơ bản trong thiết kế

Một trong những nguyên tắc quan trọng khi thiết kế đó là **Tính gắn kết cao, ràng buộc thấp**

### Về tính gắn kết và tính ràng buộc

Hai tính chất này được coi như `metrics` đối với chất lượng của phần mềm. Hai yếu tố này giúp cải thiện những đặc tính dưới đây của phần mềm:
- Tính dễ hiểu khi đọc code
- Khả năng mở rộng
- Độ tin cậy cao: khó xảy ra bug khi sửa code
- Tính tái sử dụng: một chức năng code có thể được tái sử dụng ở nhiều chỗ
- Khả năng dễ test

### Tính gắn kết

Có thể hiểu là sự liên quan, liên kết giữa `business logic`, `data` và `method`. Sự liên kết này càng lớn thì càng tốt.

`Business logic` chính là việc class này có nhiệm vụ gì ?, thực hiện chức năng gì ?

Nếu `business logic` không rõ ràng thì thiết kế đang khá tồi cũng như sau này sẽ không tạo được sự gắn kết cao với `data` và `method`.

### Ví dụ về tính gắn kết thấp

```TS
class OperationUtil {           // Tên class khá mơ hồ về chức năng của nó
  private count: number = 0;

  public void increment() {     // Đang increment cái gì và từ đâu ?
    count++;
  }

  public static void greet() {  // Method không liên quan đến biến count ở trên
    console.log('Hello');
  }
}
```

Khi không định nghĩa rõ về `business logic` của class thì sẽ xảy ra tình trạng thêm cái method không liên quan vào từ đó làm tăng chi phí maintain code.

### Ví dụ về tính gắn kết cao

```TS
class Counter {                        // Tên class khá rõ ràng về nhiệm vụ
  private count: number = 0;          // Tên biến chứa dữ liệu liên quan

  public void increment() {           // method liên quan đến count và class name
    count++;
  }

  public getCurrentCount(): number {  // method liên quan đến count và class name
    return count;
  }
}
```

### Tính ràng buộc

Thể hiện sự phụ thuộc giữa các class với nhau.

**Tính gắn kết** áp dụng cho phạm vi `nội bộ 1 class`
**Tính ràng buộc** áp dụng cho `nhiều classes`

Trong thực tế ta luôn muốn rằng tính ràng buộc giữa các class phải thấp nhất có thể vì nếu tính ràng buộc cao thì chỉ cần có sự thay đổi ở 1 class thì các classes còn lại cũng sẽ bị ảnh hưởng theo.

Ngoài ra việc thực thi bắt buộc phải có sự kết hợp giữa nhiều classes cũng được coi là `có tính ràng buộc cao`.

### Ví dụ về tính ràng buộc cao

```TS
class Printer {
  public static void print() {
    console.log(Counter.count);   // Phụ thuộc vào giá trị thuộc tính count của class Counter
  }
}

class Counter {
  public static count: number = 0;
  public static void increment() {
    count++;
  }
}

Printer.print();   // output: 0
Counter.count();
Printer.print();  // output: 1 → nhìn từ bên ngoài sẽ khó để hiểu lí do tại sao kết quả lại tăng lên 1.
```

### Ví dụ về tính ràng buộc thấp

```TS
class Printer {
  public static void print(count: number) {
    console.log(count);
  }
}

class Counter {
  public static count: number = 0;
  public static getCount(): number {
    return count;
  }
}

Printer.print(Counter.getCount());
```

Ở ví dụ trên hai class `Printer` và `Counter` chỉ có muốn liên hệ thông qua việc truyền tham số từ đó đảm bảo được tính ràng buộc thấp.
