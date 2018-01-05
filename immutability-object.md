# TÍNH BẤT BIẾN VÀ ĐỐI TƯỢNG

Bài viết này là phần 6 của loạt bài về lập trình hàm được gọi là [Thinking in Ramda](http://randycoulman.com/blog/categories/thinking-in-ramda/).

Trong [Phần 5](/point-free-style.md), chúng ta đã nói về cách viết các hàm của chúng ta theo kiểu "pointfree" hoặc "tacit", khi mà đối số chính của hàm không được hiển thị rõ ràng.

Vào thời điểm đó, chúng ta đã không thể chuyển đổi tất cả các hàm thành pointfree bởi vì chúng ta thiếu một số công cụ. Đã đến lúc học các công cụ này.

## ĐỌC THUỘC TÍNH CỦA ĐỐI TƯỢNG

Chúng ta hãy nhìn lại ví dụ về các cử tri hợp lệ trong [Phần 5](/point-free-style.md):

```
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Như bạn thấy, chúng ta đã chuyển đổi `isCitizen` và `isEligibleToVote` thành pointfree, nhưng chúng ta không thể làm điều đó với ba hàm đầu tiên.

Như chúng ta đã học trong [Phần 4](/declarative-programming.md), chúng ta có thể làm cho các hàm declarative hơn bằng `equals` và `gte`. Hãy bắt đầu từ đó.

```
const wasBornInCountry = person => equals(person.birthCountry, OUR_COUNTRY)
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => gte(person.age, 18)
```

Để làm cho các hàm này pointfree, chúng ta cần một cách để xây dựng hàm mà sau đó chúng ta có thể áp dụng cho `person`. Vấn đề là chúng ta cần truy cập vào các thuộc tính của `person` và cách duy nhất chúng ta biết là làm điều đó theo cách imperative.

### prop

May mắn thay, Ramda có thể giúp chúng ta. Nó cung cấp hàm `prop` để truy cập các thuộc tính của một đối tượng.

Sử dụng `prop`, chúng ta có thể biến `person.birthCountry` thành `prop('birthCountry', person)`. Hãy bắt đầu với điều đó.

```
const wasBornInCountry = person => equals(prop('birthCountry', person), OUR_COUNTRY)
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(prop('age', person), 18)
```

Wow, trông bây giờ tệ hơn nhiều. Nhưng chúng ta hãy tiếp tục với refactoring. Thứ nhất, chúng ta hãy hoán đổi thứ tự của các đối số mà chúng ta truyền cho equals để `prop` đến cuối cùng. equals hoạt động như nhau đối với cả hai thứ tự, vì vậy điều này là an toàn.

```
const wasBornInCountry = person => equals(OUR_COUNTRY, prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(prop('age', person), 18)
```

Tiếp theo, chúng ta hãy sử dụng tính chất curried của equals và gte để tạo các hàm mới mà chúng ta sẽ áp dụng cho kết quả của các lệnh gọi prop.

```
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(__, 18)(prop('age', person))
```

Điều đó vẫn còn tồi tệ hơn một chút, nhưng hãy tiếp tục nào. Hãy tận dụng lợi thế của currying một lần nữa với tất cả các lệnh gọi prop.

```
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry')(person))
const wasNaturalized = person => Boolean(prop('naturalizationDate')(person))
const isOver18 = person => gte(__, 18)(prop('age')(person))
```

Tệ hơn nữa. Nhưng bây giờ chúng ta thấy một khuôn mẫu quen thuộc. Cả ba hàm của chúng ta có cùng hình dạng với f\(g\(person\)\), và chúng ta biết từ Phần 2 rằng điều này tương đương với compose\(f, g\)\(person\).

Hãy tận dụng điều đó.

```
const wasBornInCountry = person => compose(equals(OUR_COUNTRY), prop('birthCountry'))(person)
const wasNaturalized = person => compose(Boolean, prop('naturalizationDate'))(person)
const isOver18 = person => compose(gte(__, 18), prop('age'))(person)
```

Bây giờ tất cả ba hàm trông giống như person =&gt; f\(person\). Chúng ta biết từ Phần 5 rằng chúng ta có thể làm cho các hàm này pointfree.

```
const wasBornInCountry = compose(equals(OUR_COUNTRY), prop('birthCountry'))
const wasNaturalized = compose(Boolean, prop('naturalizationDate'))
const isOver18 = compose(gte(__, 18), prop('age'))
```

Không rõ ràng khi chúng ta bắt đầu rằng các phương pháp của chúng ta đã làm hai việc khác nhau. Cả hai đều đang truy cập thuộc tính của một đối tượng và thực hiện một số tác vụ dựa trên giá trị của thuộc tính đó. Việc tái cấu trúc này theo phong cách pointfree đã làm cho điều đó rất rõ ràng.

Hãy nhìn vào một số công cụ khác mà Ramda cung cấp để làm việc với các đối tượng.

### pick

Trong khi prop đọc một thuộc tính từ một đối tượng và trả về giá trị, pick đọc nhiều thuộc tính từ một đối tượng và trả về một đối tượng mới chỉ với các thuộc tính đó.

Ví dụ: nếu muốn lấy tên và tuổi của một người, chúng ta có thể sử dụng pick\(\['name', 'age'\), person\).

### has

Nếu chúng ta chỉ muốn biết nếu một đối tượng có một thuộc tính hay không mà không cần đọc giá trị của nó thì chúng ta có thể sử dụng has để kiểm tra thuộc tính của chính nó, và hasIn để kiểm tra chuỗi prototype: has\('name', person\).

### path

Trong khi prop đọc một thuộc tính từ một đối tượng, path đi vào các đối tượng lồng nhau. Ví dụ: chúng ta có thể truy cập mã zip từ cấu trúc sâu hơn dưới dạng path\(\['address', 'zipCode'\), person\).

Lưu ý rằng path là khoan dung hơn prop. path sẽ trả lại undefined nếu bất cứ điều gì dọc theo con đường \(bao gồm cả các đối số ban đầu\) là null hoặc undefined trong khi prop sẽ gây ra một lỗi.

### propOr / pathOr

propOr và pathOr tương tự như prop và path kết hợp với defaultTo. Chúng cho phép bạn cung cấp giá trị mặc định để sử dụng nếu không thể tìm thấy thuộc tính hoặc đường dẫn trong đối tượng đích.

Ví dụ: chúng ta có thể cung cấp một placeholder khi chúng ta không biết tên của một người: propOr\('&lt;Unnamed&gt;', 'name', person\). Lưu ý rằng không giống như prop, propOr sẽ không gây ra một lỗi nếu person là null hoặc undefined; nó sẽ trả về giá trị mặc định.

### keys / values

phím trả về một mảng có chứa tên của tất cả các thuộc tính của chính nó trong một đối tượng. các giá trị trả về các giá trị của các thuộc tính đó. Các chức năng này có thể hữu ích khi kết hợp với các chức năng lặp lại bộ sưu tập mà chúng ta đã học được trong Phần 1.

Thêm, cập nhật và xóa thuộc tính

Bây giờ chúng ta có rất nhiều công cụ để đọc từ các đối tượng một cách khai báo, nhưng khi nào chúng ta muốn thay đổi thì sao?

Vì tính bất biến là quan trọng, chúng ta không muốn thay đổi trực tiếp các đối tượng. Thay vào đó, chúng tôi muốn trả lại các đối tượng mới đã được thay đổi theo cách chúng tôi muốn.

Một lần nữa, Ramda cung cấp rất nhiều sự giúp đỡ cho chúng tôi.

assoc / assocPath

Khi lập trình bắt buộc, chúng ta có thể thiết lập hoặc thay đổi tên của một người với toán tử gán: person.name = 'New name'.

Trong thế giới chức năng, không thay đổi của chúng ta, chúng ta sử dụng assoc thay vì: const updatedPerson = assoc \('name', 'New name', person\).

assoc trả về một đối tượng mới với giá trị thuộc tính được cập nhật hoặc cập nhật, để đối tượng ban đầu không thay đổi.

Ngoài ra còn có assocPath để cập nhật một thuộc tính lồng nhau: const updatedPerson = assocPath \(\['address', 'zipcode', '97504', person\).

dissoc / dissocPath / bỏ qua

Điều gì về việc xóa tài sản? Nhu cầu, chúng tôi có thể muốn nói xóa person.age. Trong Ramda, chúng ta sẽ sử dụng dissoc: const updatedPerson = dissoc \('age', person\).

dissocPath tương tự, nhưng hoạt động sâu hơn vào cấu trúc của đối tượng: dissocPath \(\['address', 'zipCode'\), person\).

Cũng có thể bỏ qua, có thể loại bỏ một số tài sản cùng một lúc. const updatedPerson = bỏ qua \(\['tuổi', 'birthCountry'\], người\).

Lưu ý rằng chọn và bỏ qua là khá tương tự và bổ sung cho nhau độc đáo. Chúng rất tiện dụng cho danh sách trắng \(chỉ giữ lại các thuộc tính này bằng cách chọn\) hoặc danh sách đen \(loại bỏ thuộc tính này bằng cách bỏ qua\).

Chuyển đổi Thuộc tính

Bây giờ chúng ta đã biết đủ để làm việc với các vật thể theo một cách khai báo và không thay đổi. Hãy viết một chức năng, chào mừngBirthday, cập nhật tuổi của một người vào sinh nhật của họ.

```
const nextAge = compose(inc, prop('age'))
const celebrateBirthday = person => assoc('age', nextAge(person), person)
```

Đây là một mẫu khá phổ biến. Thay vì cập nhật thuộc tính đến một giá trị mới được biết, chúng tôi thực sự muốn chuyển đổi giá trị bằng cách áp dụng một hàm cho giá trị cũ như chúng ta đã làm ở đây.

Tôi không biết một cách hay để viết ra điều này với sự trùng lặp ít hơn và trong phong cách phi tuyến miễn phí cho các công cụ chúng tôi biết.

Ramda để giải cứu một lần nữa với chức năng tiến triển của nó. Tôi giới thiệu tiến triển trong một bài trước.

tiến triển có một đối tượng xác định một chức năng chuyển đổi cho mỗi thuộc tính được chuyển đổi. Hãy tái tổ chức lại RefactorBirthday để sử dụng tiến triển:

```
const celebrateBirthday = evolve({ age: inc })
```

Mã này nói để phát triển đối tượng đích \(không được hiển thị ở đây vì phong cách pointfree\) bằng cách tạo ra một đối tượng mới có cùng các thuộc tính và giá trị, nhưng độ tuổi của nó có được bằng cách áp dụng inc với giá trị tuổi gốc.

tiến triển có thể chuyển đổi nhiều thuộc tính cùng một lúc và ở nhiều cấp độ làm tổ. Đối tượng chuyển đổi có thể có cùng hình dạng với đối tượng đang được tiến hóa và tiến triển sẽ đệ quy qua các cấu trúc, áp dụng các chức năng chuyển đổi khi nó đi.

Lưu ý rằng phát triển sẽ không thêm thuộc tính mới; nếu bạn chỉ định một sự chuyển đổi cho một thuộc tính không xuất hiện trong đối tượng đích, thì tiến triển sẽ chỉ bỏ qua nó.

Tôi đã phát hiện ra rằng tiến triển đã nhanh chóng trở thành một workhorse trong các ứng dụng của tôi.

Hợp nhất Đối tượng

Đôi khi, bạn sẽ muốn hợp nhất hai đối tượng lại với nhau. Một trường hợp phổ biến là khi bạn có một chức năng mà có các tùy chọn được đặt tên và bạn muốn kết hợp các tùy chọn với một bộ các tùy chọn mặc định. Ramda cung cấp hợp nhất cho mục đích này.

```
function f(a, b, options = {}) {
  const defaultOptions = { value: 42, local: true }
  const finalOptions = merge(defaultOptions, options)
}
```

hợp nhất trả về một đối tượng mới có chứa tất cả các thuộc tính và giá trị từ cả hai đối tượng. Nếu cả hai đối tượng có cùng một thuộc tính, giá trị từ đối số thứ hai sẽ được sử dụng.

Có tranh chấp thứ hai chiến thắng có ý nghĩa khi sử dụng hợp nhất của chính nó, nhưng ít hơn trong một tình huống đường ống. Trong trường hợp đó, bạn thường thực hiện một loạt các biến đổi đối với một đối tượng và một trong những biến đổi đó là hợp nhất một số giá trị thuộc tính mới. Trong trường hợp này, bạn muốn đối số đầu tiên giành chiến thắng.

Đơn giản chỉ cần sử dụng hợp nhất \(newValues\) trong đường ống sẽ không làm những gì bạn mong đợi.

Đối với tình huống này, tôi thường định nghĩa một hàm tiện ích gọi là reverseMerge. Nó có thể được viết như const reversMerge = flip \(merge\). Nhớ lại rằng lật ngược hai đối số đầu tiên của hàm mà nó được áp dụng.

hợp nhất thực hiện một sự hợp nhất nông. Nếu các đối tượng đang được hợp nhất cả hai đều có một thuộc tính có giá trị là một đối tượng phụ, những đối tượng con đó sẽ không được hợp nhất. Ramda hiện không có khả năng "hợp nhất sâu", nơi các đối tượng con được hợp nhất đệ quy.

Lưu ý rằng hợp nhất chỉ mất hai đối số. Nếu bạn muốn hợp nhất nhiều đối tượng vào một, có mergeAll có một mảng các đối tượng sẽ được hợp nhất.

Phần kết luận

Điều này đã cho chúng ta một bộ công cụ đẹp để làm việc với các đối tượng theo một cách khai báo và không thay đổi. Bây giờ chúng ta có thể đọc, thêm, cập nhật, xoá và chuyển đổi thuộc tính trong các đối tượng mà không thay đổi đối tượng gốc. Và chúng ta có thể làm những điều này theo một cách hoạt động khi kết hợp các chức năng.

Kế tiếp

Bây giờ chúng ta có thể làm việc với các đối tượng một cách không thay đổi, những gì về mảng? Tính không thay đổi và Mảng hiển thị chúng ta như thế nào.

