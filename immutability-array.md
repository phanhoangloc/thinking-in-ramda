# TÍNH BẤT BIẾN VÀ MẢNG

Bài đăng này là Phần 7 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 6](//immutability-object.md), chúng ta đã nói về làm việc với các đối tượng trong JavaScript theo cách functional và bất biến.

Trong bài viết này, chúng ta sẽ làm tương tự cho các mảng.

## ĐỌC PHẦN TỬ MẢNG

Trong [Phần 6](//immutability-object.md), chúng ta đã học về các hàm Ramda khác nhau để đọc thuộc tính đối tượng, bao gồm cả `prop`, `pick`, và `has`. Ramda có nhiều phương pháp để đọc các phần tử mảng.

Tương đương của `prop` là `nth`; tương đương với `pick` là `slice`, và tương đương với `has` là `contains`. Hãy nhìn vào những điều đó.

```
const numbers = [10, 20, 30, 40, 50, 60]

nth(3, numbers) // => 40  (0-based indexing)

nth(-2, numbers) // => 50 (negative numbers start from the right)

slice(2, 5, numbers) // => [30, 40, 50] (see below)

contains(20, numbers) // => true
```

`slice` lấy hai index và trả về mảng phụ bắt đầu từ index đầu tiên \(0-based\) và bao gồm tất cả các phần tử được tạo ra, nhưng không bao gồm index thứ hai.

Truy cập các phần tử đầu tiên \(`nth(0)`\) và cuối cùng \(`nth(-1)`\) là khá phổ biến, vì vậy Ramda cung cấp các phím tắt cho những trường hợp đặc biệt, `head` và `last`. Nó cũng cung cấp các hàm để truy cập vào phần tử all-but-the-first \(`tail`\), phần tử all-but-the-last \(`init`\), N phần tử đầu tiên \(`take(N)`\) và N phần tử N cuối cùng \(`takeLast(N)`\). Hãy xem những hàm này.

```
const numbers = [10, 20, 30, 40, 50, 60]

head(numbers) // => 10
tail(numbers) // => [20, 30, 40, 50, 60]

last(numbers) // => 60
init(numbers) // => [10, 20, 30, 40, 50]

take(3, numbers) // => [10, 20, 30]
takeLast(3, numbers) // => [40, 50, 60]
```

## THÊM, CẬP NHẬT VÀ XÓA CÁC PHẦN TỬ MẢNG

Đối với các đối tượng, chúng ta đã học về `assoc`, `dissoc`, và `omit` để thêm, cập nhật và loại bỏ các thuộc tính.

Bởi vì các mảng là một cấu trúc dữ liệu theo trình tự, có một số phương pháp thực hiện cùng công việc như `assoc`. Phổ biến nhất là `insert` và `update`, nhưng Ramda cũng cung cấp `append` và `prepend`  cho trường hợp phổ biến để thêm các phần tử ở đầu hoặc cuối của mảng. `insert`, `append`, và `prepend` thêm các phần tử mới vào mảng; `update` "thay thế" một phần tử bằng một giá trị mới.

Như bạn có thể mong đợi từ một thư viện lập trình hàm, tất cả các hàm này trả lại một mảng mới với những thay đổi mong muốn; mảng ban đầu là không bao giờ thay đổi.

```
const numbers = [10, 20, 30, 40, 50, 60]

insert(3, 35, numbers) // => [10, 20, 30, 35, 40, 50, 60]

append(70, numbers) // => [10, 20, 30, 40, 50, 60, 70]

prepend(0, numbers) // => [0, 10, 20, 30, 40, 50, 60]

update(1, 15, numbers) // => [10, 15, 30, 40, 50, 60]
```

Để kết hợp hai đối tượng thành một, chúng ta đã học về `merge`. Ramda cung cấp `concat` để làm tương tự với mảng.

```
const numbers = [10, 20, 30, 40, 50, 60]

concat(numbers, [70, 80, 90]) // => [10, 20, 30, 40, 50, 60, 70, 80, 90]
```

Lưu ý rằng mảng thứ hai được nối vào mảng đầu tiên. Điều này có vẻ đúng khi được sử dụng bởi chính nó, nhưng như với `merge`, nó không thể làm những gì bạn mong đợi khi sử dụng trong một đường ống. Tôi nhận thấy nó hữu ích để xác định một hàm trợ giúp, `concatAfter`: `const concatAfter = flip(concat)` để sử dụng trong đường ống.

Ramda cũng cung cấp một số tùy chọn để loại bỏ các phần tử. `remove` loại bỏ các phần tử theo index, trong khi `without` loại bỏ chúng theo giá trị. Ngoài ra còn có `drop` và `dropLast` cho trường hợp thông dụng cần loại bỏ các phần tử đầu hoặc cuối của mảng.

```
const numbers = [10, 20, 30, 40, 50, 60]

remove(2, 3, numbers) // => [10, 20, 60]

without([30, 40, 50], numbers) // => [10, 20, 60]

drop(3, numbers) // => [40, 50, 60]

dropLast(3, numbers) // => [10, 20, 30]
```

Lưu ý rằng `remove` nhận một index và một số đếm trong khi đó `slice` nhận hai index. Sự không thống nhất này có thể gây nhầm lẫn nếu bạn không biết đến nó.

## CHUYỂN ĐỔI CÁC PHẦN TỬ

Giống như đối tượng, chúng ta có thể muốn cập nhật một phần tử mảng bằng cách áp dụng một hàm cho giá trị ban đầu.

```
const numbers = [10, 20, 30, 40, 50, 60]

update(2, multiply(10, nth(2, numbers)), numbers) // => [10, 20, 300, 40, 50, 60]
```

Để đơn giản hóa trường hợp sử dụng phổ biến này, Ramda cung cấp `adjust` hoạt động tương tự như `evolve` đối với các đối tượng. Không giống như `evolve`, `adjust` chỉ hoạt động cho một phần tử mảng đơn.

```
const numbers = [10, 20, 30, 40, 50, 60]

adjust(multiply(10), 2, numbers)
```

Lưu ý rằng hai đối số đầu tiên của `adjust` được đổi chỗ khi so sánh với `update`. Đây có thể là một nguồn gây nhầm lẫn, nhưng nó có ý nghĩa khi bạn cân nhắc việc áp dụng từng phần. Bạn có thể muốn cung cấp một hàm điều chỉnh với `adjust(multiply(10))` và sau đó quyết định index và mảng nào áp dụng hàm điều chỉnh đó.

## KẾT LUẬN

Bây giờ chúng ta có các công cụ để làm việc với các mảng và các đối tượng theo một cách declarative và bất biến. Điều này cho phép chúng ta xây dựng các chương trình trên các khối xây dựng nhỏ, có hàm, kết hợp các hàm để làm những gì chúng ta cần mà không làm thay đổi cấu trúc dữ liệu của chúng ta.

## TIẾP THEO

Chúng ta đã học cách đọc, cập nhật và chuyển đổi các thuộc tính đối tượng và các phần tử mảng. Ramda cung cấp một công cụ tổng quát hơn để thực hiện các hoạt động này, ống kính \(lens\). [Lenses](//lenses.md) cho chúng thấy nó hoạt động như thế nào.

Nguồn: [Thinking in Ramda: Immutability and Arrays](http://randycoulman.com/blog/2016/07/05/thinking-in-ramda-immutability-and-arrays/)

