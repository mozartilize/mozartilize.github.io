---
layout: post
title: "Trường hợp duy nhất có thể dùng callback trong Rails"
---

Để bắt đầu, chúng ta cùng đi qua một ví dụ kinh điển trong Rails, ứng dụng mua bán trực tuyến.

Thông thường chúng ta sẽ có các lớp `Product`, `Order`, `LineItem`, nhưng bây giờ chúng ta sẽ tập trung vào lớp `Order`.

Mỗi `Order` sẽ có nhiều `LineItem`. Để tối ưu, `Order` sẽ có một bản copy tổng giá tiền là tổng giá tiền của các `LineItem`.

Ở trang đăng kí, chúng ta sẽ có một tùy chọn khách hàng đó là khách hàng thường xuyên hay không (returning customer). Khách hàng thường xuyên sẽ được giảm 10% trong tổng giá tiền. `Order#returning_customer` sẽ lưu thông tin này.

### Hai trường hợp

Đến đây chúng ta có 2 trường hợp có thể sử dụng callback để quản lý trạng thái cho `Order`:

1. Tính `Order#total_price` dựa trên giá của các LineItem thuộc về Order đó
2. Cập nhật `Order#total_price` dựa trên khách hàng có phải khách hàng thường xuyên không

Nếu không suy nghĩ kĩ, chúng ta có thể dùng callback để tính `total_price` bằng tổng giá tiền các `LineItem` và sau đó áp dụng ưu đãi cho khách hàng thường xuyên. Tuy nhiên dùng callback cho trường hợp thứ nhất sẽ vi phạm [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) vì khi đó `Order` sẽ biết đến các thuộc tính của `LineItem` và cách để tính tổng giá tiền.

Dùng callback như dưới đây là chính xác:

```ruby
class Order < ActiveRecord::Base
  has_many :line_items

  validates :total_price, numericality: true

  before_create :apply_returning_customer_discount,
                if: lambda { |order| order.returning_customer? }

  def apply_returning_customer_discount
    self.total_price = self.total_price * 0.9
  end
end
```

### Nguyên tắc

`Order` có thể sử dụng callback cho trường hợp thứ 2 được vì nó không có phụ thuộc vào lớp bên ngoài (no external dependencies) trong callback. Đó chính là nguyên tắc. **_Sử dụng callback khi nó chỉ phụ thuộc vào các trạng thái bên trong đối tượng._**

Bạn có thể thắc mắc, làm sao `Order` tính được `total_price`? Đó sẽ là công việc của một service object cấp cao hơn nào đó có thể tương tác được với `Order` và `LineItem`. Service Object đó hoàn hảo nhất sẽ là một lớp thuần của Ruby.

### Tham khảo thêm

1. https://www.bignerdranch.com/blog/the-only-acceptable-use-for-callbacks-in-rails-ever
2. http://samuelmullen.com/2013/05/the-problem-with-rails-callbacks

