# KẾT HỢP HÀM

Bài đăng này là Phần 2 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 1](//getting-started.md), tôi giới thiệu Ramda và một số ý tưởng cơ bản về lập trình hàm, chẳng hạn như hàm, hàm thuần khiết và tính bất biến. Sau đó tôi đề xuất rằng cách tốt để bắt đầu là với các hàm lặp trên tập hợp như `forEach`, `map`, `find`,...

## CÁC KẾT HỢP ĐƠN GIẢN

Một khi bạn đã quen với ý tưởng truyền hàm đến các hàm khác, bạn có thể bắt đầu tìm ra các tình huống mà bạn muốn kết hợp nhiều hàm với nhau.

Ramda cung cấp một số chức năng để tạo các kết hợp đơn giản. Hãy nhìn vào một vài ví dụ.

### complement

Trong bài viết gần nhất, chúng ta đã sử dụng `find` để tìm số chẵn đầu tiên trong danh sách:

```
const isEven = x => x % 2 === 0

find(isEven, [1, 2, 3, 4]) // --> 2
```

Điều gì sẽ xảy ra nếu chúng ta muốn tìm số lẻ đầu tiên. Chúng ta luôn có thể viết một hàm `isOdd` và sử dụng nó, nhưng chúng ta biết rằng bất kỳ số nào nếu không phải là chẵn thỉ là số lẻ. Chúng ta hãy sử dụng lại hàm `isEven`.

Ramda cung cấp một hàm bậc cao \(higher order function\), `complement`, lấy nhận một hàm và trả về một hàm mới, trả về `true` khi hàm gốc trả về một giá trị `false` và `false` khi hàm gốc trả về một giá trị `true`.

```
const isEven = x => x % 2 === 0

find(complement(isEven), [1, 2, 3, 4]) // --> 1
```

Thậm chí tốt hơn là cung cấp cho hàm bổ sung tên riêng để nó có thể được sử dụng lại:

```
const isEven = x => x % 2 === 0
const isOdd = complement(isEven)

find(isOdd, [1, 2, 3, 4]) // --> 1
```

Lưu ý rằng `complement` thực hiện cùng một ý tưởng cho các hàm giống như là `!` \(phủ định\) áp dụng cho các giá trị.

### both/either

Giả sử rằng chúng ta đang làm việc trên một hệ thống bỏ phiếu. Với một người, chúng tôi muốn có thể xác định xem người đó có đủ điều kiện bỏ phiếu hay không. Dựa trên kiến thức hiện tại của chúng ta, một người cần phải từ 18 tuổi trở lên và là một công dân mới có thể bỏ phiếu. Một người nào đó là công dân nếu họ sinh ra ở trong nước hoặc nếu sau đó họ trở thành công dân thông qua việc nhập quốc tịch.

```
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)

const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Những gì chúng ta đã viết ở trên chạy tốt, nhưng Ramda cung cấp một vài hàm hữu ích để giúp chúng ta viết nó gọn gàng hơn.

`both` nhận hai hàm và trả về một hàm mới, trả về `true` nếu cả hai hàm trả về một giá trị đúng khi áp dụng trên các tham số và `false` theo chiều ngược lại.

`either` nhận hai hàm và trả về một hàm mới, trả về `true` nếu một trong hai hàm trả về giá trị đúng khi áp dụng trên các tham số và `false` theo chiều ngược lại.

Sử dụng hai hàm này, chúng ta có thể đơn giản hóa `isCitizen` và `isEligibleToVote`:

```
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Lưu ý rằng `both`  thực hiện cùng một ý tưởng cho các hàm như toán tử && \(và\) đối với các giá trị và `either` thực hiện cùng một ý tưởng cho các hàm như \|\| \(hoặc\) cho các giá trị.

Ramda cũng cung cấp `allPass` và `anyPass`, nhận một mảng bất kỳ số lượng các hàm. Như tên của chúng cho thấy, `allPass` hoạt động như `both`, và `anyPass` hoạt động như `either`.

## ĐƯỜNG ỐNG

Đôi khi chúng tôi muốn áp dụng một số hàm cho một số dữ liệu theo kiểu đường ống. Ví dụ, chúng ta có thể muốn lấy hai con số, nhân chúng với nhau, cộng thêm một, và lấy bình phương của kết quả. Chúng ta có thể viết nó như sau:

```
const multiply = (a, b) => a * b
const addOne = x => x + 1
const square = x => x * x

const operate = (x, y) => {
  const product = multiply(x, y)
  const incremented = addOne(product)
  const squared = square(incremented)

  return squared
}

operate(3, 4) // => ((3 * 4) + 1)^2 => (12 + 1)^2 => 13^2 => 169
```

Chú ý mỗi thao tác được áp dụng dựa trên kết quả của phép tính trước.

### pipe

Ramda cung cấp hàm `pipe`, nhận vào danh sách của một hoặc nhiều hàm và trả về một hàm mới.

Hàm mới có cùng số đối số như là hàm đầu tiên được đưa vào. Sau đó nó sẽ "truyền" \(pipe\) những đối số thông qua mỗi hàm trong danh sách. Nó áp dụng hàm đầu tiên cho các tham số, truyền kết quả của nó đến hàm thứ hai và vv. Kết quả của hàm cuối cùng là kết quả của lệnh gọi `pipe`.

Lưu ý rằng tất cả các hàm sau hàm đầu tiên phải chỉ có một đối số duy nhất.

Biết được điều này, chúng ta có thể sử dụng `pipe` để đơn giản hóa hàm operate của chúng ta:

```
const operate = pipe(
  multiply,
  addOne,
  square
)
```

Khi chúng ta gọi `operate(3, 4)`, `pipe` truyền `3` và `4` đến hàm `multiply`, kết quả là `12`. Nó truyền `12` đến `addOne`, trả về `13`. Sau đó nó gửi `13` đến `square`, trả lại `169`, và trở thành kết quả cuối cùng của `operate`.

### compose

Một cách khác chúng ta có thể viết hàm `operate` ban đầu của chúng ta là nội tuyến \(inline\) tất cả các biến tạm thời:

```
const operate = (x, y) => square(addOne(multiply(x, y)))
```

Nhìn nó còn nhỏ gọn hơn, nhưng lại hơi khó đọc hơn. Tuy nhiên, trong hình thức đó, nó dẫn đến việc có thể viết lại bằng cách sử dụng hàm `compose` của Ramda.

`compose` chính xác theo cùng cách với `pipe`, ngoại trừ việc áp dụng các hàm theo thứ tự từ phải sang trái \(right to left\) thay vì từ trái sang phải \(left to right\). Hãy viết lại `operate` với `compose`:

```
const operate = compose(
  square,
  addOne,
  multiply
)
```

Điều này hoàn toàn giống như `pipe` ở trên, nhưng với các hàm theo thứ tự ngược lại. Trên thực tế, hàm `compose` của Ramda được viết theo `pipe`.

Tôi luôn luôn nghĩ đến `compose` theo cách này: `compose(f, g)(value)` tương đương với `f(g(value))`.

Như với `pipe`, lưu ý rằng tất cả các hàm ngoại trừ hàm cuối cùng chỉ phải có một tham số duy nhất.

### compose hay pipe?

Tôi nghĩ rằng `pipe` có lẽ là dễ hiểu nhất khi đến từ một nền tảng bắt buộc \(imperative\) hơn kể từ khi bạn đọc các hàm từ trái sang phải. Tuy nhiên, `compose` thì dễ dàng hơn để dịch cho các kiểu hàm lồng nhau như tôi đã chỉ ra ở trên.

Tôi vẫn chưa phát triển một quy tắc tốt để nhận biết khi nào tôi chọn `compose` và khi tôi chọn `pipe`. Vì chúng tương đương nhau trong Ramda, có thể không quan trọng bạn chọn hàm nào. Hãy chọn bất kỳ cái nào tốt nhất trong tình huống của bạn.

## KẾT LUẬN

Bằng cách kết hợp các hàm theo những cách cụ thể, chúng ta có thể bắt đầu viết các hàm nhiều chức năng hơn.

## TIẾP THEO

Bạn có thể đã nhận thấy rằng chúng ta chủ yếu bỏ qua các tham số khi chúng ta kết hợp các hàm. Chúng ta chỉ cung cấp các tham số khi chúng ta gọi hàm kết hợp.

Điều này là phổ biến trong lập trình hàm, và chúng ta sẽ nói về nó nhiều hơn nữa trong bài tiếp theo, [Áp dụng từng phần](/partial-application.md). Chúng ta cũng sẽ nói về việc làm thế nào để kết hợp các hàm có nhiều hơn một tham số.

Nguồn: [Thinking in Ramda: Combining functions](http://randycoulman.com/blog/2016/05/31/thinking-in-ramda-combining-functions/)

