# ÁP DỤNG TỪNG PHẦN

Bài đăng này là Phần 3 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 2](/combining-functions.md), chúng ta đã nói về việc kết hợp các hàm bằng nhiều cách khác nhau, kết thúc bằng các hàm `compose` và `pipe`, cho phép chúng ta áp dụng một loạt các hàm theo mô hình đường ống.

Trong bài viết đó, chúng ta đã xem xét các hàm đường ống đơn giản chỉ có một tham số. Vậy nếu chúng ta muốn sử dụng các hàm có nhiều hơn một tham số thì như thế nào?

Ví dụ: giả sử chúng ta có một bộ sưu tập các quyển sách và chúng ta muốn tìm các tiêu đề của tất cả sách được xuất bản trong một năm nhất định. Chúng ta hãy cùng viết bằng cách chỉ sử dụng các hàm lặp trên tập hợp của Ramda:

```
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(book => publishedInYear(book, year), books)

  return map(book => book.title, selected)
}
```

Sẽ là tốt nếu có thể kết hợp `filter` và `map` vào một đường ống, nhưng chúng ta không biết làm thế nào bởi vì `filter` và `map` có hai tham số.

Tốt hơn nữa là chúng ta không cần phải sử dụng một hàm mũi tên trong `filter`. Hãy giải quyết vấn đề đó trước vì nó sẽ chỉ cho chúng ta một số điều chúng ta có thể sử dụng để làm đường ống.

## HÀM BẬC CAO

Trong [Phần 1 của loạt bài này](//getting-started.md), chúng ta đã nói về các hàm như là các cấu trúc hạng nhất \(\)first-class. Các hàm có thể được truyền như các tham số cho các hàm khác và trả về như các kết quả từ các hàm khác. Chúng ta đã làm phần đầu tiên rất nhiều trước đây, nhưng đã chưa nhìn thấy phần sau đó.

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

May mắn thay, Ramda cung cấp hai hàm để giúp chúng ta: `partial` và một `partialRight`.

Hai hàm này cho phép chúng ta gọi bất kỳ hàm nào với ít tham số hơn nó cần. Cả hai đều trả lại một hàm mới nhận vào các tham số và sau đó gọi các hàm ban đầu một khi tất cả các tham số đã được cung cấp.

Sự khác biệt giữa `partial` và `partialRight` là có phải các tham số mà chúng ta cung cấp là các tham số bên trái nhất hoặc bên phải nhất cần thiết bởi hàm ban đầu.

Chúng ta hãy trở lại ví dụ ban đầu của chúng ta và sử dụng một trong những hàm này thay vì viết lại `publishedInYear`. Vì chúng ta chỉ muốn cung cấp số năm, và đó là tham số bên phải nhất, chúng ta cần phải sử dụng `partialRight`.

```
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(partialRight(publishedInYear, [year]), books)

  return map(book => book.title, selected)
}
```

Nếu chúng ta đã viết `publishedInYear` để nhận `(year, book)` thay vì `(book, year)`, chúng ta sẽ sử dụng `partial` thay vì `partialRight`.

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

Lưu ý rằng sự linh hoạt này dẫn đến một ảnh hưởng nhỏ về hiệu suất, bởi vì `curry` cần phải tìm hiểu hàm được gọi như thế nào và sau đó xác định cần phải làm gì. Nói chung, tôi chỉ thực hiện curry hàm khi tôi thấy tôi cần phải sử dụng `partial` ở nhiều nơi.

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

Lưu ý rằng để `curry` làm việc cho chúng ta, chúng ta đã phải đảo ngược thứ tự tham số. Điều này là rất phổ biến với lập trình hàm, do đó, hầu như mỗi hàm của Ramda được viết để cho các dữ liệu cần thiết để vận hành đi đến như là tham số cuối cùng.

Bạn có thể nghĩ về các tham số trước đó như là cấu hình cho tác vụ. Vì vậy, đối với `publishedInYear`, tham số `year` là cấu hình \(chúng ta đang tìm kiếm cái gì?\) Và tham số `book` là dữ liệu \(chúng ta đang tìm kiếm nó ở đâu?\).

Chúng ta đã thấy các ví dụ này với các hàm lặp trên tập hợp. Tất cả chúng đều nhận vào tập hợp như là tham số cuối cùng vì nó làm cho phong cách lập trình này dễ dàng hơn.

## CÁC THAM SỐ SAI THỨ TỰ

Điều gì sẽ xảy ra nếu chúng ta bỏ qua việc thay đổi thứ tự tham số của `publishedInYear`? Làm sao chúng ta vẫn có thể tận dụng được bản chất của curry?

Ramda cung cấp một vài lựa chọn.

### flip

Tùy chọn đầu tiên là `flip`. `flip` nhận một hàm của 2 hoặc nhiều tham số và trả về một hàm mới có các tham số tương tự, nhưng sẽ chuyển đổi thứ tự của hai tham số đầu tiên. Nó chủ yếu được sử dụng với hàm hai tham số, nhưng vẫn có thể được sử dụng trong các trường hợp tổng quát hơn.

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

Tùy chọn tổng quát hơn là tham số "placeholder" \(`__`\).

Điều gì sẽ xảy ra nếu chúng ta có một hàm ba tham số, và chúng ta muốn cung cấp các tham số đầu tiên và cuối cùng, để lại tham số giữa? Chúng ta có thể sử dụng placeholder cho tham số ở giữa:

```
const threeArgs = curry((a, b, c) => { /* ... */ })

const middleArgumentLater = threeArgs('value for a', __, 'value for c')
```

Bạn cũng có thể sử dụng placeholder nhiều lần trong một lần gọi. Ví dụ, nếu muốn chỉ cung cấp các tham số ở giữa?

```
const threeArgs = curry((a, b, c) => { /* ... */ })

const middleArgumentOnly = threeArgs(__, 'value for b', __)
```

Chúng ta có thể sử dụng placeholder thay vì `flip` nếu chúng ta thích:

```
const publishedInYear = curry((book, year) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(__, year), books)

  return map(book => book.title, selected)
}
```

Tôi nhận thấy phiên bản này dễ đọc hơn, nhưng nếu tôi cần sử dụng phiên bản "lật" \(flipped\) của `publishedInYear` nhiều, tôi có thể định nghĩa một hàm trợ giúp bằng cách sử dụng `flip`, và sau đó sử dụng hàm đó ở mọi nơi. Chúng ta sẽ thấy một số ví dụ về điều này trong các bài viết trong tương lai.

Lưu ý rằng `__` chỉ hoạt động cho các hàm curried, trong khi `partial`, `partialRight`, và `flip` chạy trên tất cả các hàm. Nếu bạn cần sử dụng `__` với một hàm bình thường, bạn luôn có thể bao quanh nó với một lệnh gọi để `curry` trước.

## HÃY LÀM MỘT ĐƯỜNG ỐNG

Hãy xem liệu chúng ta có thể di chuyển `filter` và `map` vào đường ống. Đây là trạng thái hiện tại của code, với thứ tự tham số thuận tiện cho `publishedInYear`:

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Chúng ta đã học được về `pipe` và `compose` trong bài viết trước đó, nhưng chúng ta cần thêm một thông tin nữa để có thể tận dụng điều đó.

Phần thông tin còn thiếu là: hầu như mọi hàm của Ramda đều được curried theo mặc định. Nó bao gồm `filter` và `map`. Vì vậy, `filter(publishedInYear(year))` là hoàn toàn hợp lệ và trả về một hàm mới mà chỉ cần chờ chúng ta truyền vào `books` sau đó, tiếp theo là `map(book => book.title)`.

Và bây giờ chúng ta có thể tạo ra đường ống:

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )(books)
```

Chúng ta hãy đi thêm một bước nữa và đảo ngược các tham số cho `titlesForYear` để phù hợp với quy ước của Ramda về dữ liệu ở vị trí sau cùng. Chúng ta cũng có thể curry hàm để cho phép sử dụng nó trong các đường ống sau này.

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

Bài viết này có lẽ là bài đi sâu nhất trong loạt bài này. Áp dụng từng phần và currying có thể mất một thời gian và nỗ lực để trở nên quen thuộc với bạn. Nhưng một khi bạn "hiểu ra" chúng, chúng sẽ giới thiệu cho bạn một cách rất mạnh mẽ để chuyển đổi dữ liệu của bạn theo cách lập trình hàm.

Chúng sẽ hướng bạn đến việc bắt đầu xây dựng các phép biến đổi bằng cách tạo ra những đường ống nhỏ, các khối xây dựng đơn giản.

## TIẾP THEO

Để viết code theo phong cách lập trình hàm, chúng ta cần bắt đầu suy nghĩ "declaratively" thay vì "imperatively". Để làm được điều đó, chúng ta sẽ cần phải tìm ra những cách thể hiện các cấu trúc mệnh lệnh chúng ta đã quen thuộc theo cách thức lập trình hàm. [Declarative programming](/declarative-programming.md) thảo luận về những ý tưởng này.

Nguồn: [Thinking in Ramda: Partial application](http://randycoulman.com/blog/2016/06/07/thinking-in-ramda-partial-application/)

