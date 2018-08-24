---
layout: post
title: "Database table design"
---

## 1. Smart table design

Chúng ta có 2 table như sau:


`fish_info`

| common           | species      | location             | weight     |
| ---------------- | ------------ | -------------------- | ---------- |
| bass, largemouth | M. salmoides | Montgomery Lake, GA  | 22 lb 4 oz |
| walleye          | S. vitreus   | Old Hickory Lake, TN | 25 lb 0 oz |

`fish_records`

| first_name | last_name | common           | location         | state | weight     | date       |
| ---------- | --------- | ---------------- | ---------------- | ----- | ---------- | ---------- |
| George     | Perry     | bass, largemouth | Montgomery Lake  | GA    | 22 lb 4 oz | 06/02/1932 |
| Mabry      | Harper    | walleye          | Old Hickory Lake | TN    | 25 lb 0 oz | 08/02/1960 |

Hai table trên có một số cột giống nhau, một số cột mà table kia không có, và có cột được tách ra như với `location` và `state`.

Theo bạn nghĩ thì table nào được thiết kế tốt hơn?

Câu trả lời là không có table nào cả. Cách bạn thiết kế table hoàn toàn dựa vào mục đích sử dụng của bạn. Giả sử table `fish_info` được dùng trong công việc của một nhà thủy sinh học, còn table `fish_records` dành cho một nhà buôn thủy sản. Vì nhà buôn thủy sản cần biết thông tin của người bắt được loài cá đó còn nhà thủy sinh học thì không. Vì nhà buôn thủy sản nhiều lúc cần search thông tin của `state` nơi bắt được loài cá đó, nên cần phải tách cột đó ra để query được nhanh hơn...

`first_name`, `last_name` ở table `fish_records` được tách ra vậy cột `date` có cần tách ra các cột `day`, `month`, `year` không? Điều đó còn tùy, nếu nhà buôn cảm thấy thông tin được chia nhỏ đến mức như vậy là đủ rồi thì không cần thiết nữa. Dữ liệu lúc đó là đã **atomic** - nghĩa là nó không thể hoặc không nên chia nhỏ nữa.

Dữ liệu atomic có 2 nguyên tắc cơ bản:

1. Một cột chứa dữ liệu atomic không thể có các giá trị cùng kiểu

| food_name | ingredients                  |
| --------- | ---------------------------- |
| bread     | flour, milk, egg, yeast, oil |
| salad     | lettuce, tomato, cucumber    |

2. Một bảng có dữ liệu atomic không thể có nhiều cột với cùng kiểu dữ liệu

`classes`

| teacher     | student1 | student2 | student3 |
| ----------- | -------- | -------- | -------- |
| Ms. Martini | Joe      | Ron      | Kelly    |
| Mr. Howard  | Sanjaya  | Tim      | Julie    |

Ở đây nên có một lưu ý các cột học sinh lặp lại không phải thuộc tính của giáo viên, mà là các đối tượng khác nhau.

Một ví dụ khác, chúng ta có bảng `toys`:

| toy_id | toy        | color1 | color2 | color3 |
| ------ | ---------- | ------ | ------ | ------ |
| 5      | wiffleball | white  | yellow | blue   |
| 6      | frisbee    | green  | yellow |        |
| 9      | kite       | red    | blue   | green  |
| 12     | yoyo       | white  | yellow |        |

Các color ở đây là loại màu của đồ chơi, wiffleball có 3 màu khác nhau, tương tự yoyo chỉ có 2 loại màu, chứ không có nghĩa là 1 quả wiffleball có cả 3 màu trên nó.

Mình đã gặp một table như sau:

| tag_name | color1 | color2 | color3 |
| -------- | ------ | ------ | ------ |
| p        | #ccc   | #fff   | #eee   |
| div      | #ccc   | #aaa   | #eee   |

Thì `color1`, `color2` và `color3` ở đây là thuộc tính của `tag` nên thiết kế table như vậy không hề sai, vấn đề ở cách đặt tên, đúng hơn có thể là `background_color`, `foreground_color` và `border_color`.

## 2. Normal forms

Thiết kế database sao cho normal nghĩa là phải tuân theo những nguyên tắc chuẩn, và ai cũng có thể hiểu được.

### i. First normal form (1NF)
1. Các cột chỉ chứa các giá trị atomic
2. Dữ liệu cùng kiểu không được lặp lại ở nhiều cột

1NF thực thi các tiêu chí sau:
- Không gặp lại các nhóm dữ liệu trong bảng.
- Tạo các bảng riêng lẻ cho các dữ liệu liên quan.
- Mỗi bảng ghi của dữ liệu phải có một khóa chính.

Chúng ta có thể thiết kế lại bảng `classes` như sau, với điều kiện mỗi giáo viên chỉ dạy một lớp và mỗi học sinh chỉ được tham gia một lớp.

`teachers`

| id  | name        |
| --- | ----------- |
| 1   | Ms. Martini |
| 2   | Mr. Howard  |

`students`

| id  | name    | year_of_birth | age |
| --- | ------- | ------------- | --- |
| 1   | Joe     | 1994          | 24  |
| 2   | Ron     | 1994          | 24  |
| 3   | Kelly   | 1994          | 24  |
| 4   | Sanjaya | 1994          | 24  |
| 5   | Tim     | 1994          | 24  |
| 6   | Julie   | 1994          | 24  |

`classes`

| id  | subject | teacher_id |
| --- | ------- | ---------- |
| 1   | Physics | 2          |
| 2   | Physics | 1          |


`class_list`

| id  | student_id | class_id |
| --- | ---------- | -------- |
| 1   | 1          | 1        |
| 2   | 2          | 1        |
| 3   | 3          | 1        |
| 4   | 4          | 2        |
| 5   | 5          | 2        |
| 6   | 6          | 2        |

#### Functional dependency

Ở bảng `students` trên, chúng ta có thể nhận ra nếu `year_of_birth` của học sinh đó thay đổi thì cột `age` cũng sẽ phải thay đổi theo. Vì vậy ta gọi `age` phụ thuộc hàm vào `year_of_birth`.

`students.year_of_birth -> students.age`

##### Partial functional dependency

Để hiểu được phụ thuộc hàm một phần, chúng ta phải hiểu được `composite key` (khóa tổng hợp).

Ở bảng `classes`, nếu ta không cần cột `id` làm khóa chính, thì sự kết hợp duy nhất (unique) của 2 cột còn lại là `subject` và `teacher_id` tạo thành khóa tổng hợp. Nghĩa là cứ mỗi giáo viên và một môn học, chúng ta sẽ có một lớp.

`classes`

| subject | teacher_id | teacher_address          |
| ------- | ---------- | ------------------------ |
| Physics | 2          | 3961  Roosevelt Road, WA |
| Physics | 1          | 1223  Hillcrest Lane, CA |

Với bảng trên, ta thấy `teacher_address` (non-key column) phụ thuộc vào `teacher_id` - một trong cột tạo nên khóa tổng hợp (key column) vì nếu `teacher_id` thay đổi buộc address phải thay đổi theo, do vậy ta nói `teacher_address` phụ thuộc hàm một phần vào `teacher_id`.

> Một bảng có phụ thuộc hàm một phần khi có cột không phải thuộc khóa tổng hợp (non-key column) phụ thuộc vào một hoặc nhiều nhưng không phải tất cả các cột tạo nên khóa tổng hợp.

Hãy xem thêm một ví dụ nữa.

`superheroes`

| name           | power                    | weakness | city       | initials |
| -------------- | ------------------------ | -------- | ---------- | -------- |
| Super Trashman | Cleans quickly           | bleach   | Gotham     | ST       |
| The Broker     | Makes money from nothing | NULL     | New York   | TB       |
| Super Guy      | Flies                    | birds    | Metropolis | SG       |
| Wonder Waiter  | Never forgets an order   | insects  | Paris      | WW       |

Ở bảng `superheroes`, 2 cột `name` và `power` tạo thành khóa tổng hợp, mỗi siêu anh hùng đều có tên và siêu năng lực của riêng mình. Tuy nhiên cột `initials` có thể nói là viết tắt của tên của siêu anh hùng đó. Và nếu cột `name` (key column) thay đổi, `initials` cũng phải thay đổi theo, ta gọi đó là phụ thuộc hàm một phần.

##### Transitive functional dependency

> Một bảng có phụ thuộc hàm bắc cầu nếu một cột không phải thuộc khóa tổng hợp thay đổi dẫn đến các cột cũng không phải thuộc khóa tổng hợp bất kì trong bảng thay đổi.

Phụ thuộc hàm của bảng `students` ở ví dụ trên được gọi là phụ thuộc hàm bắc cầu.

### ii. Second normal form (2NF)

1. Bảng đã ở 1NF
2. Bảng không có phụ thuộc hàm một phần

Chúng ta có phiên bản mở rộng của bảng `toys` dưới đây:

`toys`

| toy_id | toy        |
| ------ | ---------- |
| 5      | wiffleball |
| 6      | frisbee    |
| 9      | kite       |
| 12     | yoyo       |

`inventories`

| toy_id | store_id | color  | inventory | store_address    |
| ------ | -------- | ------ | --------- | ---------------- |
| 5      | 1        | white  | 34        | 23 Maple         |
| 5      | 3        | yellow | 12        | 100 E. North St. |
| 5      | 1        | blue   | 5         | 23 Maple         |
| 6      | 2        | green  | 10        | 1902 Amber Ln.   |
| 6      | 4        | yellow | 24        | 17 Engleside     |
| 9      | 1        | red    | 50        | 23 Maple         |
| 9      | 2        | blue   | 2         | 1902 Amber Ln    |
| 9      | 2        | green  | 18        | 1902 Amber Ln    |
| 12     | 4        | white  | 28        | 17 Engleside     |
| 12     | 4        | yellow | 11        | 17 Engleside     |

`store_address` phụ thuộc vào `store_id` nên chúng ta sẽ đưa nó sang bảng `stores` riêng biệt.

`stores`

| id  | address          | phone    | manager |
| --- | ---------------- | -------- | ------- |
| 1   | 23 Maple         | 555-6712 | Joe     |
| 2   | 1902 Amber Ln.   | 555-3478 | Susan   |
| 3   | 100 E. North St. | 555-0987 | Tara    |
| 4   | 17 Engleside     | 555-6554 | Gordon  |

`inventories`

| toy_id | store_id | color  | inventory |
| ------ | -------- | ------ | --------- |
| 5      | 1        | white  | 34        |
| 5      | 2        | red    | 34        |
| 5      | 3        | yellow | 12        |
| 5      | 1        | blue   | 5         |
| 6      | 2        | green  | 10        |
| 6      | 4        | yellow | 24        |
| 9      | 1        | red    | 50        |
| 9      | 2        | blue   | 2         |
| 9      | 2        | green  | 18        |
| 12     | 4        | white  | 28        |
| 12     | 4        | yellow | 11        |

`toy_id`, `store_id`, `color` tạo thành khóa tổng hợp.

Nếu chúng ta thêm vào 1 loại đồ chơi ở 1 cửa hàng, chúng ta phải cập nhật cùng lúc 2 bảng `toys` và `inventories`.

Nếu các loại đồ chơi được định nghĩa sẵn, hay còn gọi là `toy_types`. Như vậy, nếu ta thêm 1 loại đồ chơi mới vào bảng `toys`, thì `color` tương tự cũng phải được định nghĩa sẵn chứ không thể đợi đến lúc cập nhật vào bảng `inventories` chúng ta mới có thể query để biết được loại đồ chơi này có những màu nào.

Và rõ ràng `color` là một thuộc tính thuộc về `toys` thay vì của `inventories`. Điều này dẫn đến vi phạm 1NF.

`toys`

| id  | inventory   | color  | cost | weight |
| --- | ----------- | ------ | ---- | ------ |
| 1   | whiffleball | white  | 1.95 | 0.3    |
| 2   | whiffleball | yellow | 2.20 | 0.4    |
| 3   | whiffleball | blue   | 1.95 | 0.3    |
| 4   | frisbee     | green  | 3.50 | 0.5    |
| 5   | frisbee     | yellow | 1.50 | 0.2    |
| 6   | kite        | red    | 5.75 | 1.2    |
| 7   | kite        | blue   | 5.75 | 1.2    |
| 8   | kite        | green  | 3.15 | 0.8    |
| 9   | yoyo        | white  | 4.25 | 0.4    |
| 10  | yoyo        | yellow | 1.50 | 0.2    |

`inventories`

| toy_id | store_id | inventory |
| ------ | -------- | --------- |
| 5      | 1        | 34        |
| 5      | 2        | 34        |
| 5      | 3        | 12        |
| 5      | 1        | 5         |
| 6      | 2        | 10        |
| 6      | 4        | 24        |
| 9      | 1        | 50        |
| 9      | 2        | 2         |
| 9      | 2        | 18        |
| 12     | 4        | 28        |
| 12     | 4        | 11        |


### iii. Third normal form (3NF)

1. Bảng đã ở 2NF
2. Bảng không có phụ thuộc hàm bắc cầu

Bảng dưới đây thỏa mãn 2NF nhưng vi phạm 3NF:

`tournament_winners`

| tournament           | year | winner         | date_of_birth     |
| -------------------- | ---- | -------------- | ----------------- |
| Indiana Invitational | 1998 | Al Fredrickson | 21 July 1975      |
| Cleveland Open       | 1999 | Bob Albertson  | 28 September 1968 |
| Des Moines Masters   | 1999 | Al Fredrickson | 21 July 1975      |
| Indiana Invitational | 1999 | Chip Masterson | 14 March 1977     |

Khóa tổng hợp ở đây từ `tournamemt` và `year`. Tuy nhiên bảng này vi phạm 3NF vì `date_of_birth` (non-key column) phụ thuộc vào `winner` (non-key column).

`tournament_winners`

| tournament           | year | winner         |
| -------------------- | ---- | -------------- |
| Indiana Invitational | 1998 | Al Fredrickson |
| Cleveland Open       | 1999 | Bob Albertson  |
| Des Moines Masters   | 1999 | Al Fredrickson |
| Indiana Invitational | 1999 | Chip Masterson |

`winner_dates_of_birth`

| winner         | date_of_birth     |
| -------------- | ----------------- |
| Chip Masterson | 14 March 1977     |
| Al Fredrickson | 21 July 1975      |
| Bob Albertson  | 28 September 1968 |


## Tham khảo

1. Head first SQL by Lynn Beighley
2. https://en.wikipedia.org/wiki/First_normal_form
