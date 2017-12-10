DRAFT VERSION

Bài đăng này hoàn thành một loạt về lập trình chức năng được gọi là Tư duy trong Ramda.

Trong tám bài viết vừa qua, chúng tôi đã nói về thư viện JavaScript của Ramda cung cấp các chức năng để làm việc với JavaScript theo cách chức năng, khai báo và không thay đổi.

Trong loạt bài này, chúng tôi đã biết rằng Ramda có một số nguyên tắc cơ bản dẫn dắt API của nó:

Dữ liệu cuối: Hầu như tất cả các chức năng lấy tham số dữ liệu như là tham số cuối cùng.

Currying: Hầu như mọi chức năng trong Ramda đều được "xào". Nghĩa là, bạn có thể gọi một hàm chỉ với một tập con các đối số yêu cầu, và nó sẽ trả về một hàm mới lấy các đối số còn lại. Một khi tất cả các đối số được cung cấp, chức năng ban đầu được gọi ra.

Hai nguyên tắc này cho phép chúng tôi viết mã chức năng rất rõ ràng kết hợp các khối xây dựng cơ bản thành các hoạt động mạnh hơn.

Tóm lược

Để tham khảo, đây là một bản tóm tắt nhanh của loạt bài viết.

Bắt đầu giới thiệu với chúng tôi về ý tưởng về chức năng, chức năng thuần túy và tính không thay đổi. Sau đó chúng tôi bắt đầu bằng cách nhìn vào các chức năng lặp lại bộ sưu tập như bản đồ, lọc và giảm.

Kết hợp Chức năng cho chúng ta thấy rằng chúng ta có thể kết hợp các chức năng bằng nhiều cách khác nhau bằng cách sử dụng các công cụ như cả hai, hoặc, đường ống, và soạn.

Một phần Ứng dụng giúp chúng tôi phát hiện ra rằng chỉ có thể cung cấp một số đối số cho một hàm, có thể hữu ích, cho phép một chức năng sau này cung cấp phần còn lại. Chúng tôi sử dụng một phần và cà ri để giúp chúng tôi với điều này và tìm hiểu về lật và giữ chỗ \(\_\_\).

Lập trình tuyên bố dạy chúng ta về sự khác biệt giữa lập trình bắt buộc và khai báo. Chúng ta học cách sử dụng thay thế tuyên bố của Ramda cho số học, so sánh, logic và điều kiện.

Pointfree Style giới thiệu với chúng tôi về ý tưởng của phong cách pointfree, còn được gọi là lập trình ngầm. Trong phong cách pointfree, chúng tôi không thực sự thấy tham số dữ liệu mà chúng tôi đang hoạt động; nó ngầm. Các chương trình của chúng tôi bao gồm các khối xây dựng đơn giản, nhỏ được kết hợp với nhau để làm những gì chúng tôi cần. Chỉ khi nào kết thúc chúng ta mới áp dụng các hàm hợp chất của chúng ta với dữ liệu thực tế.

Tính bất biến và các đối tượng trả về cho chúng ta ý tưởng làm việc khai báo, lần này cho chúng ta những công cụ chúng ta cần phải đọc, cập nhật, xoá và chuyển đổi các thuộc tính của các đối tượng.

Tính bất biến và Mảng tiếp tục chủ đề và cho chúng ta thấy làm thế nào để làm tương tự cho các mảng.

Ống kính kết luận bằng cách giới thiệu khái niệm ống kính, một cấu trúc cho phép chúng ta tập trung vào một phần nhỏ của một cấu trúc dữ liệu lớn hơn. Sử dụng chế độ xem, đặt, và hơn, chúng ta có thể đọc, cập nhật và chuyển đổi giá trị tập trung trong ngữ cảnh của cấu trúc dữ liệu lớn hơn của nó.

Tiếp theo là gì?

Chúng tôi đã không bao gồm tất cả các phần của Ramda trong loạt bài này. Đặc biệt, chúng tôi đã không nói về các chức năng để làm việc với chuỗi, và chúng tôi đã không nói về những khái niệm tiên tiến hơn như đầu dò.

Để tìm hiểu thêm về những gì Ramda có thể làm, tôi khuyên bạn nên đọc kỹ tài liệu. Có rất nhiều thông tin ở đó. Tất cả các chức năng được nhóm lại theo loại dữ liệu mà chúng hoạt động, mặc dù có sự chồng chéo nhau. Ví dụ, một số các chức năng mảng cũng sẽ làm việc trên dây, và bản đồ hoạt động trên cả mảng và các đối tượng.

Nếu bạn quan tâm đến các chủ đề chức năng nâng cao hơn, đây là một số nơi bạn có thể đi:

Transducers: Có một bài giới thiệu tốt về phân tích các bản ghi với các đầu dò.

Các loại dữ liệu đại số: Nếu bạn đã đọc nhiều về lập trình chức năng, bạn sẽ nghe về các loại đại số và thuật ngữ như "Functor", "Applicative" và "Monad". Nếu bạn quan tâm đến việc khám phá những ý tưởng này trong bối cảnh của Ramda, hãy kiểm tra dự án ramda-tưởng tượng, thực hiện một số loại dữ liệu phù hợp với Đặc điểm Đất Ảo \(aka Đặc tả JavaScript Đại số\).

