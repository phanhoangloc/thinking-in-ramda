DRAFT VERSION

Bài đăng này là Phần 7 của loạt bài về lập trình chức năng được gọi là Tư duy trong Ramda.

Trong Phần 6, chúng ta đã nói về việc làm việc với các đối tượng JavaScript một cách có chức năng và không thay đổi.

Trong bài đăng này, chúng tôi sẽ làm tương tự cho các mảng.

Đọc phần tử mảng

Trong Phần 6, chúng ta đã học được về các hàm Ramda khác nhau để đọc thuộc tính đối tượng, bao gồm cả prop, pick, và có. Ramda có nhiều phương pháp để đọc các phần tử mảng.

Mảng tương đương của prop là thứ n; tương đương với chọn là slice, và tương đương với has đã chứa. Hãy nhìn vào những điều đó.

```
const numbers = [10, 20, 30, 40, 50, 60]

nth(3, numbers) // => 40  (0-based indexing)

nth(-2, numbers) // => 50 (negative numbers start from the right)

slice(2, 5, numbers) // => [30, 40, 50] (see below)

contains(20, numbers) // => true
```

Slice lấy hai chỉ mục và trả về mảng phụ bắt đầu từ chỉ mục đầu tiên \(dựa trên 0\) và bao gồm tất cả các phần tử được tạo ra, nhưng không bao gồm chỉ mục thứ hai.

Truy cập các phần tử thứ nhất \(nth \(0\)\) và last \(nth \(-1\)\) là khá phổ biến, vì vậy Ramda cung cấp các phím tắt cho những trường hợp đặc biệt, đầu và cuối. Nó cũng cung cấp các chức năng để truy cập vào phần tử all-but-the-first \(đuôi\), phần tử all-but-the-last \(init\), các phần tử N đầu tiên \(take \(N\)\) và các phần tử N cuối cùng \(takeLast \)\). Hãy xem những hành động này.

```
const numbers = [10, 20, 30, 40, 50, 60]

head(numbers) // => 10
tail(numbers) // => [20, 30, 40, 50, 60]

last(numbers) // => 60
init(numbers) // => [10, 20, 30, 40, 50]

take(3, numbers) // => [10, 20, 30]
takeLast(3, numbers) // => [40, 50, 60]
```

Thêm, cập nhật và xóa các phần tử mảng

Đối với các đối tượng, chúng ta đã học về assoc, dissoc, và bỏ qua để thêm, cập nhật và loại bỏ các thuộc tính.

Bởi vì các mảng là một cấu trúc dữ liệu đã ra lệnh, có một số phương pháp thực hiện cùng công việc như assoc. Phổ biến nhất là chèn và cập nhật, nhưng Ramda cũng cung cấp nối thêm và thêm vào trước cho trường hợp phổ biến để thêm các phần tử ở đầu hoặc cuối của mảng. chèn, nối thêm, và thêm vào trước thêm các phần tử mới vào mảng; cập nhật "thay thế" một phần tử bằng một giá trị mới.

Như bạn có thể mong đợi từ một thư viện chức năng, tất cả các chức năng này trở lại một mảng mới với những thay đổi mong muốn; mảng ban đầu là không bao giờ thay đổi.

```
const numbers = [10, 20, 30, 40, 50, 60]

insert(3, 35, numbers) // => [10, 20, 30, 35, 40, 50, 60]

append(70, numbers) // => [10, 20, 30, 40, 50, 60, 70]

prepend(0, numbers) // => [0, 10, 20, 30, 40, 50, 60]

update(1, 15, numbers) // => [10, 15, 30, 40, 50, 60]
```

Để kết hợp hai đối tượng thành một, chúng ta đã học về hợp nhất. Ramda cung cấp concat để làm tương tự với mảng.

```
const numbers = [10, 20, 30, 40, 50, 60]

concat(numbers, [70, 80, 90]) // => [10, 20, 30, 40, 50, 60, 70, 80, 90]
```

Lưu ý rằng mảng thứ hai được nối vào đầu tiên. Điều này có vẻ đúng khi được sử dụng bởi chính nó, nhưng như với hợp nhất, nó không thể làm những gì bạn mong đợi khi sử dụng trong một đường ống. Tôi tìm thấy nó hữu ích để xác định một chức năng trợ giúp, concat Sau khi: const concatAfter = flip \(concat\) để sử dụng trong đường ống.

Ramda cũng cung cấp một số tùy chọn để loại bỏ các phần tử. loại bỏ loại bỏ các yếu tố của chỉ số, trong khi không loại bỏ chúng theo giá trị. Ngoài ra còn có thả và thảLast cho trường hợp thông thường của việc loại bỏ các yếu tố từ đầu hoặc cuối của mảng.

```
const numbers = [10, 20, 30, 40, 50, 60]

remove(2, 3, numbers) // => [10, 20, 60]

without([30, 40, 50], numbers) // => [10, 20, 60]

drop(3, numbers) // => [40, 50, 60]

dropLast(3, numbers) // => [10, 20, 30]
```

Lưu ý rằng loại bỏ lấy một chỉ mục và một đếm trong khi đó slice mất hai chỉ số. Sự không thống nhất này có thể gây nhầm lẫn nếu bạn không biết đến nó.

Chuyển đổi các yếu tố

Giống như đối tượng, chúng ta có thể muốn cập nhật một phần tử mảng bằng cách áp dụng một hàm cho giá trị ban đầu.

```
const numbers = [10, 20, 30, 40, 50, 60]

update(2, multiply(10, nth(2, numbers)), numbers) // => [10, 20, 300, 40, 50, 60]
```

Để đơn giản hóa trường hợp sử dụng phổ biến này, Ramda cung cấp điều chỉnh hoạt động tương tự như tiến hóa đối với các đối tượng. Không giống như tiến triển, điều chỉnh chỉ hoạt động cho một phần tử mảng đơn.

```
const numbers = [10, 20, 30, 40, 50, 60]

adjust(multiply(10), 2, numbers)
```

Lưu ý rằng hai đối số đầu tiên để điều chỉnh được đổi chỗ khi so sánh với bản cập nhật. Đây có thể là một nguồn gây nhầm lẫn, nhưng có ý nghĩa khi bạn cân nhắc việc áp dụng một phần. Bạn có thể muốn cung cấp một hàm điều chỉnh với điều chỉnh \(nhân \(10\)\) và sau đó quyết định chỉ số và mảng nào áp dụng điều chỉnh đó.

Phần kết luận

Bây giờ chúng ta có các công cụ để làm việc với các mảng và các đối tượng theo một cách khai báo và không thay đổi. Điều này cho phép chúng ta xây dựng các chương trình trên các khối xây dựng nhỏ, có chức năng, kết hợp các chức năng để làm những gì chúng ta cần làm, tất cả mà không làm thay đổi cấu trúc dữ liệu của chúng ta.

Kế tiếp

Chúng ta đã học cách đọc, cập nhật và chuyển đổi các thuộc tính đối tượng và các phần tử mảng. Ramda cung cấp một công cụ tổng quát hơn để thực hiện các hoạt động này, ống kính. Ống kính cho thấy chúng hoạt động như thế nào.

