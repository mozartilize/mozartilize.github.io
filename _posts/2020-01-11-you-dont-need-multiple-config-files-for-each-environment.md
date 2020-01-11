---
layout: post
title: "You don't need multiple config files for each environment"
---

## Configuration file

Config file hoặc setting file cung cấp cho bạn khả năng quản lý các flag hoặc các tùy chọn để ứng dụng có thể bật tắt chức năng nào đó (eg: debugger), cũng có thể config cho một service nào đó (eg: database). Một khi có config cho một module hay tính năng, thì config đó cũng đã có giá trị mặc định trong ứng dụng của bạn và nó cần được document về việc nó là cái gì, sử dụng ra sao, có những option nào, vân vân.

<!-- more -->

Ví dụ với vscode, bạn có thể thay đổi theme tùy ý, thay đổi đó đã được lưu vào một file settings.json nào đó trong máy tính của bạn, nhưng trong lần khởi chạy đầu tiên, rõ ràng nó đã load theme mặc định. vscode là ứng dụng miễn phí, bạn tải về cài trên máy tính mình sử dụng, hay nói cách khác bạn đã deploy nó trên môi trường của mình, tất nhiên bạn được hướng dẫn làm sao để đổi theme.

## Wep application configuration

Nếu bạn bắt đầu dự án với Rails, nó sẽ cung cấp cho bạn sẵn 3 files config tương ứng với 3 môi trường development, test và production. Thoạt đầu nghĩ điều đó cũng hợp lý, ví dụ với môi trường local bạn chỉ cần một mail server đơn giản với host và post, trong khi với production, có thể bạn sẽ dùng service của bên thứ ba với nhiều config hơn thế.

Trái lại với Django, framework này chỉ cung cấp cho bạn mỗi file `settings.py` và mọi người bắt đầu tìm cách hoặc là thư viện giúp bạn tách config ra cho từng môi trường.

Sau một dự án dùng Django và chia ra nhiều file config thì mình nhận ra thật sự những gì Django cung cấp mặc định như vậy là quá đủ.

Có một câu nói như thế này:

> Make your local development close to production as much as possible.

Và rõ ràng là nó đúng. Điều đó giúp bạn chủ động hơn trong việc debug những lỗi trên production cũng như hạn chế những tình huống trả lời với sếp như “Trên máy em chạy ok mà!!!”

Môi trường dev và production khác nhau, và có những tính năng chỉ bật cho production, tắt cho development và ngược lại, điều này có thể dẫn đến việc production cần thêm những tùy chọn khác. Cụ thể hơn là 2 file config kia sẽ rất khác nhau. Có thể có những developer chỉ tập trung vào phát triển ở local và những config kia họ không hề biết đến. **Đặt những config này vào một file giúp cho việc nắm bắt chúng trở nên nhất quán.**

Bạn tự hỏi tính năng này chỉ bật trên production sao lại để cùng một file config? Không! Bạn phải xử lý giá trị mặc định tùy chọn bật tắt trên production trong ứng dụng của bạn.

## My structure

- Đặt configuration file bên ngoài ứng dụng. Tùy vào nền tảng hay framework bạn sẽ chọn file config cho phù hợp. Ví dụ bạn cho phép đặt các tùy chọn trong file json, ini file, plain python object hoặc ruby object... [Đây](https://github.com/pallets/flask/pull/3398) là cách flask xử lý việc load config file.

- Dynamic config file bằng environmnet variable. Nếu config file của bạn là python object chẳng hạn, bạn có thể load environmnet variable cùng với việc đặt giá trị mặc định. Ngoài ra bạn có thể load environmnet variable từ env file dựa vào những tool như python-dotenv. Docker, heroku... cung cấp những cách cho bạn sử dụng environmnet variable.

## Tham khảo

- [Self-hosted Sentry](https://docs.sentry.io/server/installation/)
- [Miroblog Flask app config](https://github.com/miguelgrinberg/microblog/blob/master/config.py)