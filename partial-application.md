# ÁP DỤNG TỪNG PHẦN

Bài đăng này là Phần 3 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 2](/combining-functions.md), chúng ta đã nói về việc kết hợp các hàm bằng nhiều cách khác nhau, kết thúc bằng các hàm `compose` và `pipe`, cho phép chúng ta áp dụng một loạt các hàm theo mô hình đường ống.

Trong bài viết đó, chúng ta đã xem xét các hàm đường ống đơn giản chỉ có một tham số. Vậy nếu chúng ta muốn sử dụng các hàm có nhiều hơn một tham số thì như thế nào?

Ví dụ: giả sử chúng ta có một bộ sưu tập các đối tượng sách và chúng tôi muốn tìm các tiêu đề của tất cả sách được xuất bản trong một năm nhất định. Chúng ta hãy cùng viết bằng cách chỉ sử dụng các hàm lặp trên tập hợp của Ramda:

```
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(book => publishedInYear(book, year), books)

  return map(book => book.title, selected)
}
```

Sẽ là tốt nếu kết hợp `filter` và `map` vào một đường ống, nhưng chúng ta không biết làm thế nào bởi vì `filter` và `map` có hai đối số.

Nó cũng sẽ là tốt nếu chúng ta không cần phải sử dụng một hàm mũi tên trong `filter`. Hãy giải quyết vấn đề đó trước vì nó sẽ dạy cho chúng ta một số điều chúng ta có thể sử dụng để làm đường ống.

## HÀM BẬC CAO

Trong [Phần 1 của loạt bài này](//getting-started.md), chúng ta đã nói về các hàm như là các cấu trúc first-class. Các hàm có thể được truyền như các tham số cho các hàm khác và trả về như các kết quả từ các hàm khác. Chúng ta đã làm phần đầu tiên rất nhiều trước đây, nhưng đã không nhìn thấy phần sau.

Các hàm nhận hoặc trả về các hàm khác được gọi là "các hàm bậc cao" \(higher order functions\).

Trong ví dụ ở trên, chúng ta truyền hàm mũi tên để `filter`: `book => publishedInYear(book, year)`, và chúng ta muốn cố gắng loại bỏ mũi tên. Để làm được điều đó, chúng ta cần một hàm mà nó nhận vào một cuốn sách và trả về giá trị đúng nếu cuốn sách được xuất bản trong một năm nhất định. Nhưng chúng ta cũng cần phải truyền vào số năm để làm cho hàm này linh hoạt.

Cách chúng ta có thể giải quyết vấn đề này là thay đổi `publishedInYear` thành một hàm trả về một hàm khác. Tôi sẽ viết nó với cú pháp chức năng đầy đủ để bạn có thể xem những gì xảy ra, nhưng sau đó tôi sẽ cho bạn thấy phiên bản ngắn hơn bằng cách sử dụng cú pháp mũi tên:

```
// Full function version:
function publishedInYear(year) {
  return function(book) {
    return book.year === year
  }
}

// Arrow function version:
const publishedInYear = year => book => book.year === year
```

Với phiên bản mới của `publishedInYear`, chúng ta có thể viết lại lệnh gọi `filter`, loại bỏ hàm mũi tên:

```
const publishedInYear = year => book => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Bây giờ, khi chúng ta gọi `filter`, `publishedInYear(year)` được đánh giá ngay lập tức, trả về một hàm nhận vào `book`, đó chính là những gì `filter` cần.

## CÁC HÀM ÁP DỤNG TỪNG PHẦN

Chúng ta có thể viết lại bất kỳ hàm nhiều tham số theo cách này nếu chúng ta muốn, nhưng chúng ta không sở hữu tất cả các hàm mà chúng ta có thể muốn sử dụng. Ngoài ra, chúng ta có thể muốn sử dụng một số hàm đa đối số theo cách thông thường.

Ví dụ: nếu chúng ta có một số code khác chỉ muốn kiểm tra xem một cuốn sách đã được xuất bản trong một năm nhất định hay không, chúng ta muốn gọi `publishedInYear(book, 2012)` nhưng chúng ta không thể làm được điều đó được nữa. Thay vào đó, chúng ta phải gọi `publishedInYear(2012)(book)`. Nó khó đọc và gây phiền toái hơn.

May mắn thay, Ramda cung cấp hai chức năng để giúp chúng ta: `partial` và một `partialRight`.

Hai chức năng này cho phép chúng ta gọi bất kỳ hàm nào với ít tham số hơn nó cần. Cả hai đều trả lại một hàm mới nhận vào các tham số và sau đó gọi các hàm ban đầu một khi tất cả các tham số đã được cung cấp.

Sự khác biệt giữa `partial` và `partialRight` là có phải các tham số mà chúng ta cung cấp là các tham số bên trái nhất hoặc bên phải nhất cần thiết bởi hàm ban đầu.

Chúng ta hãy trở lại ví dụ ban đầu của chúng ta và sử dụng một trong những hàm này thay vì viết lại `publishedInYear`. Vì chúng ta chỉ muốn cung cấp số năm, và đó là tham số bên phải nhất, chúng ta cần phải sử dụng `partialRight`.

```
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(partialRight(publishedInYear, [year]), books)

  return map(book => book.title, selected)
}
```

Nếu chúng tôi đã viết `publishedInYear` để nhận `(year, book)` thay vì `(book, year)`, chúng tôi ta sẽ sử dụng `partial` thay vì `partialRight`.

Lưu ý rằng các đối số chúng ta cung cấp cho `partial` và `partialRight` phải luôn ở trong một mảng, ngay cả khi chỉ có một phần tử. Tôi không thể nói với bạn bao nhiêu lần tôi đã quên điều đó và kết thúc với một thông báo lỗi khó hiểu:

```
First argument to _arity must be a non-negative integer no greater than ten
```

## CURRY

Phải sử dụng `partial` và `partialRight` ở mọi nơi dẫn đến sự rườm rà và tẻ nhạt. Tuy nhiên, việc phải gọi hàm số nhiều tham số như một chuỗi các hàm đơn lẻ cũng không tốt.

May mắn thay, Ramda cung cấp cho chúng ta một giải pháp: `curry`.

[Currying](https://en.wikipedia.org/wiki/Currying) là một khái niệm cốt lõi trong lập trình hàm. Về mặt kỹ thuật, một hàm curried luôn là một chuỗi các hàm đơn tham số, đó cũng là điều tôi vừa phàn nàn. Trong ngôn ngữ lập trình hàm thuần túy, cú pháp nói chung làm cho nó trông không khác gì gọi một hàm với nhiều tham số.

Nhưng bởi vì Ramda là một thư viện JavaScript, và JavaScript không có cú pháp tốt để gọi một loạt các hàm đơn tham số, các tác giả đã nới lỏng định nghĩa truyền thống của currying một chút.

Trong Ramda, một hàm curried có thể được gọi với một tập hợp con các tham số, và nó sẽ trả về một hàm mới chấp nhận các tham số còn lại. Nếu bạn gọi một hàm curried với tất cả các tham số của nó, nó sẽ thực thi hàm đó.

Bạn có thể nghĩ đến một hàm curried như là sự kết hợp tốt nhất của cả hai cách tiếp cận: bạn có thể gọi nó bình thường với tất cả các tham số của nó. Hoặc bạn có thể gọi nó với một tập hợp con các tham số, và nó sẽ hoạt động như thể bạn sử dụng `partial`.

Lưu ý rằng sự linh hoạt này mang đến một cải thiện hiệu suất nhỏ, bởi vì `curry` cần phải tìm hiểu hàm được gọi như thế nào và sau đó xác định cần phải làm gì. Nói chung, tôi chỉ thực hiện curry hàm khi tôi thấy tôi cần phải sử dụng `partial` ở nhiều nơi.

Chúng ta hãy áp dụng `curry` với hàm `publishedInYear`. Lưu ý rằng `curry` luôn hoạt động theo cách bạn đã từng sử dụng `partial`; không có phiên bản cho `partialRight`. Chúng ta sẽ nói về điều đó ở bên dưới, nhưng bây giờ, chúng ta sẽ đảo ngược các tham số cho `publishedInYear` để số năm như tham số đầu tiên.

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Chúng ta có thể một lần nữa gọi `publishedInYear` với số năm và trả lại một hàm, nhận vào một cuốn sách và thực hiện hàm ban đầu. Tuy nhiên, chúng ta vẫn có thể gọi nó theo cách thông thường `publishedInYear(2012, book)` mà không gây phiền toái với cú pháp `)(`.

## THỨ TỰ THAM SỐ

Lưu ý rằng để `curry` làm việc cho chúng ta, chúng ta đã phải đảo ngược thứ tự tham số. Điều này là rất phổ biến với lập trình hàm, do đó, hầu như mỗi hàm của Ramda được viết để cho các dữ liệu được vận hành đi đến như là tham số cuối cùng.

Bạn có thể nghĩ về các tham số trước đó như là cấu hình cho tác vụ. Vì vậy, đối với `publishedInYear`, tham số `year` là cấu hình \(chúng ta đang tìm kiếm cái gì?\) Và tham số `book` là dữ liệu \(chúng ta đang tìm kiếm nó ở đâu?\).

Chúng ta đã thấy các ví dụ này với các hàm lặp trên tập hợp. Tất cả chúng đều nhận vào tập hợp như là tham số cuối cùng vì nó làm cho phong cách lập trình này dễ dàng hơn.

## CÁC THAM SỐ SAI THỨ TỰ

Điều gì sẽ xảy ra nếu chúng ta đã bỏ qua thứ tự tham số của `publishedInYear`? Làm sao chúng ta vẫn có thể tận dụng được bản chất của curry?

Ramda cung cấp một vài lựa chọn.

### flip

Tùy chọn đầu tiên là `flip`. `flip` nhận một hàm của 2 hoặc nhiều tham số và trả về một hàm mới có các tham số tương tự, nhưng sẽ chuyển đổi thứ tự của hai tham số đầu tiên. Nó chủ yếu được sử dụng với hàm hai đối số.

Sử dụng `flip`, chúng ta có thể quay trở lại thứ tự tham số ban đầu của `publishedInYear`:

```
const publishedInYear = curry((book, year) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(flip(publishedInYear)(year), books)

  return map(book => book.title, selected)
}
```

Trong hầu hết các trường hợp, tôi muốn ưu tiên sử dụng thứ tự tham số thuận tiện hơn, nhưng nếu bạn cần sử dụng một hàm mà bạn không kiểm soát, `flip` là một lựa chọn hữu ích.

### placeholder

Tùy chọn tổng quát hơn là đối số "giữ chỗ" \(\_\_\).

Điều gì sẽ xảy ra nếu chúng ta có một chức năng của ba đối số, và chúng ta muốn cung cấp các đối số đầu tiên và cuối cùng, để lại cho trung gian một lần nữa? Chúng ta có thể sử dụng trình giữ chỗ cho đối số trung gian:

```
const threeArgs = curry((a, b, c) => { /* ... */ })

const middleArgumentLater = threeArgs('value for a', __, 'value for c')
```

Bạn cũng có thể sử dụng trình giữ chỗ nhiều lần trong cuộc gọi. Ví dụ, nếu muốn chỉ cung cấp các đối số trung gian?

```
const threeArgs = curry((a, b, c) => { /* ... */ })

const middleArgumentOnly = threeArgs(__, 'value for b', __)
```

Chúng ta có thể sử dụng kiểu giữ chỗ thay vì lật nếu chúng ta thích:

```
const publishedInYear = curry((book, year) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(__, year), books)

  return map(book => book.title, selected)
}
```

Tôi tìm thấy phiên bản này dễ đọc hơn, nhưng nếu tôi cần sử dụng phiên bản "lật" của publishedInYear rất nhiều, tôi có thể định nghĩa một hàm trợ giúp bằng cách sử dụng lật, và sau đó sử dụng chức năng trợ giúp ở mọi nơi. Chúng ta sẽ thấy một số ví dụ về điều này trong các bài viết trong tương lai.

Lưu ý rằng \_\_ chỉ hoạt động cho các chức năng chèn, trong khi phần, partialRight, và lật tất cả các công việc trên bất kỳ chức năng. Nếu bạn cần sử dụng \_\_ với một chức năng bình thường, bạn luôn có thể quấn nó với một cuộc gọi để cà ri đầu tiên.

## HÃY LÀM MỘT ĐƯỜNG ỐNG

Hãy xem liệu chúng ta có thể di chuyển các bộ lọc và các cuộc gọi bản đồ vào đường ống. Đây là trạng thái hiện tại của mã, với trật tự thuận tiện cho publishInYear:

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Chúng tôi đã học được về đường ống và sáng tác trong bài đăng cuối cùng, nhưng chúng tôi cần thêm một thông tin nữa để chúng tôi có thể tận dụng việc học đó.

Phần thông tin còn thiếu là: hầu như mọi chức năng Ramda đều được kích hoạt theo mặc định. Điều này bao gồm bộ lọc và bản đồ. Vì vậy, bộ lọc \(publishedInYear \(year\)\) là hoàn toàn hợp pháp và trả về một chức năng mới mà chỉ cần chúng ta chờ chúng ta vượt qua các cuốn sách dọc theo sau, như là map \(book =&gt; book.title\).

Và bây giờ chúng ta có thể viết đường ống:

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )(books)
```

Chúng ta hãy đi thêm một bước nữa và đảo ngược các đối số cho TitleForYear để phù hợp với công ước của Ramda về dữ liệu-cuối cùng. Chúng ta cũng có thể curry chức năng để cho phép sử dụng nó trong các đường ống sau này.

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = curry((year, books) =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )(books)
)
```

## KẾT LUẬN

Bài đăng này có lẽ là bài sâu nhất trong loạt bài này. Một phần ứng dụng và currying có thể mất một thời gian và nỗ lực để quấn quanh đầu của bạn. Nhưng một khi bạn "nhận được" chúng, họ sẽ giới thiệu cho bạn một cách rất mạnh mẽ để chuyển đổi dữ liệu của bạn một cách có chức năng.

Họ dẫn bạn bắt đầu xây dựng các phép biến đổi bằng cách tạo ra những đường ống nhỏ, các khối xây dựng đơn giản.

## TIẾP THEO

Để viết mã theo kiểu chức năng, chúng ta cần bắt đầu suy nghĩ "declaratively" thay vì "imperatively". Để làm được điều đó, chúng ta sẽ cần phải tìm ra những cách thể hiện các cấu trúc bắt buộc chúng ta đã quen với cách thức chức năng. Lập trình tuyên bố thảo luận về những ý tưởng này.

Nguồn: [Thinking in Ramda: Partial application](http://randycoulman.com/blog/2016/06/07/thinking-in-ramda-partial-application/)

