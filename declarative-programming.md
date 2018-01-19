# LẬP TRÌNH DECLARATIVE

Bài đăng này là Phần 4 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 3](/partial-application.md), chúng ta đã nói về việc kết hợp các hàm có nhiều hơn một tham số bằng cách sử dụng các kỹ thuật áp dụng từng phần và currying.

Khi chúng ta bắt đầu viết các hàm nhỏ để tạo nên các khối \(block\) và kết hợp chúng với nhau, chúng ta phải viết rất nhiều hàm bao gồm các toán tử của JavaScript như số học, so sánh, logic và luồng điều khiển. Điều này có thể cảm thấy tẻ nhạt, nhưng Ramda có thể giúp chúng ta.

Nhưng trước tiên, hãy cùng xem qua các kiến thức nền tảng.

## IMPERATIVE VS DECLARATIVE

Có nhiều cách khác nhau để phân chia các ngôn ngữ hay phong cách lập trình. Kiểu dữ liệu tĩnh và động, ngôn ngữ thông dịch so với ngôn ngữ biên dịch, cấp cao và cấp thấp, v.v ...

Một sự so sánh như vậy là lập trình imperative \(bắt buộc, mệnh lệnh chi tiết\) và declarative \(khai báo, tuyên bố\).

Nếu không đi quá sâu vào sự so sánh này, lập trình imperative là một phong cách lập trình mà các lập trình viên nói với máy tính phải làm gì bằng cách nói cho nó làm thế nào để làm điều đó \(how\). Lập trình imperative tạo ra rất nhiều cấu trúc mà chúng ta sử dụng hàng ngày: điều khiển luồng \(`if`-`then`-`else` statement và vòng lặp\), toán tử số học \(`+`, `-`, `*`, `/`\), toán tử so sánh \(`===`,`>`, `<` , vv\), và các toán tử logic \(`&&`, `||`,`!`\).

Lập trình declarative là một phong cách lập trình mà các lập trình viên nói với máy tính phải làm gì bằng cách nói với nó điều họ muốn \(what\). Máy tính sau đó phải tìm ra cách làm thế nào để đạt được kết quả.

Một trong những ngôn ngữ declarative cổ điển là Prolog. Trong Prolog, một chương trình bao gồm một tập hợp các sự kiện và một bộ quy tắc suy luận. Bạn khởi động chương trình bằng cách hỏi một câu hỏi, và công cụ suy luận của Prolog sử dụng các sự kiện và các quy tắc để trả lời câu hỏi của bạn.

Lập trình hàm được coi là một tập con của chương trình declarative. Trong một chương trình theo lập trình hàm, chúng ta xác định các hàm và sau đó cho máy tính biết phải làm gì bằng cách kết hợp các hàm này với nhau.

Ngay cả trong các chương trình declarative, chúng ta cũng cần làm những công việc tương tự như những chương trình imperative. Điều khiển luồng, số học, so sánh, và logic vẫn là các khối xây dựng cơ bản chúng ta phải làm việc cùng. Nhưng chúng ta cần phải tìm một cách để thể hiện những cấu trúc này theo hướng declarative.

## NHỮNG SỰ THAY THẾ THEO HƯỚNG DECLARATIVE

Vì chúng ta đang lập trình bằng JavaScript, ngôn ngữ imperative, nên việc sử dụng các cấu trúc imperative khi viết mã JavaScript là một điều "bình thường".

Nhưng khi chúng ta tạo ra các biến đổi \(transformations\) bằng việc sử dụng đường ống và các cấu trúc tương tự, các cấu trúc imperative không hoạt động tốt.

Hãy nhìn vào một số khối xây dựng cơ bản mà Ramda cung cấp để giúp chúng ta thoát khỏi sự bất tiện này.

## SỐ HỌC

Trở lại [phần 2](/combining-functions.md), chúng ta thực hiện một loạt các phép biến đổi số học để mô tả một đường ống:

```
const multiply = (a, b) => a * b
const addOne = x => x + 1
const square = x => x * x

const operate = pipe(
  multiply,
  addOne,
  square
)

operate(3, 4) // => ((3 * 4) + 1)^2 => (12 + 1)^2 => 13^2 => 169
```

Chú ý chúng ta phải viết các hàm cho tất cả các khối xây dựng cơ bản mà chúng ta muốn sử dụng.

Ramda cung cấp các hàm `add`, `subtract`, `multiply` và `divide` để sử dụng thay cho các toán tử số học tiêu chuẩn. Vì vậy, chúng ta có thể sử dụng phép nhân `multiply` của Ramda thay cho ký tự mà chúng ta đã viết, chúng ta có thể tận dụng hàm curried `add` của Ramda để thay thế `addOne` của chúng ta, và chúng ta có thể viết `square` bằng các phép nhân `multiply`:

```
const square = x => multiply(x, x)

const operate = pipe(
  multiply,
  add(1),
  square
)
```

`add(1)` rất giống với toán tử increment \(`++`\), nhưng toán tử increment điều chỉnh biến tăng lên và làm nó thay đổi, do đó nó là một mutation. Như chúng ta đã học trong [Phần 1](//getting-started.md), tính không thay đổi là nguyên lý cốt lõi của lập trình hàm nên chúng ta không muốn sử dụng `++` hoặc anh em của nó `--`.

Chúng ta có thể sử dụng `add(1)` và `subtract(1)` để incrementing và decrementing, nhưng vì hai tác vụ này là phổ biến nên Ramda cung cấp `inc` và `dec`.

Vì vậy, chúng ta có thể đơn giản hóa các đường ống của chúng ta một chút nữa:

```
const square = x => multiply(x, x)

const operate = pipe(
  multiply,
  inc,
  square
)
```

`subtract` là sự thay thế cho toán tử trừ `-`, nhưng cũng có toán tử `-` để phủ định một giá trị. Chúng ta có thể sử dụng `multiply(-1)`, nhưng Ramda cung cấp hàm phủ định `negate` để giúp thực hiện công việc này.

## SO SÁNH

Cũng trong [Phần 2](/combining-functions.md), chúng ta đã viết một số hàm để xác định xem một người có đủ điều kiện bỏ phiếu hay không. Phiên bản cuối cùng của code đó trông như sau:

```
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = either(wasBornInCountry, wasNaturalized)

const isEligibleToVote = both(isOver18, isCitizen)
```

Lưu ý rằng một số hàm của chúng ta đang sử dụng toán tử so sánh tiêu chuẩn \(`===` và `>=` trong trường hợp này\). Như bạn có thể dự đoán, Ramda cũng cung cấp những thay thế cho các toán tử này.

Hãy sửa đổi code của chúng ta để sử dụng `equals` thay thế `===` và `gte` thay cho `>=`.

```
const wasBornInCountry = person => equals(person.birthCountry, OUR_COUNTRY)
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => gte(person.age, 18)

const isCitizen = either(wasBornInCountry, wasNaturalized)

const isEligibleToVote = both(isOver18, isCitizen)
```

Ramda cũng cung cấp `gt` cho `>`, `lt` cho `<`, và `lte` cho `<=`.

Lưu ý rằng các hàm này nhận các tham số của chúng theo một thứ tự có vẻ bình thường \(tham số đầu tiên lớn hơn cái thứ hai?\). Điều này có ý nghĩa khi được sử dụng trong sự cô lập, nhưng có thể gây nhầm lẫn khi kết hợp các hàm. Các hàm này dường như vi phạm nguyên tắc "dữ liệu cuối cùng" của Ramda, vì vậy chúng ta phải cẩn thận khi sử dụng chúng trong các đường ống và các tình huống tương tự. Đó là khi `flip` và placeholder \(`__`\) sẽ có ích.

Bổ sung cho `equals`, có `identical` để xác định nếu hai giá trị tham chiếu cùng một bộ nhớ.

Có một vài trường hợp sử dụng phổ biến của `===`: kiểm tra nếu một chuỗi hoặc mảng là rỗng \(`str === ''` hoặc `arr.length === 0`\), và kiểm tra nếu một biến là `null` hoặc `undefined`. Ramda cung cấp các hàm hữu ích cho cả hai trường hợp: `isEmpty` và `isNil`.

## LOGIC

Trong [Phần 2](/combining-functions.md) \(và ở trên\), chúng ta đã sử dụng các hàm `both` và `either` thay cho `&&` và `||`. Chúng ta cũng nói về việc dùng `complement`  thay vì `!`

Những hàm kết hợp này hoạt động tốt khi cả hai hàm chúng ta kết hợp hoạt động trên cùng một giá trị. Trên đây, `wasBornInCountry`, `wasNaturalized`, và `isOver18` đều áp dụng cho một người `person`.

Nhưng đôi khi chúng ta cần áp dụng `&&`, `||`, và `!` cho các giá trị khác biệt. Đối với những trường hợp đó, Ramda cho chúng ta các hàm `and`, `or`, và `not`. Tôi nghĩ về nó theo cách này: `and`, `or`, và `not` làm việc với các giá trị, trong khi cả `both`, `either`, và `complement` làm việc với các hàm.

Trường hợp sử dụng phổ biến của `||` là để cung cấp các giá trị mặc định. Ví dụ, chúng ta có thể viết như thế này:

```
const lineWidth = settings.lineWidth || 80
```

Đây là một cách sử dụng phổ biến, và hoạt động đúng trong phần lớn trường hợp, tuy nhiên nó lại dựa vào định nghĩa "falsy" của JavaScript. Điều gì sẽ xảy ra nếu `0` là một giá hợp lệ? Vì `0` là falsy, chúng ta sẽ kết thúc với `lineWidth` là `80`.

Chúng ta có thể sử dụng hàm `isNil` mà chúng ta vừa học được ở trên, nhưng một lần nữa Ramda có một lựa chọn tốt hơn cho chúng ta: `defaultTo`.

```
const lineWidth = defaultTo(80, settings.lineWidth)
```

`defaultTo` kiểm tra nếu đối số thứ hai với `isNil`. Nếu `false`, nó trả về giá trị đó, ngược lại thì nó trả về giá trị đầu tiên.

## ĐIỀU KIỆN

Điều khiển luồng là không cần thiết trong các chương trình theo lập trình hàm, nhưng đôi khi vẫn hữu ích. Các hàm lặp trên tập hợp mà chúng ta đã nói ở [Phần 1](//getting-started.md) bao gồm hầu hết các tình huống lặp, nhưng các cấu trúc điều kiện vẫn còn quan trọng.

### ifElse

Chúng ta hãy viết một hàm, `forever21`, nhận vào một số tuổi và trả về tuổi tiếp theo. Nhưng, như tên của nó, một khi đến 21 tuổi, nó sẽ luôn như vậy.

```
const forever21 = age => age >= 21 ? 21 : age + 1
```

Lưu ý rằng điều kiện của chúng ta \(`age >= 21`\) và nhánh thứ hai \(`age + 1`\) đều có thể được viết theo hàm của độ tuổi `age`. Chúng ta có thể viết lại nhánh đầu tiên \(`21`\) như một hàm không đổi \(`() => 21`\). Bây giờ chúng ta có ba hàm mà có \(hoặc bỏ qua\) `age`.

Bây giờ chúng ta đang ở một vị trí mà chúng ta có thể sử dụng `ifElse` của Ramda, tương đương với cấu trúc `if... then...else` hoặc người anh em họ ngắn hơn, toán tử thứ ba \(`?:`\).

```
const forever21 = age => ifElse(gte(__, 21), () => 21, inc)(age)
```

Như đã đề cập ở trên, các hàm so sánh không hoạt động như chúng ta mong muốn khi kết hợp các hàm, vì vậy chúng ta buộc phải sử dụng placeholder ở đây \(`__`\). Chúng ta cũng có thể chuyển sang `lte`:

```
const forever21 = age => ifElse(lte(21), () => 21, inc)(age)
```

Trong trường hợp này, chúng ta phải đọc cái này là "21 nhỏ hơn hoặc bằng số tuổi". Tôi sẽ gắn bó với placeholder cho phần còn lại của bài viết này vì tôi thấy nó dễ đọc hơn và ít gây nhầm lẫn.

### Hằng số

Các hàm hằng số khá hữu ích trong các tình huống như thế này. Như bạn có thể tưởng tượng, Ramda cung cấp cho chúng ta một con đường tắt. Trong trường hợp này, nó được đặt tên là `always`.

```
const forever21 = age => ifElse(gte(__, 21), always(21), inc)(age)
```

Ramda cũng cung cấp `T` và `F` thay cho `always(true)` và `always(false)`.

### identity

Hãy thử một hàm khác, `alwaysDrivingAge`. Hàm này nhận một giá trị thời gian và trả lại chính nó nếu nó là `gte` 16. Nhưng nếu nó nhỏ hơn 16, nó sẽ trả về 16. Điều này cho phép bất cứ ai giả vờ rằng họ đang ở tuổi lái xe, ngay cả khi họ không phải vậy.

```
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), a => a)(age)
```

Nhánh thứ hai của điều kiện \(`a => a`\) là một mô hình \(pattern\) phổ biến trong lập trình hàm. Nó được gọi là hàm nhận dạng \(identity\). Tức là, một hàm trả lại bất kỳ tham số nào được đưa vào.

Như bạn có thể giả định, Ramda cung cấp hàm `identity` cho chúng ta.

```
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), identity)(age)
```

`identity` có thể có nhiều hơn một tham số, nhưng nó luôn trả về tham số đầu tiên. Nếu chúng ta muốn trả về một cái gì đó khác với tham số đầu tiên, thì hàm `nthArg` tổng quát hơn. Tuy nhiên, nó ít phổ biến hơn `identity`.

### when và unless

Có một câu lệnh `ifElse`, trong đó một trong các nhánh có điều kiện là `identity` cũng khá phổ biến, do đó Ramda cung cấp một số cách viết tắt cho chúng ta.

Nếu, như trong trường hợp của chúng ta, nhánh thứ hai là `identity`, chúng ta có thể sử dụng `when` thay vì `ifElse`:

```
const alwaysDrivingAge = age => when(lt(__, 16), always(16))(age)
```

Nếu nhánh đầu tiên của điều kiện là `identity`, chúng ta có thể sử dụng `unless`. Nếu chúng ta đảo ngược điều kiện của chúng ta để sử dụng `gte(__, 16)`, chúng ta có thể sử dụng `unless`.

```
const alwaysDrivingAge = age => unless(gte(__, 16), always(16))(age)
```

### cond

Ramda cũng cung cấp hàm `cond` để có thể thay thế `switch` hoặc một chuỗi các câu lệnh `if...then...else`.

Tôi sẽ lặp lại ví dụ từ tài liệu Ramda để chỉ ra nó được sử dụng như thế nào:

    const water = temperature => cond([
      [equals(0),   always('water freezes at 0°C')],
      [equals(100), always('water boils at 100°C')],
      [T,           temp => `nothing special happens at ${temp}°C`]
    ])(temperature)

Tôi chưa từng cần thiết phải sử dụng `cond` trong code Ramda của tôi, nhưng tôi đã viết code Common Lisp từ nhiều năm trước, do đó `cond` đối với tôi như một người bạn cũ.

## KẾT LUẬN

Chúng ta đã cùng xem xét một số hàm mà Ramda cung cấp để chuyển đổi code từ imperative sang declarative.

## TIẾP THEO

Bạn có thể nhận thấy rằng vài hàm cuối cùng chúng ta đã viết \(`forever21`, `drivingAge`, and `water`\) tất cả đều nhận vào một tham số, xây dựng một hàm mới và sau đó áp dụng hàm đó cho tham số.

Đây là một mô hình phổ biến, và một lần nữa Ramda cung cấp các công cụ để làm gọn cấu trúc này. Bài đăng tiếp theo, [Pointfree Style](/point-free-style.md) xem xét làm thế nào để đơn giản hóa các hàm theo mẫu này.

Nguồn: [Thinking in Ramda: Declarative programming](http://randycoulman.com/blog/2016/06/14/thinking-in-ramda-declarative-programming/)

