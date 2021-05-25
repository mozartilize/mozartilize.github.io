---
layout: post
title: "Database table design"
---

## 1. Smart table design

Chúng ta có 2 table như sau:

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="4" style="text-align:center">fish_info</th>
    </tr>
    <tr>
      <th>common</th>
      <th>species</th>
      <th>location</th>
      <th>weight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>bass, largemouth</td>
      <td>M. salmoides</td>
      <td>Montgomery Lake, GA</td>
      <td>22 lb 4 oz</td>
    </tr>
    <tr>
      <td>walleye</td>
      <td>S. vitreus</td>
      <td>Old Hickory Lake, TN</td>
      <td>25 lb 0 oz</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="7" style="text-align:center">fish_records</th>
    </tr>
    <tr>
      <th>first_name</th>
      <th>last_name</th>
      <th>common</th>
      <th>location</th>
      <th>state</th>
      <th>weight</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>George</td>
      <td>Perry</td>
      <td>bass, largemouth</td>
      <td>Montgomery Lake</td>
      <td>GA</td>
      <td>22 lb 4 oz</td>
      <td>06/02/1932</td>
    </tr>
    <tr>
      <td>Mabry</td>
      <td>Harper</td>
      <td>walleye</td>
      <td>Old Hickory Lake</td>
      <td>TN</td>
      <td>25 lb 0 oz</td>
      <td>08/02/1960</td>
    </tr>
  </tbody>
</table>
</div>

Hai table trên có một số cột giống nhau, một số cột mà table kia không có, và có cột được tách ra như với `location` và `state`.

Theo bạn nghĩ thì table nào được thiết kế tốt hơn?

<!-- more -->

Câu trả lời là không có table nào cả. Cách bạn thiết kế table hoàn toàn dựa vào mục đích sử dụng của bạn. Giả sử table `fish_info` được dùng trong công việc của một nhà thủy sinh học, còn table `fish_records` dành cho một nhà buôn thủy sản. Vì nhà buôn thủy sản cần biết thông tin của người bắt được loài cá đó còn nhà thủy sinh học thì không. Vì nhà buôn thủy sản nhiều lúc cần search thông tin của `state` nơi bắt được loài cá đó, nên cần phải tách cột đó ra để query được nhanh hơn...

`first_name`, `last_name` ở table `fish_records` được tách ra vậy cột `date` có cần tách ra các cột `day`, `month`, `year` không? Điều đó còn tùy, nếu nhà buôn cảm thấy thông tin được chia nhỏ đến mức như vậy là đủ rồi thì không cần thiết nữa. Dữ liệu lúc đó là đã **atomic** - nghĩa là nó không thể hoặc không nên chia nhỏ nữa.

Dữ liệu atomic có 2 nguyên tắc cơ bản:

1. Một cột chứa dữ liệu atomic không thể có các giá trị cùng kiểu

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th>food_name</th>
      <th>ingredients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>bread</td>
      <td>flour, milk, egg, yeast, oil</td>
    </tr>
    <tr>
      <td>salad</td>
      <td>lettuce, tomato, cucumber</td>
    </tr>
  </tbody>
</table>
</div>

2. Một bảng có dữ liệu atomic không thể có nhiều cột với cùng kiểu dữ liệu

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="4" style="text-align:center">classes</th>
    </tr>
    <tr>
      <th>teacher</th>
      <th>student1</th>
      <th>student2</th>
      <th>student3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ms. Martini</td>
      <td>Joe</td>
      <td>Ron</td>
      <td>Kelly</td>
    </tr>
    <tr>
      <td>Mr. Howard</td>
      <td>Sanjaya</td>
      <td>Tim</td>
      <td>Julie</td>
    </tr>
  </tbody>
</table>
</div>

Ở đây nên có một lưu ý các cột học sinh lặp lại không phải thuộc tính của giáo viên, mà là các đối tượng khác nhau.

Một ví dụ khác, chúng ta có bảng `toys`:

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th>toy_id</th>
      <th>toy</th>
      <th>color1</th>
      <th>color2</th>
      <th>color3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>5</td>
      <td>wiffleball</td>
      <td>white</td>
      <td>yellow</td>
      <td>blue</td>
    </tr>
    <tr>
      <td>6</td>
      <td>frisbee</td>
      <td>green</td>
      <td>yellow</td>
      <td>&nbsp;</td>
    </tr>
    <tr>
      <td>9</td>
      <td>kite</td>
      <td>red</td>
      <td>blue</td>
      <td>green</td>
    </tr>
    <tr>
      <td>12</td>
      <td>yoyo</td>
      <td>white</td>
      <td>yellow</td>
      <td>&nbsp;</td>
    </tr>
  </tbody>
</table>
</div>

Các color ở đây là loại màu của đồ chơi, wiffleball có 3 màu khác nhau, tương tự yoyo chỉ có 2 loại màu, chứ không có nghĩa là 1 quả wiffleball có cả 3 màu trên nó.

Mình đã gặp một table như sau:

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th>tag_name</th>
      <th>color1</th>
      <th>color2</th>
      <th>color3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>p</td>
      <td>#ccc</td>
      <td>#fff</td>
      <td>#eee</td>
    </tr>
    <tr>
      <td>div</td>
      <td>#ccc</td>
      <td>#aaa</td>
      <td>#eee</td>
    </tr>
  </tbody>
</table>
</div>

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

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="2" style="text-align:center">teachers</th>
    </tr>
    <tr>
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Ms. Martini</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Mr. Howard</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="4" style="text-align:center">students</th>
    </tr>
    <tr>
      <th>id</th>
      <th>name</th>
      <th>year_of_birth</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Joe</td>
      <td>1994</td>
      <td>24</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Ron</td>
      <td>1994</td>
      <td>24</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Kelly</td>
      <td>1994</td>
      <td>24</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Sanjaya</td>
      <td>1994</td>
      <td>24</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Tim</td>
      <td>1994</td>
      <td>24</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Julie</td>
      <td>1994</td>
      <td>24</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="3" style="text-align:center">classes</th>
    </tr>
    <tr>
      <th>id</th>
      <th>subject</th>
      <th>teacher_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Physics</td>
      <td>2</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Physics</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="3" style="text-align:center">class_list</th>
    </tr>
    <tr>
      <th>id</th>
      <th>student_id</th>
      <th>class_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>4</td>
      <td>2</td>
    </tr>
    <tr>
      <td>5</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <td>6</td>
      <td>6</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>

#### Functional dependency

Ở bảng `students` trên, chúng ta có thể nhận ra nếu `year_of_birth` của học sinh đó thay đổi thì cột `age` cũng sẽ phải thay đổi theo. Vì vậy ta gọi `age` phụ thuộc hàm vào `year_of_birth`.

`students.year_of_birth -> students.age`

##### Partial functional dependency

Để hiểu được phụ thuộc hàm một phần, chúng ta phải hiểu được `composite key` (khóa tổng hợp).

Ở bảng `classes`, nếu ta không cần cột `id` làm khóa chính, thì sự kết hợp duy nhất (unique) của 2 cột còn lại là `subject` và `teacher_id` tạo thành khóa tổng hợp. Nghĩa là cứ mỗi giáo viên và một môn học, chúng ta sẽ có một lớp.

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="3" style="text-align:center">classes</th>
    </tr>
    <tr>
      <th>subject</th>
      <th>teacher_id</th>
      <th>teacher_address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Physics</td>
      <td>2</td>
      <td>3961  Roosevelt Road, WA</td>
    </tr>
    <tr>
      <td>Physics</td>
      <td>1</td>
      <td>1223  Hillcrest Lane, CA</td>
    </tr>
  </tbody>
</table>
</div>

Với bảng trên, ta thấy `teacher_address` (non-key column) phụ thuộc vào `teacher_id` - một trong cột tạo nên khóa tổng hợp (key column) vì nếu `teacher_id` thay đổi buộc address phải thay đổi theo, do vậy ta nói `teacher_address` phụ thuộc hàm một phần vào `teacher_id`.

> Một bảng có phụ thuộc hàm một phần khi có cột không phải thuộc khóa tổng hợp (non-key column) phụ thuộc vào một hoặc nhiều nhưng không phải tất cả các cột tạo nên khóa tổng hợp.

Hãy xem thêm một ví dụ nữa.

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="5" style="text-align:center">superheroes</th>
    </tr>
    <tr>
      <th>name</th>
      <th>power</th>
      <th>weakness</th>
      <th>city</th>
      <th>initials</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Super Trashman</td>
      <td>Cleans quickly</td>
      <td>bleach</td>
      <td>Gotham</td>
      <td>ST</td>
    </tr>
    <tr>
      <td>The Broker</td>
      <td>Makes money from nothing</td>
      <td>NULL</td>
      <td>New York</td>
      <td>TB</td>
    </tr>
    <tr>
      <td>Super Guy</td>
      <td>Flies</td>
      <td>birds</td>
      <td>Metropolis</td>
      <td>SG</td>
    </tr>
    <tr>
      <td>Wonder Waiter</td>
      <td>Never forgets an order</td>
      <td>insects</td>
      <td>Paris</td>
      <td>WW</td>
    </tr>
  </tbody>
</table>
</div>

Ở bảng `superheroes`, 2 cột `name` và `power` tạo thành khóa tổng hợp, mỗi siêu anh hùng đều có tên và siêu năng lực của riêng mình. Tuy nhiên cột `initials` có thể nói là viết tắt của tên của siêu anh hùng đó. Và nếu cột `name` (key column) thay đổi, `initials` cũng phải thay đổi theo, ta gọi đó là phụ thuộc hàm một phần.

##### Transitive functional dependency

> Một bảng có phụ thuộc hàm bắc cầu nếu một cột không phải thuộc khóa tổng hợp thay đổi dẫn đến các cột cũng không phải thuộc khóa tổng hợp bất kì trong bảng thay đổi.

Phụ thuộc hàm của bảng `students` ở ví dụ trên được gọi là phụ thuộc hàm bắc cầu.

### ii. Second normal form (2NF)

1. Bảng đã ở 1NF
2. Bảng không có phụ thuộc hàm một phần

Chúng ta có phiên bản mở rộng của bảng `toys` dưới đây:

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="2" style="text-align:center">toys</th>
    </tr>
    <tr>
      <th>toy_id</th>
      <th>toy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>5</td>
      <td>wiffleball</td>
    </tr>
    <tr>
      <td>6</td>
      <td>frisbee</td>
    </tr>
    <tr>
      <td>9</td>
      <td>kite</td>
    </tr>
    <tr>
      <td>12</td>
      <td>yoyo</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="5" style="text-align:center">inventories</th>
    </tr>
    <tr>
      <th>toy_id</th>
      <th>store_id</th>
      <th>color</th>
      <th>inventory</th>
      <th>store_address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>5</td>
      <td>1</td>
      <td>white</td>
      <td>34</td>
      <td>23 Maple</td>
    </tr>
    <tr>
      <td>5</td>
      <td>3</td>
      <td>yellow</td>
      <td>12</td>
      <td>100 E. North St.</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1</td>
      <td>blue</td>
      <td>5</td>
      <td>23 Maple</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2</td>
      <td>green</td>
      <td>10</td>
      <td>1902 Amber Ln.</td>
    </tr>
    <tr>
      <td>6</td>
      <td>4</td>
      <td>yellow</td>
      <td>24</td>
      <td>17 Engleside</td>
    </tr>
    <tr>
      <td>9</td>
      <td>1</td>
      <td>red</td>
      <td>50</td>
      <td>23 Maple</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2</td>
      <td>blue</td>
      <td>2</td>
      <td>1902 Amber Ln</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2</td>
      <td>green</td>
      <td>18</td>
      <td>1902 Amber Ln</td>
    </tr>
    <tr>
      <td>12</td>
      <td>4</td>
      <td>white</td>
      <td>28</td>
      <td>17 Engleside</td>
    </tr>
    <tr>
      <td>12</td>
      <td>4</td>
      <td>yellow</td>
      <td>11</td>
      <td>17 Engleside</td>
    </tr>
  </tbody>
</table>
</div>

`store_address` phụ thuộc vào `store_id` nên chúng ta sẽ đưa nó sang bảng `stores` riêng biệt.

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="4" style="text-align:center">stores</th>
    </tr>
    <tr>
      <th>id</th>
      <th>address</th>
      <th>phone</th>
      <th>manager</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>23 Maple</td>
      <td>555-6712</td>
      <td>Joe</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1902 Amber Ln.</td>
      <td>555-3478</td>
      <td>Susan</td>
    </tr>
    <tr>
      <td>3</td>
      <td>100 E. North St.</td>
      <td>555-0987</td>
      <td>Tara</td>
    </tr>
    <tr>
      <td>4</td>
      <td>17 Engleside</td>
      <td>555-6554</td>
      <td>Gordon</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="4" style="text-align:center">inventories</th>
    </tr>
    <tr>
      <th>toy_id</th>
      <th>store_id</th>
      <th>color</th>
      <th>inventory</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>5</td>
      <td>1</td>
      <td>white</td>
      <td>34</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2</td>
      <td>red</td>
      <td>34</td>
    </tr>
    <tr>
      <td>5</td>
      <td>3</td>
      <td>yellow</td>
      <td>12</td>
    </tr>
    <tr>
      <td>5</td>
      <td>1</td>
      <td>blue</td>
      <td>5</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2</td>
      <td>green</td>
      <td>10</td>
    </tr>
    <tr>
      <td>6</td>
      <td>4</td>
      <td>yellow</td>
      <td>24</td>
    </tr>
    <tr>
      <td>9</td>
      <td>1</td>
      <td>red</td>
      <td>50</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2</td>
      <td>blue</td>
      <td>2</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2</td>
      <td>green</td>
      <td>18</td>
    </tr>
    <tr>
      <td>12</td>
      <td>4</td>
      <td>white</td>
      <td>28</td>
    </tr>
    <tr>
      <td>12</td>
      <td>4</td>
      <td>yellow</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>

`toy_id`, `store_id`, `color` tạo thành khóa tổng hợp.

Nếu chúng ta thêm vào 1 loại đồ chơi ở 1 cửa hàng, chúng ta phải cập nhật cùng lúc 2 bảng `toys` và `inventories`.

Nếu các loại đồ chơi được định nghĩa sẵn, hay còn gọi là `toy_types`. Như vậy, nếu ta thêm 1 loại đồ chơi mới vào bảng `toys`, thì `color` tương tự cũng phải được định nghĩa sẵn chứ không thể đợi đến lúc cập nhật vào bảng `inventories` chúng ta mới có thể query để biết được loại đồ chơi này có những màu nào.

Và rõ ràng `color` là một thuộc tính thuộc về `toys` thay vì của `inventories`. Điều này dẫn đến vi phạm 1NF.

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="5" style="text-align:center">toys</th>
    </tr>
    <tr>
      <th>id</th>
      <th>inventory</th>
      <th>color</th>
      <th>cost</th>
      <th>weight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>whiffleball</td>
      <td>white</td>
      <td>1.95</td>
      <td>0.3</td>
    </tr>
    <tr>
      <td>2</td>
      <td>whiffleball</td>
      <td>yellow</td>
      <td>2.20</td>
      <td>0.4</td>
    </tr>
    <tr>
      <td>3</td>
      <td>whiffleball</td>
      <td>blue</td>
      <td>1.95</td>
      <td>0.3</td>
    </tr>
    <tr>
      <td>4</td>
      <td>frisbee</td>
      <td>green</td>
      <td>3.50</td>
      <td>0.5</td>
    </tr>
    <tr>
      <td>5</td>
      <td>frisbee</td>
      <td>yellow</td>
      <td>1.50</td>
      <td>0.2</td>
    </tr>
    <tr>
      <td>6</td>
      <td>kite</td>
      <td>red</td>
      <td>5.75</td>
      <td>1.2</td>
    </tr>
    <tr>
      <td>7</td>
      <td>kite</td>
      <td>blue</td>
      <td>5.75</td>
      <td>1.2</td>
    </tr>
    <tr>
      <td>8</td>
      <td>kite</td>
      <td>green</td>
      <td>3.15</td>
      <td>0.8</td>
    </tr>
    <tr>
      <td>9</td>
      <td>yoyo</td>
      <td>white</td>
      <td>4.25</td>
      <td>0.4</td>
    </tr>
    <tr>
      <td>10</td>
      <td>yoyo</td>
      <td>yellow</td>
      <td>1.50</td>
      <td>0.2</td>
    </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
  <tr>
    <th colspan="3" style="text-align:center">inventories</th>
  </tr>
  <tr>
    <th>toy_id</th>
    <th>store_id</th>
    <th>inventory</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>5</td>
    <td>1</td>
    <td>34</td>
  </tr>
  <tr>
    <td>5</td>
    <td>2</td>
    <td>34</td>
  </tr>
  <tr>
    <td>5</td>
    <td>3</td>
    <td>12</td>
  </tr>
  <tr>
    <td>5</td>
    <td>1</td>
    <td>5</td>
  </tr>
  <tr>
    <td>6</td>
    <td>2</td>
    <td>10</td>
  </tr>
  <tr>
    <td>6</td>
    <td>4</td>
    <td>24</td>
  </tr>
  <tr>
    <td>9</td>
    <td>1</td>
    <td>50</td>
  </tr>
  <tr>
    <td>9</td>
    <td>2</td>
    <td>2</td>
  </tr>
  <tr>
    <td>9</td>
    <td>2</td>
    <td>18</td>
  </tr>
  <tr>
    <td>12</td>
    <td>4</td>
    <td>28</td>
  </tr>
  <tr>
    <td>12</td>
    <td>4</td>
    <td>11</td>
  </tr>
  </tbody>
</table>
</div>

### iii. Third normal form (3NF)

1. Bảng đã ở 2NF
2. Bảng không có phụ thuộc hàm bắc cầu

Bảng dưới đây thỏa mãn 2NF nhưng vi phạm 3NF:

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
  <tr>
    <th colspan="4" style="text-align:center">tournament_winners</th>
  </tr>
  <tr>
    <th>tournament</th>
    <th>year</th>
    <th>winner</th>
    <th>date_of_birth</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>Indiana Invitational</td>
    <td>1998</td>
    <td>Al Fredrickson</td>
    <td>21 July 1975</td>
  </tr>
  <tr>
    <td>Cleveland Open</td>
    <td>1999</td>
    <td>Bob Albertson</td>
    <td>28 September 1968</td>
  </tr>
  <tr>
    <td>Des Moines Masters</td>
    <td>1999</td>
    <td>Al Fredrickson</td>
    <td>21 July 1975</td>
  </tr>
  <tr>
    <td>Indiana Invitational</td>
    <td>1999</td>
    <td>Chip Masterson</td>
    <td>14 March 1977</td>
  </tr>
  </tbody>
</table>
</div>

Khóa tổng hợp ở đây từ `tournamemt` và `year`. Tuy nhiên bảng này vi phạm 3NF vì `date_of_birth` (non-key column) phụ thuộc vào `winner` (non-key column).

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
    <tr>
      <th colspan="3" style="text-align:center">tournament_winners</th>
    </tr>
  <tr>
    <th>tournament</th>
    <th>year</th>
    <th>winner</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>Indiana Invitational</td>
    <td>1998</td>
    <td>Al Fredrickson</td>
  </tr>
  <tr>
    <td>Cleveland Open</td>
    <td>1999</td>
    <td>Bob Albertson</td>
  </tr>
  <tr>
    <td>Des Moines Masters</td>
    <td>1999</td>
    <td>Al Fredrickson</td>
  </tr>
  <tr>
    <td>Indiana Invitational</td>
    <td>1999</td>
    <td>Chip Masterson</td>
  </tr>
  </tbody>
</table>
</div>

<div class="table-wrapper">
<table class="pure-table pure-table-bordered">
  <thead>
  <tr>
    <th colspan="2" style="text-align:center">winner_dates_of_birth</th>
  </tr>
  <tr>
    <th>winner</th>
    <th>date_of_birth</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>Chip Masterson</td>
    <td>14 March 1977</td>
  </tr>
  <tr>
    <td>Al Fredrickson</td>
    <td>21 July 1975</td>
  </tr>
  <tr>
    <td>Bob Albertson </td>
    <td>28 September 1968</td>
  </tr>
  </tbody>
</table>
</div>

## Tham khảo

1. Head first SQL by Lynn Beighley
2. https://en.wikipedia.org/wiki/First_normal_form
