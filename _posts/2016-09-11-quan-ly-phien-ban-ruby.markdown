Mỗi dự án khác nhau đôi khi sử dụng những phiên bản/thư viện khác nhau của ngôn ngữ lập trình, và ruby cũng không ngoại lệ.
Với ruby chúng ta có 2 version manager đó là `rvm` và `rbenv`. Nhưng với sự đơn giản và dễ sử dụng, bài viết này sẽ trình bày về `rbenv`.
### Cài đặt
**Lưu ý:** `rbenv` xung đột với `rvm` nên hãy gỡ bỏ hoàn toàn `rvm` trước khi bắt đầu.
1. Clone github repo vào thư mục `~/.rbenv`.
```shell
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```
Có thể tùy chọn biên dịch một số tiện ích để tăng tốc `rbenv`, nếu có lỗi cũng không sao.
```shell
$ cd ~/.rbenv && src/configure && make -C src
```
2. Thêm `~/.rbenv/bin` vào biến môi trường `$PATH` để có thể dùng lệnh `rbenv`.
```shell
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
```
  **Với Ubuntu:** Sử dụng `~/.bashrc` thay cho `~/.bash_profile`.

  **Với zsh:** Sử dụng `~/.zshrc` thay cho `~/.bash_profile`.

3. Chạy lệnh `~/.rbenv/bin/rbenv init` để khởi tạo môi trường cho `rbenv`.

  Để tự động chạy lệnh trên mỗi lần khởi động shell:
```shell
$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
```
  Sử dụng `~/.bashrc` hoặc `~/.zshrc` thay cho `~/.bash_profile` như ở bước 2.
