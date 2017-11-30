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

Bây giờ, khi chúng ta gọi `filter`, `publishedInYear(year)` được đánh giá ngay lập tức, trả về một nhận vào `book`, đó chính là những gì `filter` cần.

## Các chức năng ứng dụng một phần

Chúng ta có thể viết lại bất kỳ hàm số nhiều tham số theo cách này nếu chúng ta muốn, nhưng chúng ta không sở hữu tất cả các chức năng mà chúng ta có thể muốn sử dụng. Ngoài ra, chúng ta có thể muốn sử dụng một số chức năng đa đối số theo cách thông thường.

Ví dụ: nếu chúng tôi có một số mã khác chỉ muốn kiểm tra xem một cuốn sách đã được xuất bản trong một năm nhất định hay không, chúng tôi muốn nói đã phát hànhNăm học \(sách, 2012\) nhưng chúng tôi không thể làm được điều đó nữa. Thay vào đó, chúng ta phải công bố tạp chí InYear \(2012\) \(book\). Đó là ít có thể đọc được và gây phiền nhiễu hơn.

May mắn thay, Ramda cung cấp hai chức năng để giúp chúng tôi: một phần và một phần Right.

Hai chức năng này cho phép chúng ta gọi bất kỳ hàm nào với ít đối số hơn nó cần. Cả hai đều trả lại một chức năng mới có mất các đối số và sau đó gọi các chức năng ban đầu một khi tất cả các đối số đã được cung cấp.

Sự khác biệt giữa partial và partialRight là liệu các đối số mà chúng ta cung cấp là các đối số trái hoặc hầu hết các đối số cần thiết bởi chức năng ban đầu.

Chúng ta hãy trở lại ví dụ ban đầu của chúng ta và sử dụng một trong những chức năng này thay vì viết lại các bài báo đã xuất bản. Vì chúng ta chỉ muốn cung cấp một năm, và đó là lập luận đúng nhất, chúng ta cần phải sử dụng partialRight.

```
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(partialRight(publishedInYear, [year]), books)

  return map(book => book.title, selected)
}
```

Nếu chúng tôi đã viết được xuất bảnNên để lấy \(năm, sách\) thay vì \(sách, năm\), chúng tôi sẽ sử dụng một phần thay vì partialRight.

Lưu ý rằng các đối số chúng ta cung cấp cho phần và từng phần Right phải luôn ở trong một mảng, ngay cả khi chỉ có một trong số chúng. Tôi không thể nói với bạn bao nhiêu lần tôi đã quên và kết thúc với một thông báo lỗi khó hiểu:

```
First argument to _arity must be a non-negative integer no greater than ten
```

## Curry

Phải sử dụng phần và từng phần ở mọi nơi đều bị tiết đoạn và tẻ nhạt. Tuy nhiên, việc phải gọi hàm số nhiều tham số như một chuỗi các hàm đơn lẻ là không tốt.

May mắn thay, Ramda cung cấp cho chúng tôi một giải pháp: cà ri.

Currying là một khái niệm cốt lõi trong lập trình chức năng. Về mặt kỹ thuật, một hàm curried luôn là một chuỗi các hàm đơn lẻ, đó là điều tôi vừa phàn nàn. Trong ngôn ngữ chức năng thuần túy, cú pháp nói chung làm cho trông không khác gì gọi một hàm với nhiều đối số.

Nhưng bởi vì Ramda là một thư viện JavaScript, và JavaScript không có cú pháp đẹp để gọi một loạt các chức năng đơn lẻ, các tác giả đã nới lỏng định nghĩa truyền thống của currying một chút.

Trong Ramda, một hàm curried có thể được gọi với một tập hợp các đối số, và nó sẽ trả về một hàm mới chấp nhận các đối số còn lại. Nếu bạn gọi một chức năng curried với tất cả các đối số của nó, nó sẽ gọi chỉ cần gọi chức năng.

Bạn có thể nghĩ đến một chức năng tiên tiến như là tốt nhất của cả hai thế giới: bạn có thể gọi nó bình thường với tất cả các đối số của nó và nó sẽ chỉ làm việc. Hoặc bạn có thể gọi nó bằng một tập hợp các đối số, và nó sẽ hoạt động như thể bạn đã từng sử dụng một phần.

Lưu ý rằng sự linh hoạt này giới thiệu một hit hiệu suất nhỏ, bởi vì cà ri cần phải tìm ra chức năng được gọi như thế nào và sau đó xác định phải làm gì. Nói chung, tôi chỉ curry chức năng khi tôi thấy tôi cần phải sử dụng một phần ở nhiều nơi.

Chúng ta hãy tận dụng cà ri với chức năngInYear đã được xuất bản của chúng tôi. Lưu ý rằng cà ri luôn hoạt động như thể bạn đã từng sử dụng một phần; không có phiên bản partialRight. Chúng ta sẽ nói về điều đó ở bên dưới, nhưng bây giờ, chúng ta sẽ đảo ngược các lập luận để xuất bảnNgày Năm để năm đầu tiên đến.

```
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Chúng ta có thể một lần nữa gọi xuất bản năm nay với chỉ một năm và lấy lại một chức năng mà sau đó lấy một cuốn sách và thực hiện chức năng ban đầu của chúng tôi. Tuy nhiên, chúng tôi vẫn có thể gọi nó là bình thường như được xuất bản trong năm \(2012, sách\) mà không gây phiền nhiễu\) \(cú pháp. Tốt nhất của cả hai thế giới!

## Lệnh tranh luận

Lưu ý rằng để làm cho cà ri làm việc cho chúng tôi, chúng tôi đã phải đảo ngược thứ tự tranh luận. Điều này là rất phổ biến với chức năng lập trình, do đó, hầu như mỗi chức năng Ramda được viết để các dữ liệu được vận hành trên đi đến cuối.

Bạn có thể nghĩ ra các tham số trước đó như cấu hình cho thao tác. Vì vậy, đối với publishedInYear, tham số year là cấu hình \(chúng ta đang tìm kiếm cái gì?\) Và tham số book là dữ liệu \(chúng ta đang tìm kiếm nó ở đâu?\).

Chúng ta đã thấy các ví dụ này với các chức năng lặp lại bộ sưu tập. Tất cả họ lấy bộ sưu tập như là đối số cuối cùng vì nó làm cho phong cách lập trình này dễ dàng hơn.

## Đối số trong Đơn đặt hàng sai

Điều gì sẽ xảy ra nếu chúng ta đã bỏ trật tự tranh luận xuất bản một năm? Làm sao chúng ta vẫn có thể tận dụng được bản chất của nó?

Ramda cung cấp một vài lựa chọn.

### flip

Tùy chọn đầu tiên làflip.fliptakes một hàm của 2 hoặc nhiều đối số và trả về một hàm mới có các đối số tương tự, nhưng sẽ chuyển đổi thứ tự của hai đối số đầu tiên. Nó chủ yếu được sử dụng với hai hàm đối số, nhưng chung chung hơn.

Sử dụng tính năng này, chúng ta có thể quay trở lại nguyên tắc đối số gốc forpublishedInYear:

```
const publishedInYear = curry((book, year) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(flip(publishedInYear)(year), books)

  return map(book => book.title, selected)
}
```

Trong hầu hết các trường hợp, tôi muốn sử dụng trật tự lập luận thuận tiện hơn, nhưng nếu bạn cần sử dụng một chức năng mà bạn không kiểm soát, flip là một lựa chọn hữu ích.

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

## Hãy làm một đường ống

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

## Phần kết luận

Bài đăng này có lẽ là bài sâu nhất trong loạt bài này. Một phần ứng dụng và currying có thể mất một thời gian và nỗ lực để quấn quanh đầu của bạn. Nhưng một khi bạn "nhận được" chúng, họ sẽ giới thiệu cho bạn một cách rất mạnh mẽ để chuyển đổi dữ liệu của bạn một cách có chức năng.

Họ dẫn bạn bắt đầu xây dựng các phép biến đổi bằng cách tạo ra những đường ống nhỏ, các khối xây dựng đơn giản.

## Kế tiếp

Để viết mã theo kiểu chức năng, chúng ta cần bắt đầu suy nghĩ "declaratively" thay vì "imperatively". Để làm được điều đó, chúng ta sẽ cần phải tìm ra những cách thể hiện các cấu trúc bắt buộc chúng ta đã quen với cách thức chức năng. Lập trình tuyên bố thảo luận về những ý tưởng này.

