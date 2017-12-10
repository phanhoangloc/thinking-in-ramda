# POINTFREE STYLE

Bài đăng này là Phần 5 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [phần 4](/declarative-programming.md), chúng ta đã nói về việc viết code theo cách khai báo \(nói với máy tính phải làm gì\) thay vì imperative \(nói với máy tính làm thế nào để làm điều đó\).

Bạn có thể nhận thấy rằng một vài hàm mà chúng ta đã viết \(`forever21`, `drivingAge`và `water`\) đều lấy một tham số, xây dựng một hàm mới và sau đó áp dụng hàm đó cho tham số.

Đây là một mô hình rất phổ biến trong lập trình hàm, và một lần nữa Ramda cung cấp các công cụ để làm gọn cách viết này.

## POINTFREE STYLE

Có hai nguyên tắc chính của Ramda mà chúng ta đã nói tới trong [Phần 3](/partial-application.md):

* Đặt dữ liệu cuối cùng
* Curry tất cả mọi thứ

Hai nguyên tắc này dẫn đến một phong cách mà các lập trình viên hàm gọi là "pointfree". Tôi thích nghĩ về code pointfree như là "Dữ liệu? Dữ liệu gì? Không có dữ liệu ở đây. "

Có một bài viết tuyệt vời, [Why Ramda?](http://fr.umio.us/why-ramda/), minh họa phong cách pointfree thực sự tốt. Nói một cách rõ ràng, nó có phần tiêu đề như "Dữ liệu ở đâu?", "Được rồi! Tôi có thể xem một số dữ liệu?", Và "Tôi chỉ muốn dữ liệu của tôi, Cảm ơn ".

Chúng tôi chưa có các công cụ cần thiết để làm cho tất cả các ví dụ của chúng ta hoàn toàn pointfree, nhưng chúng ta có thể bắt đầu.

Chúng ta hãy nhìn vào forever21:

```
const forever21 = age => ifElse(gte(__, 21), always(21), inc)(age)
```

Lưu ý rằng độ tuổi chỉ xuất hiện hai lần: một lần trong danh sách tham số và một lần ở cuối của hàm khi chúng ta áp dụng hàm mới được trả về bởi ifElse.

Nếu chúng ta để ý trong khi làm việc với Ramda, chúng ta sẽ thấy mẫu này rất nhiều. Nó gần như luôn luôn có nghĩa là có một cách để chuyển đổi hàm sang phong cách pointfree.

Hãy xem nó sẽ như thế nào:

```
const forever21 = ifElse(gte(__, 21), always(21), inc)
```

Và, poof! Chúng ta vừa làm cho số tuổi age biến mất. Phong cách Pointfree. Lưu ý rằng không có sự khác biệt về hành vi trong hai phiên bản này. Chúng ta vẫn trả lại một hàm nhận vào một số tuổi, nhưng bây giờ chúng ta không chỉ rõ tham số tuổi.

Chúng ta có thể làm tương tự với alwaysDrivingAge và water.

Trong bài viết trước, alwaysDrivingAge trông như sau:

```
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), identity)(age)
```

Chúng ta có thể áp dụng cùng một phép biến đổi để làm cho nó trở nên pointfree.

```
const alwaysDrivingAge = when(lt(__, 16), always(16))
```

Và đây là cách chúng ta đã viết water:

    const water = temperature => cond([
      [equals(0),   always('water freezes at 0°C')],
      [equals(100), always('water boils at 100°C')],
      [T,           temp => `nothing special happens at ${temp}°C`]
    ])(temperature)

Và đây là water theo phong cách pointfree:

    const water = cond([
      [equals(0),   always('water freezes at 0°C')],
      [equals(100), always('water boils at 100°C')],
      [T,           temp => `nothing special happens at ${temp}°C`]
    ])

## HÀM ĐA THAM SỐ {#multi-argument-functions}

Vậy các hàm mà có nhiều hơn một tham số thì sao ? Chúng ta hãy nhìn lại các ví dụ titlesForYear từ [Phần 3](/partial-application.md).

```
const titlesForYear = curry((year, books) =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )(books)
)
```

Lưu ý rằng books chỉ xuất hiện hai lần: một lần là tham số cuối cùng trong danh sách đối số \(dữ liệu cuối cùng!\), Và một lần ở cuối của hàm khi chúng ta áp dụng đường ống vào nó. Điều này cũng tương tự như mô hình chúng ta thấy với độ tuổi ở trên, do đó hãy áp dụng cùng một sự chuyển đổi với nó:

```
const titlesForYear = year =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )
```

Nó hoạt động! Bây giờ chúng ta có một phiên bản pointfree cho titlesForYear.

Trung thực, tôi có lẽ sẽ không hướng tới phong cách pointfree trong trường hợp này vì JavaScript không làm cho việc gọi một loạt các hàm đối số đơn giản là thuận tiện, như chúng ta đã thảo luận trong các bài viết trước đó.

Nếu chúng ta muốn sử dụng titlesForYear trong một đường ống, nó vẫn ổn. Chúng ta có thể gọi titleForYear\(2012\) rất dễ dàng. Nhưng nếu chúng ta muốn sử dụng nó một cách tự nhiên, chúng ta phải quay trở lại mô hình \)\( mà chúng ta đã thấy trong bài trước: titlesForYear\(2012\) \(books\). Với tôi, điều đó không đáng để đánh đổi.

Nhưng bất cứ lúc nào tôi có hàm một tham số theo \(hoặc có thể được refactored để theo\) mô hình trên, tôi sẽ hầu như luôn luôn làm cho nó pointfree.

## REFACTOR THÀNH POINTFREE

Sẽ có những lúc các hàm của chúng ta không theo khuôn mẫu. Chúng ta có thể đang vận hành trên dữ liệu nhiều lần trong cùng một hàm.

Đây là trường hợp trong một số ví dụ trong [Phần 2](/combining-functions.md). Trong những ví dụ này, chúng ta đã tái cấu trúc code để kết hợp các hàm bằng cách sử dụng các thứ như both, either, pipe và compose. Một khi chúng tôi đã làm điều đó, làm cho các hàm của chúng ta pointfree là một sự chuyển đổi tương đối dễ dàng.

Chúng ta hãy nhìn lại ví dụ isEligibleToVote. Đây là nơi chúng ta bắt đầu:

```
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)

const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Hãy bắt đầu với isCitizen. Nó nhận vào một người person và sau đó áp dụng hai hàm khác nhau cho người đó, kết hợp các kết quả với \|\|. Như chúng ta đã học trong [Phần 2](/combining-functions.md), chúng ta có thể sử dụng either để kết hợp hai hàm vào một hàm số mới trước, và sau đó áp dụng hàm kết hợp với người đó.

```
const isCitizen = person => either(wasBornInCountry, wasNaturalized)(person)
```

Chúng ta có thể làm điều tương tự với isEligibleToVote bằng both:

```
const isEligibleToVote = person => both(isOver18, isCitizen)(person)
```

Bây giờ chúng ta đã thực hiện việc tái cấu trúc này, chúng ta có thể thấy rằng cả hai hàm đều làm theo mô hình chúng ta đã nói ở trên: person được nhắc đến hai lần, một lần là tham số hàm, và một lần ở cuối khi chúng ta áp dụng hàm kết hợp với nó. Bây giờ chúng ta có thể refactor theo phong cách pointfree:

```
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

## TẠI SAO?

Phong cách Pointfree cần có thời gian để làm quen. Có thể khó để thích ứng với các tham số dữ liệu bị thiếu ở mọi nơi. Điều quan trọng là phải có một sự quen thuộc với các hàm của Ramda để biết có bao nhiêu tham số cần thiết.

Nhưng một khi bạn đã quen với nó, nó sẽ trở nên rất có giá trị để có một loạt các hàm pointfree nhỏ kết hợp với nhau theo những cách thú vị.

Lợi thế của phong cách pointfree là gì? Có người lập luận rằng đó chỉ là một bài tập học thuật được thiết kế để giành được huy hiệu mệnh danh lập trình hàm. Tuy nhiên, tôi nghĩ rằng nó có một vài lợi thế, mặc dù phải mất thời gian để làm quen:

* Nó làm cho các chương trình đơn giản và súc tích hơn. Đây không phải luôn là một điều tốt, nhưng có thể.
* Nó làm cho các thuật toán rõ ràng hơn. Bằng cách chỉ tập trung vào các hàm được kết hợp, chúng ta có được một cảm nhận tốt hơn về những gì đang xảy ra khi không có các tham số dữ liệu.
* Nó buộc chúng ta suy nghĩ nhiều hơn về việc chuyển đổi đang được thực hiện hơn là về dữ liệu đang được chuyển đổi.
* Nó giúp chúng ta suy nghĩ về các hàm của chúng ta như các khối xây dựng thông thường, có thể làm việc với các loại dữ liệu khác nhau, hơn là nghĩ về chúng như các hoạt động trên một loại dữ liệu cụ thể. Bằng cách cung cấp cho dữ liệu một cái tên, chúng ta đang hạn chế suy nghĩ của chúng ta về nơi mà chúng ta có thể sử dụng hàm. Bằng cách bỏ qua dữ liệu, nó cho phép chúng ta sáng tạo hơn.

## KẾT LUẬN

Phong cách pointfree, còn được gọi là [tacit programming](https://en.wikipedia.org/wiki/Tacit_programming), có thể làm cho code của chúng ta rõ ràng và dễ hiểu hơn. Bằng cách tái cấu trúc code của chúng tôi để kết hợp tất cả biến đổi của chúng ta thành một hàm duy nhất, chúng ta sẽ kết thúc với các khối xây dựng nhỏ hơn có thể được sử dụng ở nhiều nơi hơn.

## TIẾP THEO

Trong ví dụ của chúng ta, chúng ta đã không thể refactor tất cả mọi thứ để phong cách pointfree. Chúng ta vẫn có code được viết theo phong cách imperative. Hầu hết các code này xử lý các đối tượng và mảng.

Chúng ta cần tìm những cách tương tác với các đối tượng và mảng. Vậy tính bất biến thì sao? Làm thế nào để chúng ta thao tác với các đối tượng và mảng theo cách bất biến?

Bài tiếp theo trong series này, Tính bất biến và Đối tượng thảo luận làm thế nào để làm việc với các đối tượng theo hướng lập trình hàm  và bất biến. Và bài sau đó, tính bất biến và mảng tương tự cho mảng.

Nguồn: [Thinking in Ramda: Pointfree style](http://randycoulman.com/blog/2016/06/21/thinking-in-ramda-pointfree-style/)

