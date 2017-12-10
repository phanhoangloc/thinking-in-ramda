# BẮT ĐẦU

Bài viết này là sự khởi đầu của một series mới về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Tôi sẽ sử dụng thư viện [Ramda](http://ramdajs.com/) cho loạt bài này, mặc dù nhiều ý tưởng có thể được áp dụng cho các thư viện JavaScript khác như [Underscore](http://underscorejs.org/) và [Lodash](https://lodash.com/), cũng như cho các ngôn ngữ khác.

Tôi sẽ gắn bó với một phiên bản nhẹ và ít hàn lâm hơn của lập trình hàm. Điều này chủ yếu là vì tôi muốn giữ cho loạt bài này có thể tiếp cận được cho nhiều người hơn, nhưng cũng một phần bởi vì bản thân tôi chưa đi đủ xa trên con đường lập trình hàm.

## RAMDA

Tôi đã nói về thư viện [Ramda](http://ramdajs.com/) áp dụng cho JavaScript trên blog này một vài lần:

* Trong [Using Ramda With Redux](http://randycoulman.com/blog/2016/02/16/using-ramda-with-redux/), tôi đã đưa ra một số ví dụ về cách Ramda có thể được sử dụng trong các ngữ cảnh khác nhau khi viết các ứng dụng [Redux](http://redux.js.org/).
* Trong [Using Redux-api-middleware With Rails](http://randycoulman.com/blog/2016/04/19/using-redux-api-middleware-with-rails/), tôi đã sử dụng Ramda để giúp chuyển đổi request và response payloads.

Tôi thấy Ramda là một thư viện được thiết kế độc đáo, cung cấp rất nhiều công cụ để lập trình hàm trong JavaScript một cách gọn gàng, dễ hiểu.

Nếu bạn muốn thử nghiệm với Ramda trong khi đọc qua loạt bài này, có một [môi trường thử nghiệm](http://ramdajs.com/repl/) trên trang web Ramda.

## HÀM

Như tên gọi có thể gợi ý, lập trình hàm có rất nhiều việc để làm với các hàm. Chúng ta sẽ định nghĩa một hàm như một đoạn code có thể tái sử dụng, được gọi với không hoặc nhiều đối số và trả về một kết quả.

Đây là một hàm đơn giản trong JavaScript:

```js
function double(x) {
  return x * 2
}
```

Với các hàm mũi tên \(=&gt;\) của ES6, bạn có thể viết cùng một hàm theo cú pháp dễ dàng hơn nhiều. Tôi đề cập đến điều này ngay bây giờ, bởi vì chúng ta sẽ sử dụng rất nhiều các hàm mũi tên sau này.

```js
const double = x => x * 2
```

Hầu như mọi ngôn ngữ đều có một số hỗ trợ cho các hàm.

Một số ngôn ngữ đi xa hơn và cung cấp hỗ trợ cho các hàm như cấu trúc first-class. Nói về "first-class", ý của tôi là các hàm có thể được sử dụng trong cùng một cách như các kiểu giá trị khác. Bạn có thể:

* Tham khảo chúng từ hằng số và biến
* Truyền chúng như các tham số cho các hàm khác
* Trả lại chúng như kết quả từ các hàm khác

JavaScript là một ngôn ngữ như vậy, và chúng ta sẽ tận dụng điều đó.

## HÀM THUẦN KHIẾT

Khi viết các chương trình theo hướng lập trình hàm, điều quan trọng là chúng ta làm việc chủ yếu với các hàm "thuần khiết" \(pure functions\).

Các hàm thuần khiết là những hàm không có hiệu ứng phụ \(side effects\). Chúng không gán giá trị cho bất kỳ các biến bên ngoài, chúng không tiêu thụ đầu vào, chúng không sản xuất đầu ra, chúng không đọc hoặc ghi vào cơ sở dữ liệu, chúng không sửa đổi các tham số chúng được truyền, vv

Ý tưởng cơ bản là, nếu bạn gọi một hàm với các đầu vào giống nhau nhiều lần, bạn luôn nhận được kết quả giống nhau.

Bạn chắc chắn có thể làm một số thứ với các hàm không thuần khiết \(và bạn phải, nếu chương trình của bạn muốn làm bất cứ điều gì thú vị\), nhưng phần lớn bạn sẽ muốn giữ cho hầu hết các hàm của bạn trở nên thuần khiết.

## TÍNH BẤT BIẾN

Một khái niệm quan trọng khác trong lập trình hàm là "tính bất biến" \(immutability\). Điều đó có nghĩa là gì? "Không thay đổi" \(immutable\) có nghĩa là "không thể thay đổi được".

Khi tôi làm việc theo mô hình bất biến, một khi tôi khởi tạo một giá trị hoặc một đối tượng tôi không bao giờ có thể thay đổi nó một lần nữa. Điều đó có nghĩa là sẽ không có sự thay đổi các phần tử của một mảng hoặc các thuộc tính của một đối tượng.

Thay vào đó, nếu tôi cần phải thay đổi một cái gì đó trong một mảng hoặc đối tượng, tôi sẽ trả lại một bản sao mới của nó với giá trị đã được thay đổi. Các bài viết sau sẽ nói về điều này một cách chi tiết.

Tính bất biến luôn đi cùng với các hàm thuần khiết. Vì các hàm thuần khiết không được phép có các hiệu ứng phụ, chúng không được phép thay đổi cấu trúc dữ liệu bên ngoài. Chúng buộc phải làm việc với dữ liệu theo cách không thể thay đổi.

## BẮT ĐẦU TỪ ĐÂU?

Cách dễ nhất để bắt đầu suy nghĩ theo hướng lập trình hàm là bắt đầu thay thế các vòng lặp bằng tập hợp các hàm lặp.

Nếu bạn đến từ một ngôn ngữ khác mà có các hàm này \(Ruby và Smalltalk là hai ví dụ\), bạn có thể đã quen thuộc với chúng.

Martin Fowler có một vài bài viết tuyệt vời về "Tập hợp đường ống" và [cách sử dụng các hàm này](http://martinfowler.com/articles/collection-pipeline/) và [cách tái cấu trúc code hiện có vào các tập hợp đường ống](http://martinfowler.com/articles/refactoring-pipelines.html).

Lưu ý rằng tất cả các hàm này \(ngoại trừ `reject`\) đều có sẵn trên `Array.prototype`, vì vậy bạn không cần Ramda để bắt đầu sử dụng chúng. Tuy nhiên, tôi sẽ sử dụng phiên bản của Ramda cho nhất quán với phần còn lại của loạt bài này.

### forEach

Thay vì viết một vòng lặp rõ ràng, hãy thử sử dụng hàm `forEach` thay thế. Đó là:

```js
// Replace this:
for (const value of myArray) {
  console.log(value)
}

// with:
forEach(value => console.log(value), myArray)
```

`forEach` nhận vào một hàm và một mảng, gọi hàm trên mỗi phần tử của mảng.

Trong khi `forEach` là cách dễ tiếp cận nhất của các hàm lặp này, nó lại ít được sử dụng nhất trong lập trình hàm. Nó không trả về một giá trị, vì vậy thực sự nó chỉ được sử dụng cho việc gọi các hàm có hiệu ứng phụ.

### map

Chức năng quan trọng nhất tiếp theo để học là `map`. Giống như `forEach`, `map` áp dụng một hàm cho mỗi phần tử của mảng. Tuy nhiên, không giống như `forEach`, `map` thu thập các kết quả của việc áp dụng hàm vào một mảng mới và trả về nó.

Đây là một ví dụ:

```js
map(x => x * 2, [1, 2, 3])  // --> [2, 4, 6]
```

Ví dụ trên sử dụng một hàm ẩn danh, nhưng chúng ta có thể dễ dàng sử dụng một hàm được đặt tên:

```js
const double = x => x * 2

map(double, [1, 2, 3])
```

### filter/reject

Tiếp theo, chúng ta hãy cũng xem xét `filter` và `reject`. Như tên của nó có thể gợi ý, `filter` lựa chọn các phần tử từ một mảng dựa trên một hàm. Ví dụ:

```
const isEven = x => x % 2 === 0

filter(isEven, [1, 2, 3, 4])  // --> [2, 4]
```

`filter` áp dụng hàm của nó \(`isEven` trong trường hợp này\) cho mỗi phần tử của mảng. Bất cứ khi nào hàm trả về một giá trị "đúng" \(truthy\), phần tử tương ứng sẽ được bao gồm trong kết quả. Bất cứ khi nào hàm trả về giá trị "sai" \(falsy\), phần tử tương ứng sẽ bị loại trừ \(bị lọc ra\) khỏi mảng.

`reject` thực hiện chính xác như vậy, nhưng theo cách ngược lại. Nó giữ các phần tử mà hàm trả về một giá trị sai và loại trừ các giá trị mà hàm trả về một giá trị đúng.

```
reject(isEven, [1, 2, 3, 4]) // --> [1, 3]
```

### find

`find` áp dụng một hàm cho mỗi phần của mảng và trả về phần tử đầu tiên mà hàm trả về một giá trị đúng.

```
find(isEven, [1, 2, 3, 4]) // --> 2
```

### reduce

`reduce` thì phức tạp hơn các hàm khác mà chúng ta đã thấy cho đến giờ.

Bạn nên biết về nó, nhưng nếu bạn gặp rắc rối trong việc hiểu nó lúc đầu, đừng để điều đó ngăn cản bạn.

Bạn có thể có thể đi được một chặng đường dài mà không nhất thiết phải hiểu nó.

`reduce` nhận vào một hàm hai đối số, một giá trị ban đầu, và mảng để vận hành.

Đối số đầu tiên của hàm mà chúng ta truyền vào được gọi là "giá trị tích lũy" \(accumulator\) và đối số thứ hai là giá trị từ mảng.

Hàm cần phải trả về một giá trị tích lũy mới.

Hãy xem xét một ví dụ và sau đó giải thích những gì đã xảy ra.

```
const add = (accum, value) => accum + value

reduce(add, 5, [1, 2, 3, 4]) // --> 15
```

1. `reduce` đầu tiên gọi hàm `add` với giá trị ban đầu \(`5`\) và phần tử đầu tiên của mảng \(`1`\). `add` trả lại một giá trị tích lũy mới \(`5 + 1 = 6`\).
2. `reduce` gọi `add` lần nữa, lần này với giá trị tích lũy mới \(`6`\) và giá trị tiếp theo từ mảng \(`2`\). `add` trả về `8`.
3. `reduce` gọi `add` một lần nữa với `8` và giá trị tiếp theo \(`3`\), kết quả là `11`.
4. `reduce` gọi `add` lần cuối với `11` và giá trị cuối cùng của mảng \(`4`\), kết quả là `15`.
5. `reduce` trả về giá trị tích lũy cuối cùng như là kết quả của nó \(`15`\).

## KẾT LUẬN

Bằng cách bắt đầu với các hàm lặp này, bạn có thể quen với ý tưởng truyền các hàm tới các hàm khác. Bạn có thể đã sử dụng chúng trong những ngôn ngữ khác mà không nhận ra bạn đã thực hành lập trình hàm.

## TIẾP THEO

Bài viết kế tiếp trong loạt bài này, [Kết hợp hàm](/combining-functions.md), sẽ chỉ ra làm thế nào có thể thực hiện bước tiếp theo và bắt đầu kết hợp các hàm theo những cách mới mẻ và thú vị.

Nguồn: [Thinking in Ramda: Getting started](http://randycoulman.com/blog/2016/05/24/thinking-in-ramda-getting-started/)

