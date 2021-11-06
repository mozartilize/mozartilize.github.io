---
layout: post
title: "A brief look at Python's PEP 612"
---

## PEP 612

PEP 612 định nghĩa một số type mới giúp typing wrapper function trong decorator rõ ràng hơn.

<!-- more -->

Chúng ta hãy xem qua ví dụ sau:

```python
from typing import Callable, TypeVar

R = TypeVar("R")

def add_logging(f: Callable[..., R]) -> Callable[..., R]:
  def wrapper(*args, **kwargs) -> R:
    log_to_database()
    return f(*args, **kwargs)
  return wrapper

@add_logging
def takes_int_str(x: int, y: str) -> int:
  return x + 7

takes_int_str(1, "A")
takes_int_str("B", 2)  # fails at runtime
```

Một trong những cách sử dụng phổ biến của decorator là truyền hết tham số của wrapper function vào main function (ở đây là `takes_int_str`) thông qua `*args` và `**kwargs`. Lưu ý là khi chúng ta decorate hàm `take_int_str` thì bây giờ nó đã được return về wrapper function rồi, và type của `*args` và `**kwargs` không được định nghĩa nên type checker không thể phát hiện lỗi nếu truyền sai kiểu dữ liệu vào `take_int_str` như với lần gọi thứ hai ở trên. Như vậy chúng ta cần sự liên kết giữa các param của main function và wrapper function.

Với PEP 612, `ParamSpec` được định nghĩa để hỗ trợ trường hợp này.

```python
from typing import Callable, ParamSpec, TypeVar

P = ParamSpec("P")
R = TypeVar("R")

def add_logging(f: Callable[P, R]) -> Callable[P, R]:
  def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
    log_to_database()
    return f(*args, **kwargs)
  return wrapper

@add_logging
def takes_int_str(x: int, y: str) -> int:
  return x + 7

takes_int_str(1, "A")  # Accepted
takes_int_str("B", 2)  # Correctly rejected by the type checker
```

Bây giờ cả type của các param (`P`) và return value (`R`) của main function đều được liên kết với wrapper function.

Không chỉ truyền nguyên param từ wrapper vào main function, đôi khi chúng ta còn thêm default param cho main function. Hãy cùng xem qua ví dụ sau.

```python
# my_func.py
from typing import Callable, Concatenate, ParamSpec, TypeVar
# from typing_extensions import Concatenate, ParamSpec

R = TypeVar("R")
P = ParamSpec("P")


def hello_gretter(f: Callable[Concatenate[str, P], R]) -> Callable[P, R]:
	def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
		rv = f("Hello", *args, **kwargs)
		return rv
	return wrapper


@hello_gretter
def hello1(gretter: str, name: str):
	return f"{gretter}, {name}!"


@hello_gretter
def hello2(gretter: str, first_name: str, last_name: str):
	return f"{gretter}, {first_name} {last_name}!"

if __name__ == "__main__":
	hello1("Tony Stark")
	hello2("Tony", "Stark")
	hello2(1, "John")
```

`gretter` được set mặc định là `Hello`, mặc dù được định nghĩa trong hàm nhưng khi gọi chúng ta không cần truyền vào nữa.

Pattern này được sử dụng khá phổ biến ở các web framework như django. `request` param của view function được truyền vào mặc định.

```python
def detail(request: Request, pk: str) -> Response: ...
```

## mypy vs pyre vs pyright

mypy vẫn [chưa support PEP 612](https://github.com/python/mypy/issues/8645).

```
$ mypy --version
mypy 0.910

$ mypy my_func.py 
my_func.py:2: error: Module "typing" has no attribute "Concatenate"
my_func.py:2: error: Module "typing" has no attribute "ParamSpec"
my_func.py:9: error: The first argument to Callable must be a list of types or "..."
my_func.py:10: error: Name "P.args" is not defined
my_func.py:10: error: Name "P.kwargs" is not defined
Found 5 errors in 1 file (checked 1 source file)
```

Mình có setup thử pyre với python 3.7, phiên bản này built-in typing module chưa có `ParamSpec` và `Concatenate` nên cần sử dụng thêm `typing_extensions`. Nhưng không hiểu có vấn đề gì mà pyre không thể tìm thấy `typing_extensions` trong import code của mình.

```json
// .pyre_configuration
{
  "source_directories": [
    "."
  ],
  "taint_models_path": "~/Documents/workspace/test-pyre/.venv/lib",
  "search_path": ["~/.pyenv/versions/3.7.12/lib/python3.7"],
  "exclude": [".*\/.venv2?\/.*"]
}
```
```
$ pyre
ƛ Found 9 type errors!
my_func.py:3:0 Undefined import [21]: Could not find a module corresponding to import `typing_extensions`.
my_func.py:9:21 Invalid type [31]: Expression `typing.Callable[(typing_extensions.Concatenate[(str, $local_my_func$P)], $local_my_func$R)]` is not a valid type.
my_func.py:9:21 Invalid type [31]: Expression `typing.Callable[(typing_extensions.Concatenate[(str, $local_my_func$P)], $local_my_func$R)]` is not a valid type.
my_func.py:9:58 Invalid type variable [34]: The type variable `Variable[R]` isn't present in the function's parameters.
my_func.py:9:58 Invalid type variable [34]: The type variable `P` isn't present in the function's parameters.
my_func.py:10:51 Invalid type variable [34]: The type variable `Variable[R]` isn't present in the function's parameters.
my_func.py:13:1 Incompatible return type [7]: Expected `typing.Callable[my_func.P, Variable[R]]` but got `typing.Callable[my_func.P, Variable[R]]`.
my_func.py:17:20 Undefined or invalid type [11]: Annotation `str` is not defined as a type.
my_func.py:25:3 Undefined attribute [16]: `str` has no attribute `__eq__`.
```

Thậm chí nếu mình dùng python 3.10 có support `ParamSpec` và `Concatenate` sẵn trong module `typing`.

```
$ pyre
ƛ Found 8 type errors!
my_func.py:9:21 Invalid type [31]: Expression `typing.Callable[(typing.Concatenate[(str, $local_my_func$P)], $local_my_func$R)]` is not a valid type.
my_func.py:9:21 Invalid type [31]: Expression `typing.Callable[(typing.Concatenate[(str, $local_my_func$P)], $local_my_func$R)]` is not a valid type.
my_func.py:9:58 Invalid type variable [34]: The type variable `Variable[R]` isn't present in the function's parameters.
my_func.py:9:58 Invalid type variable [34]: The type variable `P` isn't present in the function's parameters.
my_func.py:10:51 Invalid type variable [34]: The type variable `Variable[R]` isn't present in the function's parameters.
my_func.py:13:1 Incompatible return type [7]: Expected `typing.Callable[my_func.P, Variable[R]]` but got `typing.Callable[my_func.P, Variable[R]]`.
my_func.py:17:20 Undefined or invalid type [11]: Annotation `str` is not defined as a type.
my_func.py:25:3 Undefined attribute [16]: `str` has no attribute `__eq__`.
```

pyright có vẻ support tốt.
```
$ pyright
No configuration file found.
No pyproject.toml file found.
stubPath ~/Documents/workspace/test-pyre/typings is not a valid directory.
Assuming Python platform Linux
Searching for source files
Auto-excluding ~/Documents/workspace/test-pyre/.venv
Auto-excluding ~/Documents/workspace/test-pyre/.venv2
Found 1 source file
~/Documents/workspace/test-pyre/my_func.py
  ~/Documents/workspace/test-pyre/my_func.py:28:12 - error: Argument of type "Literal[1]" cannot be assigned to parameter "first_name" of type "str"
    "Literal[1]" is incompatible with "str" (reportGeneralTypeIssues)
1 error, 0 warnings, 0 infos 
Completed in 0.433sec
```
