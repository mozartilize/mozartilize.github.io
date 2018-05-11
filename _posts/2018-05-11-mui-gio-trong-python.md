---
layout: post
title: "Múi giờ trong Python"
---

## Múi giờ (timezone) là gì?

Múi giờ của bạn là bao nhiêu? Nếu câu trả lời là "UTC+X" thì có thể là nó đúng trong thời điểm hiện tại, chứ không hắn là lúc nào cũng vậy. Ví dụ dữ liệu múi giờ của "Asia/Ho_Chi_Minh":

```
# Zone	NAME		GMTOFF	RULES	FORMAT	[UNTIL]
Zone Asia/Ho_Chi_Minh	7:06:40 -	LMT	1906 Jul  1
			7:06:30	-	PLMT	1911 May  1 # Phù Liễn MT
			7:00	-	+07	1942 Dec 31 23:00
			8:00	-	+08	1945 Mar 14 23:00
			9:00	-	+09	1945 Sep  2
			7:00	-	+07	1947 Apr  1
			8:00	-	+08	1955 Jul  1
			7:00	-	+07	1959 Dec 31 23:00
			8:00	-	+08	1975 Jun 13
			7:00	-	+07
```

Bạn có thể thấy giá trị của X (GMTOFF) thay đổi qua từng thời kì, và hiện tại là +7.

Ngoài ra trên thế giới ở một số quốc gia còn áp dụng quy ước giờ mùa hè (Daylight Saving Time - DST), nó là quy ước chỉnh đồng hồ tăng thêm một khoảng thời gian (thường là 1 giờ) so với giờ tiêu chuẩn trong một giai đoạn (thường là vào mùa hè) trong năm. Nó có ý nghĩa thực tiễn là giúp tiết kiệm năng lượng chiếu sáng và sưởi ấm, khi tận dụng ánh sáng ban ngày của ngày làm việc từ sớm, giảm chiếu sáng ban đêm nhờ ngủ sớm.

Cũng chính vì thế mà DST gây ra những vấn đề lớn trong tính toán.

## Vậy ta lấy gì làm mốc?

Múi giờ lấy làm mốc bây giờ là UTC. UTC là múi giờ không có DST hay những thay đổi trong quá khứ. Từ UTC bạn có thể chuyển đổi giờ sang giờ địa phương, nhưng ngược lại chưa chắc đã đúng vì lý do đã nêu ở trên.

> Luôn luôn tính toán và lưu dữ liệu ở múi giờ UTC. Nếu bạn cần lưu múi giờ tại thời điểm diễn ra, lưu nó riêng biệt. Không bao giờ lưu dữ liệu là "giờ địa phương + múi giờ".

## Còn vấn đề nào khác?

Tuy nhiên trong thư viện chuẩn của Python lại có sai sót trong thiết kế:

1. `datetime` module không bao gồm dữ liệu về timezone vì timezone hay thay đổi
2. Tuy nhiên `datetime` module phải cung cấp api để lưu thông tin về timezone vào `datetime` object
3. Nó nên cung cấp những object sau: `date`, `time`, `datetime` và `timedelta`

Tuy nhiên một số thứ không đúng. Vấn đề lớn nhất là chúng ta không thể tính toán với datetime object có timezone và datetime object không có chính timezone đó:

```python
>>> dt1 = datetime.utcnow()
>>> dt2 = dt1.replace(tzinfo=pytz.utc)
>>> dt1 > dt2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't compare offset-naive and offset-aware datetimes
```

Một vấn đề khác là có 2 cách để tạo datetime object vào thời điểm hiện tại trong Python:

```python
dt1 = datetime.utcnow()

dt2 = datetime.now()
```

Một là giờ ở UTC, một là giờ địa phương. Tuy nhiên nó lại không cho bạn biết múi giờ địa phương là gì cũng như với 2 giá trị `dt1` và `dt2` bạn không thể biết được cái nào có múi giờ UTC.

Thư viện còn cung cấp `date` object và `time` object. Và cả hai đều không có ý nghĩa gì nữa nếu dính líu đến timezone. Vì nếu chỉ `time` không thì không đủ hoặc nếu là `date` thì nó chỉ mang tính chất địa phương, nó có thể là hôm nay đối với tôi nhưng có thể là hôm qua, hoặc ngày mai đối với bạn.

## Best practices

### Convert timezone

Nếu bạn muốn tạo `datetime` object và lưu thông tin timezone, bạn không nên truyền `tzinfo` trong constructure mà nên dùng hàm `localize` cung cấp bởi `pytz`:

```python
>>> tz_sing = pytz.timezone('Asia/Singapore')
>>> dt1 = datetime(2018, 1, 1, 0, tzinfo=tz_sing)  # wrong
>>> dt1
datetime.datetime(2018, 1, 1, 0, 0, tzinfo=<DstTzInfo 'Asia/Singapore' LMT+6:55:00 STD>)
>>> dt1.astimezone(pytz.utc)
datetime.datetime(2017, 12, 31, 17, 5, tzinfo=<UTC>)
>>> dt2 = tz_sing.localize(datetime(2018, 1, 1, 0))  # right
>>> dt2
datetime.datetime(2018, 1, 1, 0, 0, tzinfo=<DstTzInfo 'Asia/Singapore' +08+8:00:00 STD>)
>>> dt2.astimezone(pytz.utc)
datetime.datetime(2017, 12, 31, 16, 0, tzinfo=<UTC>)
```

Bạn có thể thấy với `dt2` nó sử dụng đúng offset ở thời điểm hiện lại là +8.

### Sử dụng UTC trong tính toán nội bộ

Nếu bạn muốn lấy thời gian hiện tại, luôn luôn dùng `datetime.utcnow()`. Khi người dùng nhập giờ địa phương, lập tức chuyển qua UTC rồi tiếp tục xử lý. Nếu bạn không biết múi giờ của người dùng, hãy hỏi họ, không thể tự ý đoán dựa trên IP hay số điện thoại (đây là ý tưởng thực khi mình làm dự án hiện tại).

### Không nên dùng offset-aware datetime

`datetime` object là offset-aware khi nó lưu thông tin về timezone, nếu không thì gọi là offset-navie.

Nó có thể là ý tưởng tốt nếu bạn lưu tzinfo trong `datetime` object. Nhưng sẽ tốt hơn nếu bạn không làm như vậy. Hãy tận dụng hạn chế trong thiết kế API:

- Trong tính toán nội bộ sử dụng offset-navie `datetime` object và quy ước timezone của nó là UTC
- Khi có giao tiếp với người dùng, chuyển đổi nó qua giờ địa phương

Tại sao bạn nên làm như vậy? Đầu tiên là nhiều thư viện được viết với ngầm định `tzinfo=None`. Thứ hai, sử dụng tzinfo trong khi thư viện chuẩn được thiết kế sai là một ý tưởng tệ hại. Đó cũng là lý do vì sao `pytz` cung cấp hàm `localize` để chuyển đổi timezone vì API của thư viện chuẩn không đủ linh hoạt làm việc đó với hơn 500 timezone. Không sử dụng `tzinfo` là cơ hội để bạn trở nên tốt hơn.

Lý do cuối cùng mà bạn không nên dùng offset-aware là bởi vì `tzinfo` object của các thư viện của các ngôn ngữ là khác nhau (implementation defined). Hiện nay chưa có chuẩn nào để truyền tzinfo từ ngôn ngữ này sang ngôn ngữ khác hay HTTP ngoài sử dụng UTC offset như hiện tại.
