---
layout: post
title: "What ORM lie to you"
---

### What is ORM?
Nếu bạn là web developer và dùng một framework nào đó, chắc hẳn bạn đã biết về ORM. Còn nếu không, wow, good for you!

ORM - viết tắt của Object Relational Mapping, là kĩ thuật ánh xạ object model (từ class của ngôn ngữ lập trình) sang relation model (từ table của cơ sở dữ liệu) và ngược lại. Nghĩa là nếu bạn tạo một model class thì ORM sẽ giúp bạn tạo tabel tương ứng trong DB, nếu bạn query dữ liệu từ DB, ORM sẽ giúp bạn chuyển data đó sang object.

Chính vì thế ORM giúp cho quá trình phát triển ứng dụng nhanh hơn, giúp bạn tập trung về business nhiều hơn là về vấn đề kĩ thuật.

Tuy nhiên, đến khi ứng dụng của bạn đủ phức tạp, bạn sẽ thấy ORM sẽ có những hạn chế của nó:
- Việc tạo ra nhiều object model sẽ tạo nhiều relational model tương ứng, điều đó khiến cho việc query ở database trở nên phức tạp, giảm performance. Ví dụ về phía ứng dụng bạn muốn tạo 2 model `Account` liên quan đến thông tin đăng nhập hệ thống và `Profile` liên quan đến thông tin cá nhân người dùng, tương ứng bạn sẽ có 2 table tương tự trong database, nhưng thực ra bạn có thể chỉ cần tạo một table `users` để lưu những thông tin đó vào database.
- Nhiều lập trình viên quá phụ thuộc vào ORM, nhưng thực ra nó lại liên quan nhiều đến SQL nên bạn phải biết SQL để sử dụng ORM hiệu quả, và nhiều trường hợp, ORM không thể generate sang những câu lệnh SQL thích hợp, nó chuyển sang dùng preload thay vì `join` hoặc `subquery` chẳng hạn, khiến cho performance bị giảm xuống.
- Mỗi ngôn ngữ khác nhau có những thư viện ORM khác nhau: Ruby có ActiveRecord, Sequel...; Python có Django ORM, SQLAlchemy... và nếu chuyển ngôn ngữ hay framework yêu cầu bạn phải học API của những thư viện đó, trong khi mục đích chung của chúng là để truy vấn sql (cứ như hằng sa số complier cho JS vậy).


### What ORM lie to us
Object-oriented model có nhiều điểm mạnh hơn so với relational model, vì phát triển phần mềm là một quá trình khó khắn.
OOP cung cấp attribute, behavior (method) cho object, trong khi relational model cung cấp data.
Nếu cố gắng mapping 1:1 giữa O và R, chúng ta sẽ giới hạn khả năng của cả hai công cụ trên.

>**Mixing messy data with messy and unpredictable behavior is what ORM really means** - [*Solnic*](http://solnic.eu/2015/09/18/ditch-your-orm.html)

Trong ứng dụng thực tế, giả sử bạn muốn hiển thị thông tin của User gồm email, fullname, address, age và như trên chúng ta có 2 model là `Account(email)`, `Profile(fullname, address, date_of_birth)`.
Phía ứng dụng, bạn phải định nghĩa attibute email cho Profile nếu bạn muốn query từ Profile và hiển thị dữ liệu.

```python
class Profile(models.Model):
    @property
    def email(self):
        return self.account.email

    @property
    def age(self):
        today = date.today()
        return today.year - self.date_of_birth.year

# then query
profiles = Profile.objects.all().preload_related('account')
```
```html
# then show in template
{% for profile in profiles %}
  - {{ profile.fullname }}
  - {{ profile.email }}
  - {{ profile.age }}
  - {{ profile.address }}
{% endfor %}
```

Ở câu query, thay vì thực hiện join, Django ORM sẽ thực hiện 2 query, vì preload accounts giúp tránh trường hợp N+1 query. Nếu không, mỗi lần lặp qua profile ở template, Django sẽ query lần lượt qua từng row của account để lấy email:

Có preload:
```SQL
SELECT profiles.* FROM profiles;
SELECT accounts.* FROM accounts;
```

Không preload:
```SQL
SELECT profiles.* FROM profiles;
SELECT accounts.* FROM accounts JOIN profiles ON profiles.account_id = accounts.id where profiles.id = 1;
SELECT accounts.* FROM accounts JOIN profiles ON profiles.account_id = accounts.id where profiles.id = 2;
SELECT accounts.* FROM accounts JOIN profiles ON profiles.account_id = accounts.id where profiles.id = 3;
...
```

ORM đơn giản sẽ xử lý như vậy, nếu ứng dụng phức tạp hơn, ORM có thể không hỗ trợ generate query cho bạn.
Nếu là database, nó sẽ query như sau:
```SQL
SELECT p.fullname, p.address, DATE_PART('year', AGE(p.date_of_birth)) AS age, a.email
FROM profiles p
JOIN accounts a ON a.id = p.account_id;
```

Như ở trên, chúng ta xử dụng các hàm của SQL để truy vấn dữ liệu như DATE_PART, AGE chứ không cần phải tính attribute age cho Profile. Tất nhiên, như vậy sẽ nhanh hơn nhiều nếu so Python vs SQL (cụ thể là Postgresql).


### What Django ORM lie to me
Việc O/R Mapping của ORM đã tạo nên một lớp abstraction và lớp này che giấu đi quá trình chuyển đổi dữ liệu từ ngôn ngữ lập trình sang cơ sở dữ liệu.
Ví dụ về model sau:

```python
from django.db import models

class Account(models.Model):
    email = models.EmailField(nullable=True, default='')
```

Về phía ứng dụng, trường email với kiểu dữ liệu là EmailField có vẻ hợp lý, nhưng khi tạo table trong database, column email sẽ là VARCHAR và max_length của nó là bao nhiêu thì phải vào source code của django mới biết được. Vậy EmailField ở đây giúp ích gì? Thực ra nó giúp validate dữ liệu khi bạn tạo record từ cli (`./manage.py shell`) và map trường này nếu bạn có tạo ModelForm tương ứng cho Account, bạn không cần phải viết lại validation cho nó. Wow, too much magic!

Và thêm một điều nữa, argument `default`. Trong database, khi định nghĩa column bạn có thể sẽ thêm tùy chọn DEFAULT VALUE cho nó, Django ORM làm được điều này với `nullable=True` (trong database `NULL`) nhưng lại không làm được với default. Điều đó khiến cho mình bất ngờ, vì hiện tại mình đang làm một dự án mà api và admin dùng chung một database, nhưng khác ngôn ngữ. Việc tạo table từ ORM ban đầu theo mình nghĩ sẽ giúp đơn giản hơn, nhưng hóa ra lại gây khó khăn cho người viết api.

### Solution
Mình sẽ chỉ đưa ra solution ngắn ngọn của Jeff Atwood từ [Object-Relational Mapping is the Vietnam of Computer Science](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science/).

Không có giải pháp hoàn hảo nào có thể giải quyết được vấn đề của ORM. Vẫn có những giải pháp, nhưng chúng đều bao gồm những bước lùi, rủi ro nghiêm trọng. Điều tệ hơn là bạn sẽ chưa thấy hậu quả đến khi viết code.

Một giải pháp hữu hiệu là chọn một trong 2 giữa O và R. Nếu bạn bỏ đi đẳng thức giữa O và R, bạn sẽ không còn gặp vấn đề về mapping nữa.
