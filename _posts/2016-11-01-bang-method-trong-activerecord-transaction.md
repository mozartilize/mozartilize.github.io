---
layout: post
title:  "Bang method trong ActiveRecord Transaction"
---
Chúng ta có 2 đoạn code như sau:

```ruby
def create
  @user = User.new params[:user]
  @user.save!
  redirect_to user_path(@user)
rescue ActiveRecord::RecordNotSaved
  flash[:notice] = 'Unable to create user'
  render :action => :new
end
```

và:

```ruby
def create
  @user = User.new params[:user]
  if @user.save
    redirect_to user_path(@user)
  else
    flash[:notice] = 'Unable to create user'
    render :action => :new
  end
end
```
<!-- more -->

Sự khác biệt ở đây là method `save!` (bang method) và `save`. Trong rails, bang method sẽ ném ra một ngoại lệ nếu như phương thức đó có lỗi, còn ở cách thứ hai, rails đơn thuần trả về `true` hoặc `false`. Vậy thì với 2 cách trên, chúng ta nên sử dụng cách nào?

> Exceptions should not be expected

Có thể hiểu là "Ngoại lệ là không lường trước được."

Khi viết một ứng dụng, bạn có thể lường trước được việc người dùng có thể không nhập hoặc nhập sai vào một trường nào đó. Do đó, chúng ta KHÔNG NÊN dùng exception để xử lý vì exception chỉ nên dùng ở những người hợp chúng ta không biết trước được, như là:

- Mất kết nối đến cơ sở dữ liệu
- Hết bộ nhớ
- Lỗi truy xuất (IO/socket)

Nếu những lỗi trên xảy ra, tốt hơn hết là trả về cho người dùng trang thông báo lỗi (500) và lập trình viên sẽ được thông báo về những lỗi đó thông qua email chẳng hạn.

Ví dụ khi đọc dữ liệu từ file:

```ruby
File.open('testfile') do |file|
  while line = file.gets
    puts line
  end
end
```

Phương thức IO#gets trả về `String` hoặc `nil`. Nếu như trả về `nil` nghĩa là đã đọc đến cuối file và điều này hoàn toàn hợp lý. Đó không phải là một ngoại lệ và nên xử lý bằng các lệnh điều kiện.

Nếu ruby ném ngoại lệ EOFError:

```ruby
File.open('testfile') do |file|
  begin
    while line = file.readline
      puts line
    end
  rescue EOFError
    # do nothing
  end
end
```

Ném ngoại lệ khi đọc đến cuối file nghĩa là điều đó không được lường trước. Thật điên rồ!

Vậy thì trong Rails, bang method của `ActiveRecord` như `save!` hay `update_attibutes!` nên được loại bỏ khỏi API chăng? Vì chúng ta có thể xử lý thông qua giá trị true/false trả về của `save` hoặc `update_attributes`.

Tuy nhiên, các bang method vẫn được sử dụng, trong transaction. Cách duy nhất đề roll back một transaction là ném ra một ngoại lệ.

Trong ruby, ý nghĩa của bang method là chỉnh sửa từ đối tượng nguồn. Còn trong rails, `!` trong `save!`... có nghĩa là làm điều gì đó nguy hiểm nên cần phải ném ra ngoại lệ.

```ruby
ActiveRecord::Base.transaction do
  david.update_attributes!(:amount => -100)
  mary.update_attributes!(:amount => 100)
end
```

Ví dụ trên thực hiện chuyển 100$ từ tài khoản của `david` sang `mary`. Nếu một trong 2 thao tác trên thất bại thì toàn bộ giao dịch thất bại, do đó chúng ta cần dùng đến transaction và exception từ bang method sẽ là dấu hiệu để roll back lại dữ liệu nếu có xảy ra lỗi.

> ### Một vài trường hợp KHÔNG NÊN dùng transaction:
1. Sử dụng transaction cho một record đơn lẻ.
2. Những transaction lồng nhau không cần thiết.
3. Block code của transaction không ném ra ngoại lệ.
4. Sử dụng transction trong controller.
