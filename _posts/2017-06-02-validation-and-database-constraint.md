---
layout: post
title: "Validation và database constraint"
---

Validation trong Rails rất đa dạng, từ kiểm tra tính tồn tại (presence), định dạng (format), duy nhất (uniqueness)... Với mỗi ràng buộc dữ liệu đều có thể được viết chỉ một dòng với phương thức `validates` nhằm đảm bảo dữ liệu được lưu phải chính xác và hợp lý.

### Khi validations vẫn không đủ

Như đối với [kiểm tra dữ liệu có phải là duy nhất](https://robots.thoughtbot.com/the-perils-of-uniqueness-validations), chúng ta buộc phải sử dụng ràng buộc của hệ quản trị CSDL vì validation ở lớp ứng dụng vẫn có kẽ hở. Lập trình viên phải dùng `null: false` trong schema hoặc [các ràng buộc được cung cấp bởi hệ quản trị CSDL](https://www.postgresql.org/docs/current/static/ddl-constraints.html).

Hẳn chúng ta sẽ đặt câu hỏi về việc lặp lại kiểm tra dữ liệu ở lớp ứng dụng (trong model) và lớp database (trong schema). Ví dụ:

```ruby
create_table "users", force: :cascade do |t|
  t.name :string, null: false
  t.username :string, null: false, index: true
  t.encrypted_password :string, null: false
  t.belongs_to :organization,
    null: false,
    index: :true,
    foreign_key: { on_delete: :cascade }
  t.timestamps

  t.index :username, unique: true
end
```

```ruby
class User < ApplicationRecord
  attr_accessor :password

  belongs_to :organization

  validates :name, presence: true
  validates :username, presence: true, uniqueness: true
  validates :password, presence: true
  validates :encrypted_password, presence: true
end
```

Chúng ta có bảng users và model User tương ứng, cho phép người dùng có thể đăng kí, đặt lại mật khẩu, thay đổi tên. Người dùng thuộc về một tổ chức (organization) được khai báo khi đăng nhập thông qua tham số truyền qua route.

### Vậy DRY thì sao?

Dễ dàng nhận ra nhiều sự lặp lại, tuy nhiên, chúng ta cần phải biết rằng mục đích của ràng buộc ở schema và validation ở model là khác nhau.

Ràng buộc ở schema đảm bảo dữ liệu được lưu vào database phải chính xác và hợp lý với yêu cầu nghiệp vụ. Schema cho thấy người dùng phải có tên (name), tên đăng nhập (username), mật khẩu đã mã hóa (encrypted_password) và tổ chức liên quan (organization). Schema ràng buộc username là duy nhất, organization phải tồn tại và khi organization bị xóa thì tất cả user liên quan cũng bị xóa theo để tránh `foreign_key` không phù hợp.

Kiểm tra dữ liệu trong model cung cấp một giao diện người dùng về thông báo lỗi. Nếu người dùng không nhập trường `username`, presence validation (kiểm tra tính tồn tại) sẽ đưa ra thông báo cho người dùng biết.

### Lựa chọn validation và database constraint

Chúng ta không nhất thiết phải viết mọi validation cho mỗi constraint và ngược lại. Trước khi lựa chọn, chúng ta nên tìm ra câu trả lời cho 2 câu hỏi sau:

1. Bạn có muốn ngăn chặn dữ liệu không chính xác được lưu vào database? Nếu có thì bạn phải dùng constraint.

2. Bạn có muốn tránh những lỗi mà người dùng có thể tự sữa chữa được? Nếu vậy bạn nên dùng validation.

Xem lại ví dụ ở trên, với validation kiểm tra sự tồn tại của `encrypted_password`, nếu nó để trống nghĩa là ứng dụng của chúng ta có bug, và nó có thể sẽ hiển thì thông báo ra phía người dùng. Tuy nhiên, trường này lại không có trong form, nên người dùng không thể xử lý được.

Nếu bỏ dòng validation đó đi, ứng dụng vẫn có thể có lỗi vì constraint `null: false`, tuy nhiên đó là lỗi phía lập trình.

Với trường `organization`, mặc định trong Rails 5, `belongs_to` mặc định validate tính tồn tại. Từ đó model của chúng ta có thể được sửa lại như sau:

```ruby
class User < ApplicationRecord
  attr_accessor :password

  belongs_to :organization, optional: true

  validates :name, presence: true
  validates :username, presence: true, uniqueness: true
  validates :password, presence: true
end
```

### Thiết kế ứng dụng

Việc validate dữ liệu ở phía ứng dụng nên được tách ra khỏi model bằng cách sử dụng form object. Form object cung cấp giao diện người dùng để tạo một hoặc nhiều model. Khi chúng ta đưa validation ra phía giao diện người dùng, nó sẽ giúp cho việc kiểm tra dữ liệu trở nên hợp lý và việc lặp lại là điều dễ hiểu.

Form object trong từng ngữ cảnh giúp đưa ra những thông báo lỗi phù hợp với ngữ cảnh đó. Với ví dụ trên chúng ta có thể sử dụng `RegistrationForm`, `ProfileForm`, và `PasswordResetForm` và lớp `User` sẽ hoàn toàn không có bất kì validation nào.

### Tham khảo thêm

1. [https://robots.thoughtbot.com/validation-database-constraint-or-both](https://robots.thoughtbot.com/validation-database-constraint-or-both)
2. [https://www.postgresql.org/docs/current/static/ddl-constraints.html](https://www.postgresql.org/docs/current/static/ddl-constraints.html)
3. [https://robots.thoughtbot.com/referential-integrity-with-foreign-keys#caveats](https://robots.thoughtbot.com/referential-integrity-with-foreign-keys#caveats)
4. [https://robots.thoughtbot.com/the-perils-of-uniqueness-validations](https://robots.thoughtbot.com/the-perils-of-uniqueness-validations)
