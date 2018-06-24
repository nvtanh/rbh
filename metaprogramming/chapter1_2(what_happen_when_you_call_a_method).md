## 1.5 What happen when you call a method
Khi bạn goị một method, Ruby sẽ làm 2 điều:
1. Nó tìm method đó. Đây là một quá trình gọi là `method lookup`.
2. Nó sẽ thực thi method đó. Để làm điều này, Ruby cần một cái gì đó gọi là `self`.

Quá trình này, tìm và thực thi một method, nó xảy ra ở mọi ngôn ngữ hướng đối tượng.
Trong một ngôn ngữ động như Ruby, điều quan trọng là bạn cần hiểu nó sâu. 
Bạn đã bao giờ từn hỏi `method` nó được định nghĩa ở đâu chưa? Nếu có thì bạn nên tìm hiểu thêm về  `method lookup` và `self`.

### Method lookup
Khi bạn gọi một methods, RUby nhìn vào class của object đấy và tìm method đó.
Trước khi đi vào một ví dụ phức tạp, thì bạn cần biết về 2 khái niệm: `the receiver` và `the ancestors chain.`
`The receiver` đơn giản là object mà bạn gọi method. Ví dụ: nếu bạn viết là `my_string.reverse()` thì `my_string` là `the receiver`.
Để hiểu về khái niệm `an ancestors chain`, bạn hãy nhìn vào một class Ruby bất kì. 
Hãy hình dung khi di chuyển từ class hiện tại đến superclass của nó, và tiếp tục đến superclass của superclass đó,
và cứ như thế đến `Object` class và cuối cùng là `BasicObject` class. Cái đường dẫn của những class mà bạn đã đi quá đó gọi là `THe ancestors chain` của class.

Ok, sau khi bạn đã hiểu về  `the receiver` và `ancestors chain` thì bạn chỉ cần tóm tắt lại quá trình `method lookup` trong một câu:
`để tìm một method, RUby sẽ đi đến receiver class để tìm xem có method đó hay không, nếu không có nó sẽ tiếp tục tìm method đó ở các class trong ancestor chain cho đến khi tìm được method đó`
Ví dụ:
```ruby
class MyClass
  def my_method
    "my_method()"
  end
end

class MySubClass < MyClass
end

obj = MySubClass.new
obj.my_method()      # => "my_method()"
```
Khi bạn gọi `my_method()`, Ruby sẽ đi sang bên phải từ `obj` - the receiver, đến MySubClass. 
Nó không tìm thấy method `my_method()` ở đây, Ruby tiếp tục tìm nó bằng cách đi tiếp qua superclass `MyClass`
Ở đây là điểm cuối vì nó tìm thấy method `my_method()`. Nhưng nếu không tìm thấy thì nó sẽ tiếp tục tìm đến `Object`,
rồi cuối cùng là `BasicObject` (Như hình dưới).

![Method Lookup](https://github.com/nvtanh/rbh/blob/master/metaprogramming/method_lookup.png)

Và rule là:
> go one step to the right into the receiver’s class, and then go up the ancestors chain until you find the method

Hoặc bạn có thể xem `ancestors chain` bằng cách gọi `ancestors()` method:
```ruby
MySubclass.ancestors # => [MySubclass, MyClass, Object, Kernel, BasicObject]
```
Hey, tại sao lại có `Kernel` xuất hiện ở đây? không phải `Kernel` là một module, ko phải class sao???
