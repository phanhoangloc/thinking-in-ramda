# LENSES

Bài đăng này là Phần 8 của loạt bài về lập trình chức năng được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 6](//immutability-object.md) và [Phần 7](//immutability-array.md), chúng ta đã học cách đọc, cập nhật và chuyển đổi các thuộc tính của đối tượng và các phần tử mảng theo cách declarative và bất biến.

Ramda cung cấp một công cụ tổng quát hơn để thực hiện các tác vụ này, lens \(ống kính\).

## LENS LÀ GÌ?

Lens kết hợp một hàm "getter" và một hàm "setter" vào một đơn vị. Ramda cung cấp một bộ các hàm để làm việc với lens.

Chúng ta có thể nghĩ đến lens như một cái gì đó tập trung vào một phần cụ thể của một cấu trúc dữ liệu lớn hơn.

## LÀM THẾ NÀO ĐỂ TẠO MỘT LENS?

Cách phổ biến nhất để tạo một lens ở Ramda là dùng hàm `lens`. `lens` nhận vào một hàm getter, một hàm setter và trả về lens mới.

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

Ở đây chúng ta sử dụng `prop` và `path` như các hàm getter, `assoc` và `assocPath` như các hàm setter.

Lưu ý rằng chúng ta phải sao chép các thuộc tính và đối số đường dẫn đến các hàm này. May mắn thay, Ramda cung cấp các phím tắt cho các trường hợp sử dụng phổ biến nhất của lens: `lensProp`, `lensPath`, và `lensIndex`.

* `lensProp` tạo ra một lens tập trung vào một thuộc tính của một đối tượng.
* `lensPath` tạo ra một lens tập trung vào một thuộc tính lồng nhau của một đối tượng.
* `lensIndex` tạo ra một lens tập trung vào một phần tử của mảng.

Chúng ta có thể viết lại lens ở trên với `lensProp` và `lensPath`:

```
const nameLens = lensProp('name')
const twitterLens = lensPath(['socialMedia', 'twitter'])
```

Nó đơn giản hơn rất nhiều và không bị trùng lắp. Trong thực tế, tôi hầu như không bao giờ cần phải sử dụng hàm `lens`.

## Tôi có thể làm gì với nó?

OK, tuyệt vời, chúng ta đã tạo ra một số lens. Chúng ta có thể làm gì với chúng?

Ramda cung cấp ba hàm để làm việc với lens.

* `view` đọc giá trị của lens.
* `set` cập nhật giá trị của lens.
* `over` áp dụng một hàm chuyển đổi cho lens.

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

Lưu ý rằng `set` và `over` trả lại toàn bộ đối tượng với thuộc tính của lens được sửa đổi theo chỉ định.

## KẾT LUẬN

Các lens có thể hữu ích nếu chúng ta có một cấu trúc dữ liệu phức tạp mà chúng ta muốn trừu tượng hoá. Thay vì phơi bày cấu trúc hoặc cung cấp một bộ getter, setter, và hàm chuyển đổi cho mọi thuộc tính, thay vào đó chúng ta có thể cung cấp các lens.

Code sau đó có thể làm việc với cấu trúc dữ liệu của chúng ta bằng cách sử dụng `view`, `set` và `over` mà không cần phải kết hợp với hình dạng chính xác của cấu trúc.

## TIẾP THEO

Bây giờ chúng ta đã học được rất nhiều thứ mà Ramda cung cấp; chắc chắn đủ để làm hầu hết những gì chúng ta cần làm trong các chương trình của chúng ta. Tổng kết đánh giá series này và đề cập đến một số chủ đề khác mà chúng ta có thể muốn khám phá thêm.

