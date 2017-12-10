* # TỔNG KẾT

DRAFT VERSION

Bài viết này hoàn thành series về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong tám bài viết vừa qua, chúng tôi đã nói về thư viện Ramda cung cấp các hàm để làm việc với JavaScript theo hướng lập trình hàm, declarative và bất biến.

Trong series này, chúng ta đã biết rằng Ramda có một số nguyên tắc cơ bản:

* Dữ liệu cuối cùng: Hầu như tất cả các hàm nhận tham số dữ liệu như là tham số cuối cùng.
* Currying: Hầu như mọi hàm trong Ramda đều được "curried". Nghĩa là, bạn có thể gọi một hàm chỉ với một tập con các tham số yêu cầu, và nó sẽ trả về một hàm mới nhận vào các tham số còn lại. Một khi tất cả các tham số được cung cấp, hàm ban đầu sẽ được thực thi.

Hai nguyên tắc này cho phép chúng ta viết code theo hướng lập trình hàm rất rõ ràng, kết hợp các khối xây dựng cơ bản thành các tác vụ hữu ích hơn.

## TÓM LẠI

Để tham khảo, đây là một bản tóm tắt nhanh của series.

* [Bắt đầu](//getting-started.md) giới thiệu với chúng ta ý tưởng về hàm, hàm thuần túy và tính không thay đổi. Sau đó chúng ta bắt đầu bằng cách nhìn vào các hàm lặp trên tập hợp như map, filter và reduce.
* [Kết hợp hàm](/combining-functions.md) cho thấy rằng chúng ta có thể kết hợp các hàm bằng nhiều cách khác nhau bằng cách sử dụng các công cụ như both, either, pipe, và compose.
* Áp dụng một phần giúp chúng ta phát hiện ra rằng chỉ có thể cung cấp một số tham số cho một hàm, có thể hữu ích, cho phép một hàm sau này cung cấp phần còn lại. Chúng ta sử dụng partial  và curry để giúp chúng ta với điều này và tìm hiểu về flip và placeholder \(\_\_\).
* Lập trình tuyên bố dạy chúng ta về sự khác biệt giữa lập trình bắt buộc và khai báo. Chúng ta học cách sử dụng thay thế tuyên bố của Ramda cho số học, so sánh, logic và điều kiện.
* Pointfree Style giới thiệu với chúng tôi về ý tưởng của phong cách pointfree, còn được gọi là lập trình ngầm. Trong phong cách pointfree, chúng tôi không thực sự thấy tham số dữ liệu mà chúng tôi đang hoạt động; nó ngầm. Các chương trình của chúng tôi bao gồm các khối xây dựng đơn giản, nhỏ được kết hợp với nhau để làm những gì chúng tôi cần. Chỉ khi nào kết thúc chúng ta mới áp dụng các hàm hợp chất của chúng ta với dữ liệu thực tế.
* Tính bất biến và các đối tượng trả về cho chúng ta ý tưởng làm việc khai báo, lần này cho chúng ta những công cụ chúng ta cần phải đọc, cập nhật, xoá và chuyển đổi các thuộc tính của các đối tượng.
* Tính bất biến và Mảng tiếp tục chủ đề và cho chúng ta thấy làm thế nào để làm tương tự cho các mảng.
* Ống kính kết luận bằng cách giới thiệu khái niệm ống kính, một cấu trúc cho phép chúng ta tập trung vào một phần nhỏ của một cấu trúc dữ liệu lớn hơn. Sử dụng chế độ xem, đặt, và hơn, chúng ta có thể đọc, cập nhật và chuyển đổi giá trị tập trung trong ngữ cảnh của cấu trúc dữ liệu lớn hơn của nó.

## TIẾP THEO LÀ GÌ?

Chúng ta đã không bao gồm tất cả các phần của Ramda trong series này. Đặc biệt, chúng ta đã không nói về các hàm để làm việc với chuỗi, và chúng ta đã không nói về những khái niệm tiên tiến hơn như [transducers](http://ramdajs.com/0.21.0/docs/#transduce).

Để tìm hiểu thêm về những gì Ramda có thể làm, tôi khuyên bạn nên đọc kỹ [tài liệu](http://ramdajs.com/docs/). Có rất nhiều thông tin ở đó. Tất cả các hàm được nhóm lại theo loại dữ liệu mà chúng hoạt động, mặc dù có sự đan xen nhau. Ví dụ, một số các hàm mảng cũng sẽ làm việc trên chuỗi, và `map` hoạt động trên cả mảng và đối tượng.

Nếu bạn quan tâm đến các chủ đề lập trình hàm nâng cao hơn, đây là một số nơi bạn có thể đi đến:

* Transducers: Có một [bài giới thiệu hay](http://simplectic.com/blog/2015/ramda-transducers-logs/) về phân tích logs với các transducers.
* Các kiểu dữ liệu đại số \(ADTs\): Nếu bạn đã đọc nhiều về lập trình hàm, bạn có thể sẽ nghe về các kiểu đại số và thuật ngữ như "Functor", "Applicative" và "Monad". Nếu bạn quan tâm đến việc khám phá những ý tưởng này trong ngữ cảnh của Ramda, hãy xem thử dự án [ramda-fantasy](https://github.com/ramda/ramda-fantasy), nó bổ sung một số loại dữ liệu phù hợp với [Fantasy Land Specification](https://github.com/fantasyland/fantasy-land) \(aka Algebraic JavaScript Specification\).



