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

Wow, trông bây giờ tệ hơn nhiều. Nhưng chúng ta hãy tiếp tục với refactoring. Thứ nhất, chúng ta hãy hoán đổi thứ tự của các đối số mà chúng ta truyền cho `equals` để `prop` đến cuối cùng. `equals` hoạt động như nhau đối với cả hai thứ tự, vì vậy điều này là an toàn.

```
const wasBornInCountry = person => equals(OUR_COUNTRY, prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(prop('age', person), 18)
```

Tiếp theo, chúng ta hãy sử dụng tính chất curried của `equals` và `gte` để tạo các hàm mới mà chúng ta sẽ áp dụng cho kết quả của các lệnh gọi `prop`.

```
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(__, 18)(prop('age', person))
```

Nó thậm chí còn tệ hơn trước, nhưng hãy tiếp tục nào. Hãy tận dụng lợi thế của currying một lần nữa với tất cả các lệnh gọi `prop`.

```
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry')(person))
const wasNaturalized = person => Boolean(prop('naturalizationDate')(person))
const isOver18 = person => gte(__, 18)(prop('age')(person))
```

Tệ hơn nữa. Nhưng bây giờ chúng ta thấy một mẫu quen thuộc. Cả ba hàm của chúng ta có cùng hình dạng với `f(g(person))`, và chúng ta biết từ [Phần 2](/combining-functions.md) rằng điều này tương đương với `compose(f, g)(person)`.

Hãy tận dụng điều đó.

```
const wasBornInCountry = person => compose(equals(OUR_COUNTRY), prop('birthCountry'))(person)
const wasNaturalized = person => compose(Boolean, prop('naturalizationDate'))(person)
const isOver18 = person => compose(gte(__, 18), prop('age'))(person)
```

Bây giờ tất cả ba hàm trông giống như `person => f(person)`. Chúng ta biết từ [Phần 5](/point-free-style.md) rằng chúng ta có thể làm cho các hàm này pointfree.

```
const wasBornInCountry = compose(equals(OUR_COUNTRY), prop('birthCountry'))
const wasNaturalized = compose(Boolean, prop('naturalizationDate'))
const isOver18 = compose(gte(__, 18), prop('age'))
```

Không rõ ràng khi bắt đầu các phương pháp của chúng ta để làm hai việc khác nhau. Cả hai đều truy cập thuộc tính của một đối tượng và thực hiện một số tác vụ dựa trên giá trị của thuộc tính đó. Việc tái cấu trúc này theo phong cách pointfree đã làm cho điều đó rất rõ ràng.

Hãy nhìn vào một số công cụ khác mà Ramda cung cấp để làm việc với các đối tượng.

### pick

Trong khi `prop` đọc một thuộc tính từ một đối tượng và trả về giá trị, `pick` đọc nhiều thuộc tính từ một đối tượng và trả về một đối tượng mới chỉ với các thuộc tính đó.

Ví dụ: nếu muốn lấy tên và tuổi của một người, chúng ta có thể sử dụng `pick(['name', 'age'), person)`.

### has

Nếu chúng ta chỉ muốn biết nếu một đối tượng có một thuộc tính hay không mà không cần đọc giá trị của nó thì chúng ta có thể sử dụng `has` để kiểm tra thuộc tính của chính nó, và `hasIn` để kiểm tra chuỗi prototype: `has('name', person)`.

### path

Trong khi `prop` đọc một thuộc tính từ một đối tượng, `path` đi vào các đối tượng lồng nhau. Ví dụ: chúng ta có thể truy cập mã zip từ cấu trúc sâu hơn dưới dạng `path(['address', 'zipCode'), person)`.

Lưu ý rằng `path` khoan dung hơn `prop`. `path` sẽ trả lại `undefined` nếu bất cứ điều gì dọc theo đường dẫn \(bao gồm cả các đối số ban đầu\) là `null` hoặc `undefined` trong khi `prop` sẽ gây ra một lỗi.

### propOr / pathOr

propOr và pathOr tương tự như prop và path kết hợp với defaultTo. Chúng cho phép bạn cung cấp giá trị mặc định để sử dụng nếu không thể tìm thấy thuộc tính hoặc đường dẫn trong đối tượng đích.

Ví dụ: chúng ta có thể cung cấp một placeholder khi chúng ta không biết tên của một người: propOr\('&lt;Unnamed&gt;', 'name', person\). Lưu ý rằng không giống như prop, propOr sẽ không gây ra một lỗi nếu person là null hoặc undefined; nó sẽ trả về giá trị mặc định.

### keys / values

`keys` trả về một mảng có chứa tên của tất cả các thuộc tính của chính nó trong một đối tượng. `values` trả về các giá trị của các thuộc tính đó. Các hàm này có thể hữu ích khi kết hợp với các hàm lặp trên tập hợp mà chúng ta đã học được trong [Phần 1](//getting-started.md).

## THÊM, CẬP NHẬT VÀ XÓA THUỘC TÍNH

Bây giờ chúng ta có rất nhiều công cụ để đọc từ các đối tượng một cách declarative, nhưng khi chúng ta muốn thay đổi thì sao?

Vì tính bất biến là quan trọng, chúng ta không muốn thay đổi trực tiếp các đối tượng. Thay vào đó, chúng ta muốn trả lại các đối tượng mới đã được thay đổi theo cách chúng ta muốn.

Một lần nữa, Ramda cung cấp rất nhiều sự giúp đỡ cho chúng ta.

### assoc / assocPath

Khi lập trình theo cách imperative, chúng ta có thể thiết lập hoặc thay đổi tên của một người với toán tử gán: `person.name = 'New name'`.

Trong thế giới functional, bất biến của chúng ta, chúng ta sử dụng `assoc`: `const updatedPerson = assoc('name', 'New name', person)`.

`assoc` trả về một đối tượng mới với giá trị thuộc tính được cập nhật, để đối tượng ban đầu không thay đổi.

Ngoài ra còn có `assocPath` để cập nhật một thuộc tính lồng nhau: `const updatedPerson = assocPath(['address', 'zipcode'], '97504', person)`.

### dissoc / dissocPath / omit

Việc xoá thuộc tính thì sao? Theo cách imperative, chúng ta có thể muốn xóa `person.age`. Trong Ramda, chúng ta sẽ sử dụng `dissoc`: `const updatedPerson = dissoc('age', person)`.

`dissocPath` tương tự, nhưng hoạt động sâu hơn vào cấu trúc của đối tượng: `dissocPath(['address', 'zipCode']), person)`.

Cũng có `omit`, có thể loại bỏ một số thuộc tính cùng một lúc. `const updatedPerson = omit(['age', 'birthCountry'], person)`.

Lưu ý rằng `pick` và `omit` là khá tương tự và bổ sung cho nhau. Chúng rất tiện dụng cho white-listing \(chỉ giữ lại các thuộc tính này bằng `pick`\) hoặc black-listing \(loại bỏ các thuộc tính này bằng `omit`\).

### Chuyển đổi các thuộc tính

Bây giờ chúng ta đã biết đủ để làm việc với các đối tượng theo một cách declarative và bất biến. Hãy viết một hàm, `celebrateBirthday`, cập nhật tuổi của một người vào sinh nhật của họ.

```
const nextAge = compose(inc, prop('age'))
const celebrateBirthday = person => assoc('age', nextAge(person), person)
```

Đây là một mẫu \(pattern\) khá phổ biến. Thay vì cập nhật thuộc tính đến một giá trị mới được biết, chúng ta thực sự muốn chuyển đổi giá trị bằng cách áp dụng một hàm cho giá trị cũ như chúng ta đã làm ở đây.

Tôi không biết một cách hay để viết ra điều này với sự trùng lặp ít hơn và theo phong cách pointfree dựa trên các công cụ chúng tôi biết.

Ramda để giải cứu chúng ta một lần nữa với hàm `evolve` của nó. Tôi giới thiệu `evolve` trong một [bài viết trước](http://randycoulman.com/blog/2016/02/16/using-ramda-with-redux/).

`evolve` có một đối tượng xác định một hàm chuyển đổi cho mỗi thuộc tính được chuyển đổi. Hãy tái tổ chức lại `celebrateBirthday` để sử dụng `evolve`:

```
const celebrateBirthday = evolve({ age: inc })
```

Code này để phát triển đối tượng đích \(không được hiển thị ở đây vì phong cách pointfree\) bằng cách tạo ra một đối tượng mới có cùng các thuộc tính và giá trị, nhưng `age` của nó có được bằng cách áp dụng `inc` với giá trị `age` gốc.

`evolve` có thể chuyển đổi nhiều thuộc tính cùng một lúc và ở nhiều cấp độ nesting. Đối tượng chuyển đổi có thể có cùng hình dạng với đối tượng đang được tiến hóa và `evolve` sẽ đệ quy qua các cấu trúc, áp dụng các hàm chuyển đổi khi nó đi.

Lưu ý rằng `evolve` sẽ không thêm thuộc tính mới; nếu bạn chỉ định một sự chuyển đổi cho một thuộc tính không xuất hiện trong đối tượng đích, thì `evolve` sẽ bỏ qua nó.

Tôi đã phát hiện ra rằng `evolve` đã nhanh chóng trở thành một workhorse trong các ứng dụng của tôi.

## HỢP NHẤT CÁC ĐỐI TƯỢNG

Đôi khi, bạn sẽ muốn hợp nhất hai đối tượng lại với nhau. Một trường hợp phổ biến là khi bạn có một hàm mà có các tùy chọn và bạn muốn kết hợp các tùy chọn này với một bộ các tùy chọn mặc định. Ramda cung cấp `merge` cho mục đích này.

```
function f(a, b, options = {}) {
  const defaultOptions = { value: 42, local: true }
  const finalOptions = merge(defaultOptions, options)
}
```

`merge` trả về một đối tượng mới có chứa tất cả các thuộc tính và giá trị từ cả hai đối tượng. Nếu cả hai đối tượng có cùng một thuộc tính, giá trị từ tham số thứ hai sẽ được sử dụng.

Tham số thứ hai chiến thắng có ý nghĩa khi sử dụng `merge` bởi chính nó, nhưng ít có giá trị trong một tình huống đường ống \(pipe\). Trong trường hợp đó, bạn thường thực hiện một loạt các biến đổi đối với một đối tượng và một trong những biến đổi đó là hợp nhất một số giá trị của thuộc tính mới. Trong trường hợp này, bạn muốn tham số đầu tiên giành chiến thắng.

Đơn giản chỉ sử dụng `merge(newValues)` trong đường ống sẽ không làm những gì bạn mong đợi.

Đối với tình huống này, tôi thường định nghĩa một hàm tiện ích gọi là `reverseMerge`. Nó có thể được viết như `const reversMerge = flip(merge)`. Nhớ lại rằng `flip` đảo ngược hai đối số đầu tiên của hàm mà nó được áp dụng.

`merge` thực hiện một sự hợp nhất cạn \(shallow merge\). Nếu các đối tượng đang được hợp nhất cả hai đều có một thuộc tính có giá trị là một đối tượng phụ, những đối tượng con đó sẽ không được hợp nhất. Ramda hiện không có khả năng "hợp nhất sâu", nơi các đối tượng con được hợp nhất đệ quy.

Lưu ý rằng `merge` chỉ nhận hai đối số. Nếu bạn muốn hợp nhất nhiều đối tượng vào một,  `mergeAll` có một mảng các đối tượng sẽ được hợp nhất.

## KẾT LUẬN

Bài viết này đã cho chúng ta một bộ công cụ để làm việc với các đối tượng theo cách declarative và bất biến. Bây giờ chúng ta có thể đọc, thêm, cập nhật, xoá và chuyển đổi thuộc tính trong các đối tượng mà không thay đổi đối tượng gốc. Và chúng ta có thể làm những điều này dựa trên sự kết hợp các hàm.

## TIẾP THEO

Bây giờ chúng ta có thể làm việc với các đối tượng một cách bất biến, còn mảng thì sao? [Tính bất biến và mảng](//immutability-array.md) chỉ ra cho chúng ta như thế nào.

Nguồn: [Thinking in Ramda: Immutability and Objects](http://randycoulman.com/blog/2016/06/28/thinking-in-ramda-immutability-and-objects/)

