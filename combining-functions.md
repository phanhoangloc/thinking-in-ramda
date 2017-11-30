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

Hãy nói rằng chúng tôi đang làm việc trên một hệ thống bỏ phiếu. Với một người, chúng tôi muốn để có thể xác định xem người đó có đủ điều kiện bỏ phiếu. Dựa trên kiến thức hiện tại của chúng tôi, một người phải từ 18 tuổi trở lên và trở thành công dân để có thể bỏ phiếu. Một người nào đó là công dân nếu họ sinh ra ở trong nước hoặc nếu sau đó họ trở thành công dân thông qua việc nhập quốc tịch

```
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)

const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Những gì chúng tôi đã viết ở trên các công trình, nhưng Ramda cung cấp một vài chức năng hữu ích để giúp chúng tôi làm sạch nó lên một chút.

cả hai mất hai chức năng khác và trả về một hàm mới trả về true nếu cả hai hàm trả về một giá trị truey khi áp dụng cho các đối số và sai nếu không.

hoặc lấy hai hàm khác và trả về một hàm mới trả về true nếu một trong hai hàm trả về giá trị truey khi áp dụng cho các đối số và sai nếu không.

Sử dụng hai chức năng này, chúng ta có thể đơn giản hóa làCitizen và isEligibleToVote:

```
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Lưu ý rằng cả hai đều thực hiện cùng một ý tưởng cho các hàm như toán tử && \(và\) đối với các giá trị và thực hiện cùng một ý tưởng cho các hàm như \|\| \(hoặc\) cho các giá trị.

Ramda cũng cung cấp allPass và anyPass có một mảng của bất kỳ số lượng các chức năng. Như tên của họ cho thấy, allPass hoạt động như cả hai, và anyPass hoạt động như là một trong hai.

## Pipelines

Đôi khi chúng tôi muốn áp dụng một số chức năng cho một số dữ liệu trong một đường ống thời trang. Ví dụ, chúng ta có thể muốn lấy hai con số, nhân chúng với nhau, thêm một, và sắp xếp kết quả. Chúng ta có thể viết nó như sau:

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

Chú ý mỗi thao tác được áp dụng như thế nào với kết quả trước.

### Pipe

Ramda cung cấp các chức năng ống, trong đó có một danh sách của một hoặc nhiều chức năng và trả về một chức năng mới.

Hàm mới có cùng số đối số như là hàm đầu tiên được đưa ra. Nó sau đó "ống" những đối số thông qua mỗi chức năng trong danh sách. Nó áp dụng các chức năng đầu tiên cho các đối số, vượt qua kết quả của nó đến chức năng thứ hai và vv. Kết quả của chức năng cuối cùng là kết quả của cuộc gọi đường ống.

Lưu ý rằng tất cả các chức năng sau khi người đầu tiên phải chỉ mất một đối số duy nhất.

Biết được điều này, chúng ta có thể sử dụng ống để đơn giản hóa chức năng vận hành của chúng tôi:

```
const operate = pipe(
  multiply,
  addOne,
  square
)
```

Khi chúng ta gọi hoạt động \(3, 4\), đường ống đi qua 3 và 4 đến hàm nhân, kết quả là 12. Nó truyền 12 đến addOne, trả về 13. Sau đó nó đi qua 13 đến hình vuông, quay lại 169, và rằng trở thành kết quả cuối cùng của hoạt động.

### Compose

Một cách khác chúng ta có thể đã viết chức năng hoạt động ban đầu của chúng tôi là nội tuyến tất cả các biến tạm thời:

```
const operate = (x, y) => square(addOne(multiply(x, y)))
```

Điều đó còn nhỏ gọn hơn, nhưng hơi khó đọc hơn. Tuy nhiên, trong hình thức đó, nó tự cho mình viết lại bằng cách sử dụng chức năng soạn thảo của Ramda.

soạn thảo chính xác theo cùng cách với đường ống, ngoại trừ việc áp dụng các chức năng theo thứ tự từ phải sang trái thay vì từ trái sang phải. Hãy viết hoạt động với soạn:

```
const operate = compose(
  square,
  addOne,
  multiply
)
```

Điều này hoàn toàn giống như ống ở trên, nhưng với các chức năng theo thứ tự ngược lại. Trên thực tế, chức năng sáng tác của Ramda được viết dưới dạng đường ống.

Tôi luôn luôn nghĩ đến soạn theo cách này: compose \(f, g\) \(value\) tương đương với f \(g \(value\)\).

Như với đường ống, lưu ý rằng tất cả các chức năng ngoại trừ cuối cùng chỉ phải mất một đối số duy nhất.

### Compose or Pipe?

Tôi nghĩ rằng đường ống có lẽ là dễ hiểu nhất khi đến từ một nền tảng bắt buộc hơn kể từ khi bạn đọc các chức năng từ trái sang phải. Tuy nhiên, soạn thảo là một chút dễ dàng hơn để dịch cho các mẫu chức năng lồng nhau như tôi đã chỉ ra ở trên.

Tôi vẫn chưa phát triển một quy tắc tốt cho khi nào tôi thích sáng tác và khi tôi thích ống dẫn. Vì chúng tương đương nhau trong Ramda, có thể không quan trọng bạn chọn loại nào. Chỉ cần đi với bất cứ ai đọc tốt nhất trong tình huống của bạn.

## Phần kết luận

Bằng cách kết hợp một số chức năng theo những cách cụ thể, chúng ta có thể bắt đầu viết các chức năng mạnh hơn.

## Kế tiếp

Bạn có thể đã nhận thấy rằng chúng tôi chủ yếu bỏ qua các đối số chức năng khi chúng tôi đang kết hợp các chức năng. Chúng ta chỉ cung cấp các đối số khi chúng ta gọi hàm kết hợp.

Điều này là phổ biến trong lập trình chức năng, và chúng tôi nói về điều đó nhiều hơn nữa trong bài tiếp theo trong loạt bài này, một phần ứng dụng. Chúng ta cũng nói về làm thế nào để kết hợp các chức năng mà có nhiều hơn một đối số.

