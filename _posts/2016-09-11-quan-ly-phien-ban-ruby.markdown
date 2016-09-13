---
layout: post
title:  "Quản lý phiên bản Ruby"
---
Mỗi dự án khác nhau đôi khi sử dụng những phiên bản/thư viện khác nhau của ngôn ngữ lập trình, và ruby cũng không ngoại lệ.

Với ruby chúng ta có 2 version manager đó là `rvm` và `rbenv`. Nhưng với sự đơn giản và dễ sử dụng, bài viết này sẽ trình bày về `rbenv`.

<!-- more -->
### Cài đặt

  **Lưu ý:** `rbenv` xung đột với `rvm` nên hãy gỡ bỏ hoàn toàn `rvm` trước khi bắt đầu.

1. Clone github repo vào thư mục `~/.rbenv`.

    ```bash
    $ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
    ```

    Có thể tùy chọn biên dịch một số tiện ích để tăng tốc `rbenv`, nếu có lỗi cũng không sao.

    ```bash
    $ cd ~/.rbenv && src/configure && make -C src
    ```

2. Thêm `~/.rbenv/bin` vào biến môi trường `$PATH` để có thể dùng lệnh `rbenv`.

    ```bash
    $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    ```

    **Với Ubuntu:** Sử dụng `~/.bashrc` thay cho `~/.bash_profile`.

    **Với zsh:** Sử dụng `~/.zshrc` thay cho `~/.bash_profile`.

3. Chạy lệnh `~/.rbenv/bin/rbenv init` để khởi tạo môi trường cho `rbenv`.

    Để tự động chạy lệnh trên mỗi lần khởi động bash:

    ```bash
    $ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    ```

    Sử dụng `~/.bashrc` hoặc `~/.zshrc` thay cho `~/.bash_profile` như ở bước 2.

4. Khởi động lại shell. Kiểm tra `rbenv` đã được cài đặt thành công hay chưa:

    ```bash
    $ type rbenv
    #=> "rbenv is a function"
    ```

5. Cài plugin `ruby-build`

    ```obal
    git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
    ```

6. Cài plugin `rbenv-gemset`

    ```
    $ git clone git://github.com/jf/rbenv-gemset.git $HOME/.rbenv/plugins/rbenv-gemset
    ```

### Sử dụng
1. Hiển thị tất cả các version của ruby:

    ```bash
    # list all available versions:
    $ rbenv install -l
    ```
    ![ruby available versions](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/ruby-available-version.png)
2. Cài đặt một phiên bản nào đó của ruby:

    ```bash
    $ rbenv install 2.2.1
    ```
    ![install ruby](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/install-ruby.png)

    Đối với Ubuntu, chúng ta cần phải cài thêm một số package:

    ```bash
    $ sudo apt-get install autoconf bison build-essential libssl-dev \
    libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev \
    libgdbm3 libgdbm-dev
    ```

    Các distro Linux khác có thể xem chi tiết [tại đây](https://github.com/rbenv/ruby-build/wiki#user-content-suggested-build-environment).

    Để xem các phiên bản ruby đã được cài:

    ```bash
    $ rbenv versions
    ```
3. Chuyển đổi giữa các phiên bản ruby:

    `rbenv` cung cấp 3 thiết đặt chuyển đổi phiên bản ruby.

    #### rbenv local
    ```bash
    $ rbenv local 2.2.1
    ```
    Khi đặt `local`, `rbenv` sẽ tạo một file `.ruby-version` trong thư mục hiện tại của shell, mỗi khi thay đổi đến thư mục đó, `rbenv` sẽ tự thiết đặt phiên bản `2.2.1`.

    Để xem phiên bản ruby hiện tại đang được thiết đặt:

    ```bash
    $ rbenv version
    ```
    ![rbenv local](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/rbenv-local.png)

    #### rbenv global
    ```bash
    $ rbenv global 2.2.1
    ```
    Với `global`, phiên bản `2.2.1` sẽ được áp dụng ở tất cả các shell. Tuy nhiên, nó sẽ bị ghi đè với phiên bản ruby được đặt `local`.

    #### rbenv shell
    ```bash
    $ rbenv shell 2.2.1
    ```
    Thiết đặt phiên ruby bản cho shell hiện tại. Nó khi đè phiên bản ruby được thiết đặt bới `local` và `global`.

    ![rbenv shell](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/rbenv-shell.png)

    Khi muốn gỡ thiết lập phiên bản ruby hiện tại:

    ```bash
    $ rbenv shell --unset
    ```
    Tương tự đối với `local` và `global`.

### Quản lý thư viện bên thứ ba với `rbenv-gemset`

`rbenv` thật sự vẫn chưa đủ khi các dự án khác nhau dùng chung một phiên bản ruby nhưng yêu cầu các thư viện (gem) khác nhau.
Plugin `rbenv-gemset` giải quyết vấn đề này một cách đơn giản.

Trong mỗi thư mục của mỗi dự án, chạy lệnh:

```bash
$ rbenv gemset init
```
`rbenv-gemset` sẽ tạo một file `.rbenv-gemsets`. Khi đó, mỗi lần install một gem với `gem install package-sample`, gem này sẽ thuộc về bộ gem chỉ dành cho dự án này.

#### Kế thừa gemset

```bash
$ rbenv gemset create [version] [gemset]
```
Với lệnh trên, một bộ gemset sẽ được tạo ứng với phiên bản ruby trong `[version]` nhưng nó sẽ không thuộc về một thư mục của dự án nào cả.
Và trong file `.rbenv-gemsets`, chúng ta có thể thêm bộ gemset này vào cuối file, và bộ gem của dự án sẽ kế thừa từ bộ gemset này.

![gemset list](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/gemset-list.png)

Ở hình trên, với phiên bản 2.3.1 chúng ta có 2 dự án là `arshavindn.github.io` và `sample-dir` tương ứng với 2 bộ gem. Và chúng ta muốn 2 bộ gem này dùng chung bộ gem `base`, nên ta sẽ thêm `base` vào **cuối** của 2 file `.rbenv-gemsets` vì khi cài một gem thì nó sẽ cài vào bộ gem nằm ở trên.

Cài đặt một số gem cho gemset `base`:

```
$ RBENV_GEMSETS="base" gem install bundle rubocop
```

_`bundle` là gem dùng để quản lý bộ gem dùng cho dự án thông qua Gemfile và `rubocop` là gem dùng để phát hiện lỗi trong code ruby, do đó dự án nào cũng cần có 2 gem này, ví dụ vậy._

**Lưu ý:** Sau khi cài một gem, cần phải chạy lệnh `$ rbenv rehash`.

Như vậy 2 bộ gem `arshavindn.github.io` và `sample-dir` sẽ sử dụng được gem `bundle` và `rubocop` mà không cần phải cài riêng lẽ cho chúng.

**arshavindn.github.io**
![gemset arshavindn.github.io](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/gemset\ arshavindn.github.io.png)

![demo arshavindn.github.io](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/demo\ arshavindn.github.io.png)

**sample-dir**
![demo sample-dir](/images/posts/2016-09-11-quan-ly-phien-ban-ruby/demo\ sample-dir.png)

### Kết luận
Khi bắt đầu một dự án, việc thiết lập môi trường sao cho không làm ảnh hưởng đến dự án khác là điều quan trọng. Và theo ý kiến cá nhân thì `rbenv`, `ruby-install` và `rbenv-gemset` thực hiện việc này một cách đơn giản và tiện lợi.

####Tham khảo thêm:
Github: [rbenv](https://github.com/rbenv/rbenv), [ruby-build](https://github.com/rbenv/ruby-build), [rbenv-gemset](https://github.com/jf/rbenv-gemset)
