## Chương 6: Thực thi tầng domain

Ở tầng domain ta sẽ thực thi pattern design "chiến thuật", cụ thể là tầng domain sẽ tiến hành biểu thị `domain model` và chia chúng thành 2 loại như sau:
- Domain Object: entity, value-object, domain-event
- Những thứ sử dụng domain object: repository, factory, domain service

> Entity, value-object sẽ biểu thị những "sự vật" của domain model
> Domain-event sẽ biểu thị những "sự việc" của donain model

Sự khác nhau giữa `entity` và `value-object` nằm ở chỗ
- `Entity` sẽ được phân biệt bởi các ID
- `Value-Object` là giá trị dùng để đảm bảo sự phân biệt

### Entity

Ta lấy ví dụ: với ứng dụng quản lí sinh viên, mỗi sinh viên sẽ có một mã số sinh viên (MSSV) riêng và nó tương đương với ID. Khi một sinh viên A được cập nhật thành tích học tập hay địa chỉ thì sih viên A vẫn là sinh viên A. Cũng là một sinh viên A khác (tuy cùng tên nhưng MSSV khác nhau), thì sinh viên này là một thực thể khác biệt so với sinh viên A ở trên.

### Value-object

Lấy ví dụ với 2 tờ tiền 10,000. Hai tờ này được in vào 2 năm là 2022 và 2021. Về mặt giá trị thì chúng là tương đương nhau. Tuy nhiên nếu đưa chúng vào một bộ sưu tập tiền thì do context thay đổi nên dựa vào năm in ta sẽ phân biệt chúng như 2 "thực thể" khác nhau.

Hơn nữa giá trị của 2 tờ tiền này luôn là 10,000 - KHÔNG BAO GIỜ THAY ĐỔI

Điều này đồng nghĩa với việc

> Entity thì có thể thay đổi còn Value-Object thì bất biến

### Domain Service

Dùng khi `việc biểu thị model bằng một object là không thể`. Thông thường sẽ thao tác với một tập các objects.

VD: một ví dụ tiêu biểu đó là việc check xem mail có bị trùng lặp hay không - nói cách khác mail đã được sử dụng cho user trong hệ thống hay chưa. Bản thân một user object có thể biết được mail của mình nhưng không thể biết được thông tin về mail của object khác nên việc tự nó kiểm tra là điều không thể.

Những trường hợp như trên thường sẽ được xử lí bởi `domain service`.

Thế nhưng:

> Hãy cố gắng sử dụng entity và value-object nhiều nhất có thể và hạn chế tối đa việc sử dụng domain-service

Lí do là bởi nếu vô tình viết nhiều `business logic` vào đây thì trong tương lai nó sẽ trở thành một `Fat class` một cách không mong muốn.

### Repository

Repository dùng để lưu thông dữ liệu của một `kết tập` vào DB.

Các dữ liệu của một `kết tập` thường sẽ có tính gắn kết cao.

Một repository sẽ gắn với một `kết tập`, tuy nhiên việc truyền kết tập vào repository hay trả về kết tập từ repository đều phải thông qua `kết tập gốc` - `root aggregate`. Việc tham chiếu đến các object con bên trong aggregate đều phải thông qua `root aggregate` này.

Tuy nhiên không được phép trả về trực tiếp object con từ repository hoặc tạo một repository dùng riêng cho object con

> Repository sẽ được sử dụng như một LIST

Cụ thể như sau: Repository sẽ không chứa LIST các method như `Register User` hay `Suspend User` mà thay vào đó nó sẽ chứa **LIST các users** với các trạng thái như `Registing` hay `Suspending`. Việc làm này giúp cho repository không phải chứa quá nhiều các method liên quan đến business logic.

Hơn nữa cách làm trên cũng giúp ta dễ dàng thực hiện `In memory mock` khi tiến hành test. Do vậy nếu repository có chứa `business logic` thì sẽ không đảm bảo được tính chính xác khi test

### Factory

Được sử dụng để tạo ra object mới khi logic tạo object phức tạp hoặc trong trường hợp cần có sự tham chiếu đến một kết tập khác.

Factory cũng có thể được coi như một loại `domain service`

Do nó biểu thị domain logic nên nó cũng có thể tham chiếu hoặc sử dụng `repository`.

### Các object khác ở tầng domain

Ngoài cách thiết kế `chiến thuật` như đã nói ở trên, nếu đảm bảo `tính liên kết cao` và `tính phụ thuộc thấp` thì các cách thiết kế khác cũng hoàn toàn có thể được sử dụng.

Ví dụ như: `First class collection` hoặc sử dụng `enum` để biểu thị business logic.

> Điều quan trọng nhất với tầng domain đó là: biểu thị được domai logic và viết các object biểu thị các model

Nếu đảm bảo được 2 yếu tố trên thì dù là cách thiết kế hay triển khai nào cũng đều OK cả.

### Đảm bảo tính ràng buộc về dữ liệu giữa các kết tập

Có 2 cách:
① Thực thi ở tầng use-case: ở tầng này ta sẽ sử dụng các repository tương ứng với các kết tập để lưu dữ liệu. Cách này đơn giản tuy nhiên nếu use-case khác cũng thực hiện việc tương tự thì có thể dẫn tới xung đột dữ liệu

② Sử dụng domain event, cụ thể là ở tầng domain ta sẽ tạo ra các event để từ đó gọi đến các kết tập. Cách làm này luôn đảm bảo tính ràng buộc về dữ liệu tuy nhiên lại khá khó để triển khai.

### QA - Mục đích của việc đưa logic vào tầng domain

Khi đưa quá nhiều logic vào tầng domain có thể dẫn tới tình trạng các tầng phụ thuộc vào tầng domain này sẽ bị ảnh hưởng nếu tầng domain có sự thay đổi.

Ở đây ta có nguyên tắc về `sự phụ thuộc ổn định` - SDP: Stable Dependencies Principle. Nguyên tắc này nói rằng

> Sẽ rất khó để sửa 1 component mà có nhiều components khác phụ thuộc vào nó, thay vào đó hãy để chúng phụ thuộc vào các modules ít bị thay đổi

Mục đích chính của việc đưa tầng domain thành 1 tầng độc lập là để `dễ dàng thay đổi`.
Tầng domain luôn thay đổi để phản ánh đúng và đủ nội dung của nghiệp vụ vậy nên việc để tầng domain bị ảnh hưởng hay phụ thuộc vào các công nghệ như:
- Framework
- Database
thì sẽ dễ dẫn đến những tình trạng như: do framework hay db này không hỗ trợ tính năng này nên việc triển khai nghiệp vụ là điều không thể.

Khi điều đó diễn ra thường xuyên sẽ làm cho domain và nghiệp vụ thực tế bị xa rời nhau dẫn đến hệ thống làm ra không có khả năng giải quyết được vấn đề đang gặp phải.

Nếu sự chỉnh sửa domain làm ảnh hưởng đến các tầng khác thì quá trình test sẽ xử lí điều này.

### QA - Khả năng sử dụng các kiểu dữ liệu nguyên thuỷ

Ngoài việc sử dụng value-object, bạn cũng có thể cân nhắc việc sử dụng các kiểu dữ liệu nguyên thuỷ nếu thấy phù hợp.

### QA - Dữ liệu lấy ra từ DB nên được thể hiện như thế nào ở instance

Có thể tạo các `constructor` chuyên dụng, `constructor` này sẽ nhận dữ liệu đầu vào và giữ nguyên giá trị khi tạo instance.

Cũng cần chú ý về tính `private / public` của các constructors này.

### QA - Có thể viết các reconstruct methods ở tầng infra ?

Hoàn toàn có thể nhưng điều này nên được cân nhắc tuỳ theo ngôn ngữ lập trình được sử dụng.

Ví dụ với Java, nếu ta viết ở tầng infra thì sẽ không tận dụng được tính **reflection** về kiểu dữ liệu của nó.

### QA - Liệu domain object có cần chứa các thuộc tính giống y hệt như các cột của table trong DB hay không ?

Không nhất thiết. Điều quan trọng ở đây đó là **tầng infra phải phụ thuộc vào tầng domain** nên việc tầng domain mang những thuộc tính gì là do nghiệp vụ chứ không nhất thiết là phải giống như ở tầng infra.

### QA - Thực thi sắp xếp

Nên thực thi ở tầng infra. Repository interface sẽ có tính trừu tượng cao nên việc thực thi sắp xếp sẽ được triển khai ở tầng infra thông qua dấu hiệu là một `tham số` chỉ thứ tự sắp xếp chẳng hạn.

### QA - Thực thi cache

Nên cân nhắc khi thực thi với CQRS. Ta sẽ định nghĩa các query-model riêng dùng để query và sẽ thực thi cache nhằm tăng performance ở chỗ lấy thông tin cho query model.

### QA - Mối quan hệ giữa external API và Repository

Với các giá trị đưa vào external API và nhận về từ external API nếu nó có ý nghĩa với tầng domain thì ta cũng nên định nghĩa nó ở tầng domain.

Còn nếu không thì ta có thể tham khảo ví dụ về việc sử dụng notification sau. Việc nhận và sử dụng notification từ external API sẽ được thực hiện ở tầng usecase. Ta sẽ định nghĩa `NotificationAdapter` interface ở tầng này và implement nó ở tầng infra.

### QA - Cách lưu trữ, sử dụng dữ liệu lấy về từ external API

Bản thân Repository interface ở tầng domain mang tính trừu tượng cao, nó chỉ quan tâm `Đưa ra điều kiện nào và lấy về được dữ liệu nào` mà thôi, còn quá trình:
- Lấy dữ liệu
- Chuyển đổi dữ liệu
sẽ được thực thi ở tầng infra

### QA - Sự gắn kết giữa các entities

Lấy ví dụ: 1 user sẽ có N tasks thì khi thực thi ta nên chọn cách nào
① Task class sẽ chứa userId
② User class sẽ chứa list các tasks

Như đã biết **nếu trong cùng 1 kết tập thì sẽ tham chiếu theo instance, còn khác kết tập thì tham chiếu theo ID**. Do đó

① Sẽ áp dụng nếu `User` và `Task` không nằm trong cùng một kết tập

② Sẽ áp dụng khi `User` và `Task` nằm trong cùng một kết tập. Khi đó việc thêm, bớt task sẽ thực thi thông qua `Task List` và ta sẽ truyền toàn bộ dữ liệu có trong user instance vào repository để nó lưu vào DB. Thực tế thì việc "ép" `User` và `Task` vào cùng 1 kết tập đã là một điều không thể. Do đó để chuyển về thực thi phương án ① bạn cần phải thay đổi về phạm vi và cách thiết kế kết tập.

### QA - Lí do định nghĩa Repository Interface ở tầng domain

Mục đích chính là để thể hiện rõ phạm vi của kết tập tại tầng domain. Repository method cũng sẽ định nghĩa việc lưu trữ entity hoặc value-object dưới đơn vị gì.

Nếu định nghĩa Repository Interface ở tầng use-case thì khi use-case thay đổi thì phạm vi giới hạn của kết tập cũng có thể thay đổi theo.

Ngoài ra domain-service cũng không thể tham chiếu đến repository nếu repository được định nghĩa ở tầng use-case.

### QA - Update một phần của entity

Khi update entity ta cần phải truyền toàn bộ entity vào repository chứ không phải chỉ phần muốn update. Lí do là bởi repository bắt buộc phải thực hiện ở đơn vị kết tập.

Do tính ràng buộc về dữ liệu của kết tập sẽ được object `kết tập gốc` - `Aggregate Root` quản lí nên việc update một phần dữ liệu của kết tập mà không thông qua `Aggregate root` là điều không được phép.

### QA - Liệu có nên theo tác với repository từ entity

Điều này là không nên vì khi đó entity sẽ bị phình to quá mức dẫn đến việc khó bảo trì và đọc hiểu hệ thống.

### QA - Chỉ cần truyền ID khi muốn xoá entity

Đó là bởi nếu truyền một entity instance hoàn chỉnh thì sẽ cần phải lấy dữ liệu của entity đó từ DB về và điều này là điều vô nghĩa.
