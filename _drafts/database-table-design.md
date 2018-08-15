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

| first_name | last_name | common           | location         | state | weight     | date     |
| ---------- | --------- | ---------------- | ---------------- | ----- | ---------- | -------- |
| George     | Perry     | bass, largemouth | Montgomery Lake  | GA    | 22 lb 4 oz | 6/2/1932 |
| Mabry      | Harper    | walleye          | Old Hickory Lake | TN    | 25 lb 0 oz | 8/2/1960 |

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

Mình đã gặp một table như sau:

| tag_name | color1 | color2 | color3 |
| -------- | ------ | ------ | ------ |
| p        | #ccc   | #fff   | #eee   |
| div      | #ccc   | #aaa   | #eee   |

Thì `color1`, `color2` và `color3` ở đây là thuộc tính của `tag` nên thiết kế table như vậy không hề sai, vấn đề ở cách đặt tên, đúng hơn có thể là `background_color`, `foreground_color` và `color`.

## 2. Normal forms

Thiết kế database sao cho normal nghĩa là phải tuân theo những nguyên tắc chuẩn, và ai cũng có thể hiểu được.

### i. First normal form (1NF)
1. Các cột chỉ chứa các giá trị atomic
2. Dữ liệu cùng kiểu không được lặp lại ở nhiều cột

Hai nguyên tắc này giống với hai nguyên tắc của bảng chứa dữ liệu atomic, nên sẽ không đi sâu vào nữa.

Chúng ta có thể thiết kế lại bảng `classes` như sau, với điều kiện mỗi giáo viên chỉ dạy một lớp và mỗi học sinh chỉ được tham gia một lớp.

`teachers`

| id  | name        |
| --- | ----------- |
| 1   | Ms. Martini |
| 2   | Mr. Howard  |

`students`

| id  | name    | teacher_id |
| --- | ------- | ---------- |
| 1   | Joe     | 1          |
| 2   | Ron     | 1          |
| 3   | Kelly   | 1          |
| 4   | Sanjaya | 2          |
| 5   | Tim     | 2          |
| 6   | Julie   | 2          |

#### Functional dependency

##### Partial functional dependency

##### Transitive functional dependency


### ii. Second normal form (2NF)


### iii. Third normal form (3NF)
