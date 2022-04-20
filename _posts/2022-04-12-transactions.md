---
layout: post
title: "Transactions"
---

Trong một hệ thống dữ liệu, việc đọc, ghi dữ liệu bất cứ lúc nào cũng có thể xảy ra lỗi (lỗi phần cứng, ứng dụng crash, mất nguồn điện, vấn đề về xử lý song song,...). Transaction là một cơ chế đơn giản hóa việc xử lý các lỗi này, tạo ra một abstraction để application sử dụng và tập trung vào business logic.

Một transaction là một tập hợp các lệnh đọc, ghi; được coi như là một đơn vị thực thi: hoặc là cả transaction thành công (commit), hoặc là cả transaction thất bại (abort, rollback).

Transaction cung cấp cho chúng ta các đảm bảo an toàn (safety guarantees) nhưng đôi khi chúng ta không sử dụng hoặc sử dụng transaction với mức đảm bảo an toàn thấp hơn.

Các đảm bảo an toàn mà transaction cung cấp cho chúng ta được biết đến với ACID: Atomicity, Consistency, Isolation và Durability.

Atomicity: Nghĩa là transaction là một đơn vị không thể chia nhỏ hơn nữa. Nếu một trong các lệnh thất bại thì cả transaction coi như thất bại.

Consistency: Đảm bảo tính toàn vẹn của dữ liệu bằng cách cung cấp các ràng buộc (uniqueness, foreign key...).

Isolation: Đảm bảo các transaction chạy đồng thời được tách biệt với nhau nếu chúng cùng truy cập vào một dữ liệu nào đó.

Durability: Đảm bảo rằng nếu transaction đã commit, dự liệu được lưu lại an toàn.

Bài viết này sẽ tập trung vào Isolation Levels.

# Why Isolation Levels?

Nếu hai transaction không đụng đến cùng một dữ liệu, chúng có thể chạy song song một cách an toàn. Những vấn đề về xử lý song song (race conditions) chỉ xuất hiện khi một transaction đọc dữ liệu từ một transaction khác đang chỉnh sửa chúng, hoặc cả hai cùng đồng thời chỉnh sửa một dữ liệu nào đó.

Bug liên quan đến xử lý song song khó tìm ra bằng test, vì nó chỉ xảy ra theo hướng hên xui. Nếu một ứng dụng có nhiều người dùng truy cập vào cùng thời điểm khiến cho vấn đề này càng khó giải quyết hơn nữa.

Isolation level là đáp án cho vấn đề này. Serializable isolation level là một loại trong số đó đảm bảo rằng không có transaction nào chạy song song bằng cách ép buộc chúng chạy theo tuần tự. Tuy nhiên nó lại ảnh hưởng đến hiệu suất của ứng dụng. Do đó ứng dụng thường dùng những isolation level yếu hơn. Chúng không đảm bảo cho bạn tránh khỏi 100% bug do xử lý song song nhưng trong thực tế vẫn được dùng.

Chúng ta hãy cùng tìm hiểu các race condition và cách giải quyết chúng khi sử dụng transaction.

## Read Committed

1. Khi đọc dữ liệu từ database, bạn chỉ đọc dữ liệu đã được transaction commit (no dirty reads).
2. Khi ghi dữ liệu từ database, bạn chỉ ghi đè dữ liệu đã được commit (no dirty writes).

### Dirty reads

Nếu bạn có 2 transaction chạy song song từ 2 user 1 và 2. Giả sử dữ liệu x=1, và cả 2 transaction đều đọc ra x=1, bây giờ transaction 1 set x=2, nếu transaction 2 đọc lại x từ database nó sẽ trả về x=2, mặc dù transaction 1 chưa commit. Đó gọi là dirty read.

### Dirty writes

![dirty write](/images/posts/2022-04-12-transactions/dirty-write.png)

Khi 2 transaction cùng chỉnh sửa một dữ liệu. Transaction 1 set x=2, chưa commit; transaction 2 set x=3 và commit. Như vậy transaction 2 đã ghi đè vào dữ liệu chưa được commit từ transaction 1.

## Repeatable Read (Snapshot Isolation)

### Unreadtable Read (Read Skew)

Ví dụ bạn có 1 transaction update data qua nhiều bước.
Như ngân hàng Alice có 2 tài khoản, 1 tài khoản thẻ, 1 tài khoản mobile chẳng hạn.

![read skew](/images/posts/2022-04-12-transactions/read-skew.png)

Cô ấy muốn xem tổng số tiền của 2 tài khoản là bao nhiêu, nhưng giữa 2 câu lệnh select lại có 1 transaction đang thực thi. Unreadtable read gây ra lỗi chỉ lấy được tổng $900 nhưng thực tế là $1000.

Alice có thể dễ dàng tải lại trang web để kiểm tra tài khoản của mình nhưng trong một số trường hợp không thể.

- Sao lưu dữ liệu.
- Các câu truy vấn lấy dữ liệu cho phân tích hoặc kiểm tra tính toàn vẹn của dữ liệu.

Repeatable Read (Snapshot) Isolation level giải quyết được vấn đề này.

## Lost Updates

Vấn đề về lost updates xảy ra khi application đọc dữ liệu ra, xử lý trên dữ liệu đó một khoảng thời gian rồi lưu lại dữ liệu vừa đọc ra đó. Nó được gọi là read-modify-write cycle. Nếu 2 transaction cùng chuỗi lệnh như vậy, 1 trong 2 transaction sẽ bị mất đi dữ liệu nó đã xử lý.

![lost updates](/images/posts/2022-04-12-transactions/lost-updates.png)

Cả read committed và snapshot isolation level không giải quyết được vấn đề này.

Tuy nhiên vẫn có một số cách khác:

### Atomic write operation

Đó chính là dùng câu lệnh `UPDATE ... SET ... WHERE` của SQL.Tuy nhiên, một điều đáng nói là các ORM framework thường hướng mọi người viết code theo read-modify-write cycle thay vì atomic write.

### Explicit locking

Chúng ta vẫn thực thi theo read-modify-write cycle, tuy nhiên dữ liệu đọc ra sẽ bị lock lại không cho transaction khác đọc nó cho đến khi được update.

```sql
BEGIN TRANSACTION;
SELECT * FROM figures
WHERE name = 'robot' AND game_id = 222
FOR UPDATE;
-- Check whether move is valid, then update the position
-- of the piece that was returned by the previous SELECT.
UPDATE figures SET position = 'c4' WHERE id = 1234;
COMMIT;
```

## Phantom Read và Write Skew

Phantom read xảy ra khi bạn có 2 transaction mà đầu tiên chúng có cùng 1 query để lọc ra dữ liệu (records). Lúc này 1 trong 2 transaction có thể insert hay delete 1 trong số các records đó, nếu sau đó transaction còn lại thực thi lại câu query kia thì nó không trả về cùng một kết quả nữa (phantom read).

![write skew](/images/posts/2022-04-12-transactions/write-skew.png)

Ví dụ ở bệnh viện 1 ca trực cần có ít nhất 1 bác sĩ. Tuy nhiên Carol ốm nên đã xin nghỉ trước. Bây giờ còn Bob và Alice 2 người cũng thấy không khỏe nên muốn xin nghỉ luôn.

Đầu tiên nó kiểm tra số bác sĩ sẽ trực đêm đó, trả về 2. Vì cả 2 người nhận cùng kết quả là 2 nên sẽ tiếp tục cho xử lý, đó là set column `on_call` sang `false`. Nhưng việc này đã vi phạm requirement của ứng dụng. Ta gọi việc update column `on_call` sang `false` là write skew.

### Định nghĩa Write Skew

Write skew không phải dirty write hay lost updates, vì nó update các record khác nhau. Nếu 2 transaction trên chạy theo thứ tự, kết quả query sẽ khác và người kia không được update cột `on_call` nữa. Cho nên có thể nói đây là race condition vì 2 transaction cùng chạy song song.

Chúng ta có thể nói write skew là trường hợp bao quát của lost update. Write skew xảy ra khi 2 transaction đọc ra cùng các record rồi update một vài trong số chúng (mỗi transaction có thể update các record khác nhau). Trong trường hợp đặc biệt, nếu các transaction update cùng một record, nó sẽ gây ra dirty read hoặc lost update (tùy vào timing).

Một vài ví dụ về write skew:
- Đặt trước phòng họp (meeting room booking).
- Multiplayer game: 2 người chơi cùng đặt vật phẩm vào 1 ô.
- Đăng kí account bằng username không bị trùng.
- Không được tiêu quá số tiền cho trước.

### Giải pháp cho write skew

Với ví dụ về ca trực chúng ta có thể dùng `SELECT ... FOR UPDATE`, tuy nhiên với meeting room booking hoặc tiêu tiền thì có thể chúng ta sẽ không có record nào để `SELECT ... FOR UPDATE` có thể acquire lock được.

#### Materializing conflicts

Nếu trong trường hợp không có record nào để lock, chúng ta có thể tạo sẵn chúng trước khi bắt đầu transaction. Tuy nhiên giải pháp này có thể khó thực hiện và dễ phát sinh bug, thêm vào đó nó leak cơ chế quản lý chạy song song (concurrency control mechanism leak) vào business code (vi phạm single responsibility, maybe?).

#### Serializable isolation level

Sử dụng serializable isolation level là phù hợp nhất trong những trường hợp như thế này.

Mình có tạo một repo demo giải quyết vấn đề write skew của booking meeting room [ở đây](https://github.com/mozartilize/db-isolation-level-demo).

## What's next?

Có một điều chúng ta có thể nhận ra ở write skew là nó vi phạm requirement, tuy nhiên điều đó lại phụ thuộc vào phía logic của ứng dụng. Chúng ta cần đảm bảo consistency nhưng không thông qua những gì database cung cấp (foreign key, uniquess...). Bài viết tới sẽ tập trung vào consistency và distributed transation.

## Tham khảo

[Isolation Levels in Database Management Systems](https://www.youtube.com/watch?v=-gxyut1VLcs)

[[Backend #9] Understand isolation levels & read phenomena in MySQL & PostgreSQL via examples](https://www.youtube.com/watch?v=4EajrPgJAk0)

Martin Kleppmann's Design Data-Intensive Applications
