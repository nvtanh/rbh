#### The Object Model

## Open Class

Trong Ruby không phân biệt giữa code để định nghĩa một Class và code những kiểu khác. Bạn có thể để bất kì code gì mà bạn muốn trong một class. Ví dụ:
```
3.times do
  class C
    puts "Hello"
  end
end
```

kết quả
Hello
Hello
Hello

Ruby thực hiện code với ở trong class cũng giống như cách thực thi với các code khác.
Vậy code ở trên có nghĩa là bạn đã định nghĩa 3 lớp có cùng một tên là C? Câu trả lời là không :)
Bạn hãy xem ví dụ sau:
```
class D
  def x; "x"; end
end

class D
  def y; "y"; end
end

obj = D.new
obj.x    # => "x"
obj.y    # => "y"

```
Khi lần đầu tiên khởi tạo class D thì trước đó chưa có class nào tên D được định nghĩa trước đó. Vì vậy, Ruby sẽ định nghĩa ra một class D với method x().
Với lần thứ 2 khởi tạo class D thì do trước đó class D đã tồn tại trước đó, vì vậy Ruby không cần khởi tạo lại nó nữa. Thay vào đó nó chỉ reopen class đã tồn tại và định nghĩa thêm một method nữa tên là y().

Như vậy điều quan trọng ở đây, là bạn luôn có thể reopen một lớp đã tồn tại, kể cả những lớp base, thư viện như là String, Array, ... và thay đổi chúng một cách nhanh chóng. Kĩ thuật này gọi là Open Class.

Để xem ứng dụng thực tế của Open Class, chúng ta sẽ đi qua một ví dụ cụ thể từ gem money(một tập các tiện ích để quản lý tiền và tiền tệ).


## The Money Example

Chúng ta cùng xem cách tạo ra một đối tượng Money:
```
cents = 999
# 99.99 US Dollars:
bargain_price = Money.new(cents)
```
Bạn cũng có thể tạo chuyển một số bất kì thành một object Money bằng cách gọi Numeric#to_money();
```
# 100.00 US Dollars:
standard_price = 100.to_money()
```

Ở đây, lớp Numeric là một lớp cơ sở của Ruby, và bạn có thể tự hỏi phương thức `Numberic#to_money()` lầy từ đâu ra?
Nhìn vào source của Money gem, chúng ta sẽ thấy
```
class Numeric
  def to_money
    Money.new(self * 100)
  end
end
```

Open Class là một cách khá phổ biến trong các thư viện, gems.



## The problem with Open Classes
Xem xét method thay thế những phần tử trong một array sau
```
def replace(array, from, to)
  array.each_with_index do |e, i|
    array[i] = to if e == from
  end
end
```

Thay vì việc nhìn vào code thực thi ở trên, thì chúng ta xem phần unit tests để thấy cách mà phương thức này sử
```
class ReplacementTest < Test::Unit::TestCase
  def test_replace
    book_topics = ['html', 'java', 'css']
    replace(book_topics, 'java', 'ruby')

    expected = ['html', 'ruby', 'css']
    assert_equal expected, book_topics
  end
end
```

Áp dụng Open Class thì chúng ta sẽ di chuyển method replace vào class Array.
```
class Array
  def replace(from, to)
    each_with_index do |e, i|
      self[i] = to if e == from
    end
  end
end
```

Khi đó bạn sẽ phải thay đổi cách gọi đến method replace() qua Array#replace()
```
class ArrayExtensionTest < Test::Unit::TestCase
  def test_replace
    book_topics = ['html', 'java', 'css']
    book_topics.replace('java', 'ruby')
    expected = ['html', 'ruby', 'css']
    assert_equal expected, book_topics
  end
end
```

Không có test case nào bị fail cả, code bạn thay đổi không có vấn đề gì cả. Tại sao vậy?

## Monkey See, Monkey Patch

