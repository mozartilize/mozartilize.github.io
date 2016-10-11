---
layout: post
title:  "Ruby method arguments"
---
### 1. Parameters vs Arguments
Parameter là một phần trong định nghĩa phương thức.
Còn argument là các giá trị được truyền khi gọi hàm thông qua parameter.

```ruby
def foo(bar)
  # Do something with bar
end

foo(123456)

foo("pretty girls")
```
Ở ví dụ trên, `bar` là parameter của method `foo`. Còn `123456` và `pretty girls` là 2 argument được truyền vào phương thức `foo` khi nó được gọi.

### 2. Giá trị mặc định của argument
Ví dụ ta có một phương thức:

```ruby
def foo(a, b=10):
  puts a, b
end

foo(20)
# result:
# 20
# 10

foo(20, 15)
# result:
# 20
# 15
```
Với phương thức trên, `b` sẽ nhận giá trị 10 nếu ta không truyền giá trị nào khi gọi hàm.

Bây giờ giả sử ta có một phương thức với 2 argument có giá trị mặc định.

```ruby
def bar(a, b=10, c="pineapple")
  puts a, b, c
end
```
Nếu bạn muốn giữ nguyên giá trị mặc định của `b` và thay đổi `c` thành `pen` khi gọi `bar`?

Với Python, ta có thể gọi tên của argument và gán giá trị cho nó khi gọi phương thức:

```python
def bar(a, b=10, c="pineapple"):
  print(a, b, c)

bar(20, c="pen")  # 20 10 pen
```
Tuy nhiên, với Ruby cách này không thể áp dụng được. Đơn giản là vì Ruby không hỗ trợ named argument :((

```ruby
def bar(a, b=10, c="pineapple")
  puts a, b, c
end

bar(20, c="pen")
# result:
# 20
# pen
# pineapple
```

### 3. Keywork arguments
Nhưng kể từ phiên bản 2.0, Ruby hỗ trợ keywork argument, tương tự như named argument.

```ruby
def bar(a, b: 10, c: "pineapple")
  puts a, b, c
end

bar(20, c: "pen")
# result:
# 20
# 10
# pen

# We also can change the order of arguments
bar(20, c: "pen", b: "apple")
# result:
# 20
# apple
# pen
```

Nhưng khi sử dụng keywork argument, chúng ta buộc phải gọi tên của argument \*\*wtf\*\*

```ruby
def foo(a:, b:)
  puts a, b
end

foo(a: 10, b: 20)  # works, but...

foo(10, 20)  # not work
```
