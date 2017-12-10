DRAFT VERSION

Bài đăng này là Phần 8 của loạt bài về lập trình chức năng được gọi là Tư duy trong Ramda.

Trong Phần 6 và Phần 7, chúng ta đã học cách đọc, cập nhật và chuyển đổi thuộc tính đối tượng và các phần tử mảng theo một cách khai báo, không thay đổi.

Ramda cung cấp một công cụ tổng quát hơn để thực hiện các hoạt động này, ống kính.

Lens là gì?

Một ống kính kết hợp một hàm "getter" và một hàm "setter" vào một đơn vị. Ramda cung cấp một bộ các chức năng để làm việc với ống kính.

Chúng ta có thể nghĩ đến một ống kính như một cái gì đó tập trung vào một phần cụ thể của một cấu trúc dữ liệu lớn hơn.

Làm thế nào để Tạo một Lens?

Cách chung nhất để tạo một ống kính ở Ramda là với chức năng ống kính. ống kính lấy một hàm getter và một hàm setter và trả về ống kính mới.

```
const person = {
  name: 'Randy',
  socialMedia: {
    github: 'randycoulman',
    twitter: '@randycoulman'
  }
}

const nameLens = lens(prop('name'), assoc('name'))
const twitterLens = lens(
  path(['socialMedia', 'twitter']),
  assocPath(['socialMedia', 'twitter'])
)
```

Ở đây chúng ta đang sử dụng prop và path như các hàm getter của chúng ta và assoc và assocPath như các hàm setter của chúng ta.

Lưu ý rằng chúng ta phải sao chép các thuộc tính và đối số đường dẫn đến các hàm này. May mắn thay, Ramda cung cấp các phím tắt đẹp cho việc sử dụng phổ biến nhất của ống kính: lensProp, lensPath, và lensIndex.

lensProp tạo ra một ống kính tập trung vào một thuộc tính của một đối tượng.

lensPath tạo ra một ống kính tập trung vào một thuộc tính lồng nhau của một đối tượng.

lensIndex tạo ra một ống kính tập trung vào một phần tử của mảng.

Chúng tôi có thể viết lại ống kính của chúng tôi ở trên với lensProp và lensPath:

```
const nameLens = lensProp('name')
const twitterLens = lensPath(['socialMedia', 'twitter'])
```

Đó là một đơn giản hơn rất nhiều và được thoát khỏi trùng lắp. Trong thực tế, tôi thấy rằng tôi hầu như không bao giờ cần phải sử dụng chức năng ống kính chung.

Tôi có thể làm gì với nó?

OK, tuyệt vời, chúng tôi đã tạo ra một số ống kính. Chúng ta có thể làm gì với họ?

Ramda cung cấp ba chức năng để làm việc với ống kính.

xem đọc giá trị của ống kính.

thiết lập cập nhật giá trị của ống kính.

over áp dụng một chức năng chuyển đổi cho ống kính.

```
view(nameLens, person) // => 'Randy'

set(twitterLens, '@randy', person)
// => {
//   name: 'Randy',
//   socialMedia: {
//     github: 'randycoulman',
//     twitter: '@randy'
//   }
// }

over(nameLens, toUpper, person)
// => {
//   name: 'RANDY',
//   socialMedia: {
//     github: 'randycoulman',
//     twitter: '@randycoulman'
//   }
// }
```

Lưu ý rằng thiết lập và trả lại toàn bộ đối tượng với thuộc tính tập trung của ống kính được sửa đổi theo quy định.

Phần kết luận

Các ống kính có thể hữu ích nếu chúng ta có một cấu trúc dữ liệu phức tạp mà chúng ta muốn trừu tượng khỏi mã gọi. Thay vì phơi bày cấu trúc hoặc cung cấp một bộ getter, setter, và biến áp cho mọi tài sản dễ tiếp cận, thay vào đó chúng ta có thể phơi ra thấu kính.

Mã khách hàng sau đó có thể làm việc với cấu trúc dữ liệu của chúng tôi bằng cách sử dụng chế độ xem, đặt và hơn mà không cần phải kết hợp với hình dạng chính xác của cấu trúc.

Kế tiếp

Bây giờ chúng ta đã học được rất nhiều thứ mà Ramda cung cấp; chắc chắn đủ để làm hầu hết những gì chúng ta cần làm trong các chương trình của chúng tôi. Tóm tắt đánh giá chuỗi và đề cập đến một số chủ đề khác mà chúng tôi có thể muốn tự khám phá.

