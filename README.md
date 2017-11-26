# Giới thiệu

Đây là bản dịch tiếng Việt của loạt bài viết Thinking in Ramda của Randy Coulman. Các bạn có thể tìm thấy đường dẫn đến  bài viết gốccủa tác giả ở phần cuối của mỗi chương.

---

## Thinking in Ramda: Bắt đầu

Bài viết này là sự khởi đầu của một series mới về lập trình hàm được gọi là Thinking in Ramda.

Tôi sẽ sử dụng thư viện [Ramda](http://ramdajs.com/) cho loạt bài này, mặc dù nhiều ý tưởng có thể được áp dụng cho các thư viện JavaScript khác như [Underscore](http://underscorejs.org/) và [Lodash](https://lodash.com/), cũng như cho các ngôn ngữ khác.

Tôi sẽ gắn bó với một phiên bản nhẹ và ít hàn lâm hơn của lập trình hàm. Điều này chủ yếu là vì tôi muốn giữ cho loạt bài này có thể tiếp cận được cho nhiều người hơn, nhưng cũng một phần bởi vì bản thân tôi chưa đi đủ xa trên con đường lập trình hàm.

## Ramda

Tôi đã nói chuyện về thư viện [Ramda](http://ramdajs.com/) áp dụng cho JavaScript trên blog này một vài lần:

* Trong [Using Ramda With Redux](http://randycoulman.com/blog/2016/02/16/using-ramda-with-redux/), tôi đã đưa ra một số ví dụ về cách Ramda có thể được sử dụng trong các ngữ cảnh khác nhau khi viết các ứng dụng [Redux](http://redux.js.org/).
* Trong [Using Redux-api-middleware With Rails](http://randycoulman.com/blog/2016/04/19/using-redux-api-middleware-with-rails/), tôi đã sử dụng Ramda để giúp chuyển đổi request và response payloads.

Tôi thấy Ramda là một thư viện được thiết kế độc đáo, cung cấp rất nhiều công cụ để lập trình hàm trong JavaScript một cách gọn gàng, thanh lịch.

Nếu bạn muốn thử nghiệm với Ramda trong khi đọc qua loạt bài này, có một [môi trường thử nghiệm](http://ramdajs.com/repl/) trên trang web Ramda.

## Hàm

Như tên có thể gợi ý, lập trình hàm có rất nhiều việc để làm với các hàm. Chúng ta sẽ định nghĩa một hàm như một đoạn code có thể tái sử dụng, được gọi với không hoặc nhiều đối số và trả về một kết quả.

Đây là một hàm đơn giản trong JavaScript:

```js
function double(x) {
  return x * 2
}
```

Với các hàm mũi tên \(=&gt;\) của ES6, bạn có thể viết cùng một hàm dễ dàng hơn nhiều. Tôi đề cập đến điều này ngay bây giờ, bởi vì chúng tôi ta sẽ sử dụng rất nhiều hàm mũi tên sau này.

```js
const double = x => x * 2
```

Hầu như mọi ngôn ngữ đều có một số hỗ trợ cho các hàm.

Một số ngôn ngữ đi xa hơn và cung cấp hỗ trợ cho các hàm như cấu trúc first-class. Nói về "first-class", ý của tôi là các hàm có thể được sử dụng trong cùng một cách như các kiểu giá trị khác. Bạn có thể:

* Tham khảo chúng từ hằng số và biến
* Truyền chúng như các tham số cho các hàm khác
* Trả lại chúng như kết quả từ các chức năng khác

JavaScript là một ngôn ngữ như vậy, và chúng tôi sẽ tận dụng điều đó.

## Hàm thuần khiết

Khi viết các chương trình chức năng, điều quan trọng là nó làm việc chủ yếu với cái gọi là các hàm "thuần khiết" \(pure functions\).

Các hàm thuần khiết là những hàm không có hiệu ứng phụ \(side effects\). Họ không gán cho bất kỳ các biến bên ngoài, chúng không tiêu thụ đầu vào, chúng không sản xuất đầu ra, chúng không đọc hoặc ghi vào cơ sở dữ liệu, chúng không sửa đổi các tham số chúng được truyền, vv

Ý tưởng cơ bản là, nếu bạn gọi một hàm với các đầu vào giống nhau nhiều lần, bạn luôn nhận được kết quả giống nhau.

Bạn chắc chắn có thể làm một số thứ với các hàm không thuần khiết \(và bạn phải, nếu chương trình của bạn muốn làm bất cứ điều gì thú vị\), nhưng phần lớn bạn sẽ muốn giữ cho hầu hết các hàm của bạn trở nên thuần khiết.

## Tính bất biến

Một khái niệm quan trọng khác trong lập trình chức năng là "tính bất biến" \(Immutability\). Điều đó có nghĩa là gì? "Không thay đổi" có nghĩa là "không thể thay đổi được".

Khi tôi làm việc trong một kiểu không thay đổi, một khi tôi khởi tạo một giá trị hoặc một đối tượng tôi không bao giờ có thể thay đổi nó một lần nữa. Điều đó có nghĩa là sẽ không có sự thay đổi các phần tử của một mảng hoặc các thuộc tính của một đối tượng.

Thay vào đó, nếu tôi cần phải thay đổi một cái gì đó trong một mảng hoặc đối tượng, tôi sẽ trả lại một bản sao mới của nó với giá trị thay đổi. Các bài viết sau sẽ nói về điều này một cách chi tiết.

Tính không thay đổi luôn đi cùng với các hàm thuần khiết. Vì các hàm thuần khiết không được phép có các hiệu ứng phụ, chúng không được phép thay đổi cấu trúc dữ liệu bên ngoài. Chúng buộc phải làm việc với dữ liệu theo cách không thể thay đổi.

## Bắt đầu từ đâu?

Cách dễ nhất để bắt đầu suy nghĩ theo hướng lập trình hàm là bắt đầu thay thế các vòng lặp bằng tập hợp các hàm lặp.

Nếu bạn đến từ một ngôn ngữ khác mà có các hàm này \(Ruby và Smalltalk là hai ví dụ\), bạn có thể đã quen thuộc với chúng.

Martin Fowler có một vài bài viết tuyệt vời về "Tập hợp đường ống" về [cách sử dụng các hàm này](http://martinfowler.com/articles/collection-pipeline/) và [cách tái cấu trúc code hiện có vào các tập hợp đường ống](http://martinfowler.com/articles/refactoring-pipelines.html).

Lưu ý rằng tất cả các hàm này \(ngoại trừ _reject_\) đều có sẵn trên _Array.prototype_, vì vậy bạn không cần Ramda để bắt đầu sử dụng chúng. Tuy nhiên, tôi sẽ sử dụng phiên bản của Ramda cho phù hợp với phần còn lại của loạt bài này.









Nguồn: [Thinking in Ramda: Getting started](http://randycoulman.com/blog/2016/05/24/thinking-in-ramda-getting-started/)

