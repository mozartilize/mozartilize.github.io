---
layout: post
title: "Database table design"
---

## 1. Smart table design

Chúng ta có 2 table như sau:


fish_info
| common           | species      | location             | weight     |
| ---------------- | ------------ | -------------------- | ---------- |
| bass, largemouth | M. salmoides | Montgomery Lake, GA  | 22 lb 4 oz |
| walleye          | S. vitreus   | Old Hickory Lake, TN | 25 lb 0 oz |

fish_records
| first_name | last_name | common           | location         | state | weight     | date     |
| ---------- | --------- | ---------------- | ---------------- | ----- | ---------- | -------- |
| George     | Perry     | bass, largemouth | Montgomery Lake  | GA    | 22 lb 4 oz | 6/2/1932 |
| Mabry      | Harper    | walleye          | Old Hickory Lake | TN    | 25 lb 0 oz | 8/2/1960 |

Hai table trên có một số cột giống nhau, một số cột mà table kia không có, và có cột được tách ra như với `location` và `state`.

Theo bạn nghĩ thì table nào được thiết kế tốt hơn?

Câu trả lời là không có table nào cả. Cách bạn thiết kế table hoàn toàn dựa vào mục đích sử dụng của bạn. Giả sử table `fish_info` được dùng trong công việc của một nhà thủy sinh học, còn table `fish_records` dành cho một nhà buôn thủy sản. Vì nhà buôn thủy sản cần biết thông tin của người bắt được loài cá đó còn nhà thủy sinh học thì không. Vì nhà buôn thủy sản nhiều lúc cần search thông tin của `state` nơi bắt được loài cá đó, nên cần phải tách cột đó ra để query được nhanh hơn...

`first_name`, `last_name` ở table `fish_records` được tách ra vậy cột `date` có cần tách ra các cột `day`, `month`, `year` không? Điều đó còn tùy, nếu nhà buôn cảm thấy thông tin được chia nhỏ đến mức như vậy là đủ rồi thì không cần thiết nữa. Dữ liệu lúc đó là đã **atomic** - nghĩa là nó không thể hoặc không nên chia nhỏ nữa.



