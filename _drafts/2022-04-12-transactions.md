---
layout: post
title: "Transactions"
---

# Transactions

Trong một hệ thống dữ liệu, bất cứ lúc nào cũng có thể xảy ra những lỗi ảnh hưởng đến việc đọc, ghi dữ liệu (lỗi phần cứng, crashes, mất nguồn điện, concurrencies,...). Transaction là một cơ chế đơn giản hóa việc xử lý các lỗi này, tạo ra một abstraction để application sử dụng và tập trung vào business logic.

Một transaction là một tập hợp các lệnh đọc, ghi; được coi như là một đơn vị thực thi: hoặc là cả transaction thành công (commit), hoặc là cả transaction thất bại (abort, rollback).

Transaction cung cấp cho chúng ta các đảm bảo an toàn (safety guarantees) nhưng đôi khi chúng ta không sử dụng hoặc sử dụng transaction với mức đảm bảo an toàn thấp hơn.

Các đảm bảo an toàn mà transaction cung cấp cho chúng ta được biết đến với ACID: Atomicity, Consistency, Isolation và Durability.

Atomicity: Nghĩa là transaction là một đơn vị không thể chia nhỏ hơn nữa. Nếu một trong các lệnh thất bại thì cả transaction coi như thất bại.

Consistency: Đảm bảo tính toàn vẹn của dữ liệu bằng cách cung cấp các ràng buộc (uniqueness, foreign key...).

Isolation: Đảm bảo các transaction chạy đồng thời được tách biệt với nhau nếu chúng cùng truy cập vào một dữ liệu nào đó.

Durability: Đảm bảo rằng nếu transaction đã commit, dự liệu được lưu lại an toàn.