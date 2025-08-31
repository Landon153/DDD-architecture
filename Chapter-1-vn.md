## Chuơng 1: Các khái niệm cơ bản về DDD

### Domain, Model, DomainModel, DataModel, Modeling là gì ?

`Domain` là lĩnh vực mà phần mềm sẽ giải quyết một vấn đề của nó.
`Model` là một hệ thống các abstractions mô tả một khía cạnh của domain và có thể được sử dụng để giải quyết vấn đề liên quan đến domain đó.
`Modeling` là quá trình tạo ra một model.
`Domain Model`: là model dùng để giải quyết vấn đề của domain.
`Data model`: là model dùng để giải quyết vấn đề liên quan đến việc lưu trữ dữ liệu.

### Đưa những gì vào model ?

Nên cân nhắc những gì cần thiết phải đưa vào model cũng như những gì không cần thiết phải đưa vào model.

Ta lấy ví dụ với model `Resume`, ta cần đưa vào nó những thông tin `quan trọng` sau đây:
- Tên
- Quá trình làm việc / công tác
- Lí do apply
- Ảnh mặt

Còn những yếu tố dưới đây được coi là những yếu tố `không quan trọng`:
- Chữ kí
- Resume maker
- Cảm xúc khi viết Resume

### Để software kế thừa những gì có ở model 

Nếu model và software triển khai nó có một sự khác biệt đáng kể thì việc giải quyết vấn đề (vốn dĩ là nhiệm vụ chính của model) sẽ ngày một khó hơn.

Không những thế, việc đọc code để hiểu được model cũng trở nên khó khăn hơn.

Do đó hãy giữ cho code và model ở "gần nhau" nhất có thể, và cũng vì lí do này mà DDD hỗ trợ OOP rất mạnh.

### Kiến trúc "cách li" model

Các xử lí giao tiếp với `UI` và `DB` không nên "được trộn lẫn" vào phần code của model. Lí do là vì khi đó:
- Lượng code của model sẽ tăng lên rất nhiều
- Giảm đi tính đọc hiểu của code
- Giảm đi tính bảo trì của code

Do đó các xử lí liên quan đến `UI` hay `DB`, ... và code của model nên được **phân chia thành các tầng riêng biệt**. Tầng đảm nhiệm model được gọi là `tầng domain`.

Về cơ bản ta sẽ thấy 3 loại kiến trúc sau thường xuất hiện trong thực tế:
- Layer Architecture
- Onion Architecture
- Clean Architecture

### Những pattern thiết kế "chiến thuật"

Ta có thể thấy những thiết kế rất tốt với:
- Entity
- Repository
những thiết kế này được gọi là `Light DDD`, thế nhưng nếu chúng không giúp software giải quyết được những bài toàn trong thực tế thì cũng không có ý nghĩa gì cả.

Thế nên trong thực tế hãy áp dụng:
- Các pattern thiết kế
- Modeling
cùng nhau để dần dần cải thiện thiết kế của bạn nhằm mục đích giúp software giải quyết được một vấn đề trong thực tế.

### Bắt đầu nhỏ, Thất bại nhỏ

Việc tạo ra một model hoàn chỉnh ngay từ đầu là chuyện không thể. Do đó hãy tiến hành vòng tuần hoàn sau:

MODELING → TRY → FIX

Cho đến khi bạn tìm ra được một model ưng ý nhất cho mình. Hơn nữa nếu bạn cần phải đưa ra một thiết kế áp dụng cho nhiều ứng dụng cùng một lúc thì điều này là một điều vô cùng khó và dễ gây chán nản, hãy bắt đầu từ một phần nhỏ trong ứng dụng. Sau khi cảm thấy ổn hãy scale dần lên.
