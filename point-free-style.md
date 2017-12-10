Point free

Bài đăng này là Phần 5 của loạt bài về lập trình chức năng được gọi là Tư duy trong Ramda.

Trong phần 4, chúng ta đã nói về việc viết code một cách công khai \(nói với máy tính phải làm gì\) thay vì imperative \(nói với máy tính làm thế nào để làm điều đó\).

Bạn có thể nhận thấy rằng một vài chức năng mà chúng ta đã viết \(forever21, drivingAge, and water\) đều lấy một tham số, xây dựng một hàm mới và sau đó áp dụng hàm đó cho tham số.

Đây là một mô hình rất phổ biến trong lập trình chức năng, và một lần nữa Ramda cung cấp các công cụ để làm sạch này lên.

Phong cách không Pointfree

Có hai nguyên tắc hướng dẫn chính của Ramda mà chúng ta đã nói tới trong Phần 3:

Đặt dữ liệu cuối cùng

Cà chua tất cả những thứ

Hai nguyên tắc này dẫn đến một phong cách mà các lập trình viên chức năng gọi là "pointfree". Tôi thích nghĩ về mã pointfree là "Dữ liệu? Dữ liệu gì? Không có dữ liệu ở đây. "

Có một bài đăng blog tuyệt vời, Why Ramda ?, minh họa phong cách pointfree thực sự tốt. Nói một cách rõ ràng, nó có phần tiêu đề như "Where's the Data?", "Tất cả các quyền, đã! Tôi có thể xem một số dữ liệu? ", Và" Tôi chỉ muốn dữ liệu của tôi, Cảm ơn ".

Chúng tôi chưa có các công cụ cần thiết để làm cho tất cả các ví dụ của chúng tôi hoàn toàn miễn phí, nhưng chúng tôi có thể bắt đầu.

Chúng ta hãy nhìn vào mãi mãi21:

```
const forever21 = age => ifElse(gte(__, 21), always(21), inc)(age)
```

Lưu ý rằng độ tuổi chỉ xuất hiện hai lần: một lần trong danh sách đối số và một lần ở cuối của hàm khi chúng ta áp dụng hàm mới được trả về bởi ifElse.

Nếu chúng ta quan tâm trong khi làm việc với Ramda, chúng ta sẽ thấy mẫu này rất nhiều. Nó gần như luôn luôn có nghĩa là có một cách để chuyển đổi các chức năng sang phong cách pointfree.

Hãy xem những gì sẽ như thế nào:

```
const forever21 = ifElse(gte(__, 21), always(21), inc)
```

Và, poof! Chúng tôi chỉ làm cho tuổi biến mất. Phong cách Pointfree. Lưu ý rằng không có sự khác biệt hành vi trong hai phiên bản này. Chúng tôi vẫn trả lại một chức năng cần một lứa tuổi, nhưng bây giờ chúng tôi không rõ ràng xác định tham số độ tuổi.

Chúng ta có thể làm tương tự với AlwaysDrivingAge và nước.

Khi chúng tôi rời đi, AlwaysDrivingAge trông như sau:

```
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), identity)(age)
```

Chúng ta có thể áp dụng cùng một sự biến đổi để làm cho nó trở nên không có điểm.

```
const alwaysDrivingAge = when(lt(__, 16), always(16))
```

Và đây là nơi chúng tôi để lại nước:

    const water = temperature => cond([
      [equals(0),   always('water freezes at 0°C')],
      [equals(100), always('water boils at 100°C')],
      [T,           temp => `nothing special happens at ${temp}°C`]
    ])(temperature)

Và đây là, phong cách pointfree:

    const water = cond([
      [equals(0),   always('water freezes at 0°C')],
      [equals(100), always('water boils at 100°C')],
      [T,           temp => `nothing special happens at ${temp}°C`]
    ])

## Multi-argument Functions {#multi-argument-functions}



