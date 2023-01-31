---
layout: post
title: "Framework vs Your Application and Packaging"
---

Vừa rồi mình có làm một presentation về "12 factor app" trên công ty. Sau nhiều
năm viết kha khá ứng dụng web, và tự config linux desktop environment (mình đang
dùng sway, ở cả máy công ty cũng như máy cá nhân) thì có thể nói mình đã thấm nhuần
những factor đó (ha). Nhưng vì sao config linux desktop lại liên quan đến ứng dụng
web?

# Framework vs Application

Có lẽ tựa đề hơi clickbail một chút nhưng mình sẽ chia sẻ về những web framework
mình từng sử dụng như django, ruby on rails, flask. Có thể nói ngành IT ở Việt
Nam khá hot và cũng có rất nhiều tài liệu cũng như tutorials để mọi người học,
thời gian để cho ra một sản phẩm cũng rất nhanh, phần lớn nhờ vào những
framwork. Tuy nhiên theo mình thấy chúng được quảng cáo thái quá về việc chúng
tốt như thế nào, nhanh, dễ sử dụng ra làm sao. Và với một vài command, dòng code
chúng ta đã có một web application. Điều đó thật sự không tốt. Không tốt ở chỗ những
framework này cho chúng là cả một ứng dụng, từ cách lên cấu trúc (structure) đến
deploy lên production không theo tiêu chuẩn từ ngôn ngữ mà chúng được viết. Nó
sẽ là rào cản thực sự khi ứng dụng của bạn grow, cả về codebase lẫn quy mô
deploy.

Những web framework theo mô hình MVC thường có concept "Thin Controllers, and
Fat Models". Họ chỉ thấy framework là application, và chỉ có mỗi web interface
nên business logic hoặc viết ở controller, hoặc ở model. Nhưng nếu các bạn muốn
ứng dụng của mình hỗ trợ thêm CLI hoặc native graphical (ứng dụng desktop truyền
thống) thì chúng làm gì có controller, còn nếu đặt ở model thì nó vi phạm Single
Responsibility Principle (đi chi tiết thì có thể sẽ trong một post khác).

Nơi tốt nhất để viết business logic chỉ có thể ở service code, chúng tốt nhất là
những function, không cần thiết phải là class gì phức tạp vì dù sao đi nữa ứng
dụng của chúng ta phần lớn là để transform dữ liệu [0].

Điều quan trọng mình muốn nhấn mạnh ở đây là framework cũng chỉ là thư viện để
hỗ trợ cho ứng dụng của bạn, nó là một phần của ứng dụng chứ không phải cả ứng
dụng.

# Packaging

Để viết một ứng dụng đúng chuẩn, bạn phải theo structure như một library nào đó
của ngôn ngữ đó, hay nói cách khác là bạn có thể install ứng dụng của bạn bằng
package manager của ngôn ngữ đó. Những open source library là nơi bạn có thể
copy structure về áp dụng cho ứng dụng của bạn.

Và khi framework coi chúng là ứng dụng, chúng ta có quyền đổ lỗi cho ngôn ngữ.
Python toolchain rất tệ, requirements.txt, pip, setup.py bị chê rất nhiều, chính
vì thế mà developer không thể structure hoàn chỉnh một ứng dụng, chúng không
được nhắc đến khi bạn bắt đầu học Python. Ngược lại thì Rust chẳng hạn, làm rất
tốt việc này.

Hãy nhìn vào Django. `startproject` tạo ra một ứng dụng không đúng theo chuẩn
packaging của python. Và họ nói rằng `startapp` mới thực sự là application, một
project có thể có nhiều ứng dụng và application là reusable[1] (nhưng việc này rất
hiếm). `manage.py` là CLI entrypoint có kha khá hạn chế cả về code và
deployment.

Tưởng tượng bạn muốn deploy một django app. Bạn cần có git để clone source code
về, hay ít ra là curl - nói chung bạn cần source code. Sau đó phải cd vào đúng
directory chứa `manage.py`, khi đó bạn mới có thể run `gunicorn` hay chạy
`migrate` cho migrations. Nếu bạn cần source code thì sẽ có cơ số thứ không cần
thiết để deploy hay cả những tool như git.

>Docker thì sao? Với cloud infrastructure như bây giờ thì mình thấy đóng gói ứng
dụng vào docker image hơi thừa thải. Có thể dùng terraform để setup một
ec2 instance với đầy đủ os level dependencies. Việc của chúng ta là chạy trực
tiếp ứng dụng trên đó. Docker với mình phù hợp hơn trong việc development để chạy
những service ở local. Tất nhiên bạn có thể không manage infra, thế nên việc
documentation cho ứng dụng của bạn để devops deploy là điều quan trọng không
kém.

Nhưng nếu bạn build ứng dụng theo một library. Sau đó run `pip install
saksa-1.2.0.tar.gz` chẳng hạn, bạn không cần phải cd đến directory nào cả,
module ứng dụng của bạn đã tồn tại trong `sys.path`, bạn chỉ việc `gunicorn
saksa.application`.

Vậy còn `manage.py`? Tất nhiên bạn nên đóng gói ứng dụng của bạn support CLI, có
thể wrap `manage.py` vào module hoặc dùng những thư viện để viết CLI như `click`
và `saksa migrate`.

Đó là những gì mình học được khi nghiên cứu những open source project như
sentry[2], lemur[3], dispatch[4]...

# Configuration

Trước đây mình cũng có đề cập về configuration, nhưng nó vẫn trong phạm vi của
framwork. Sau này khi đã biết về packaging application, mình nhìn lại những ứng
dụng mình build, install trên linux desktop của mình, chúng đều là deployment.
Microsoft phát hành vscode, bạn deploy nó lên máy của mình, nó có thể chạy với
default configuration, và bạn có thể custom chúng, store vào repository trên
github và dùng chung cho nhiều máy khác. Tương tự với linux enviroment của mình,
mình đã có một repo dotfiles dùng chung có cả máy công ty, máy bàn và laptop.

Web application cũng vậy. Dẹp những concept về dev, stag hay production đi. Hãy
coi bạn deploy app của mình nhưng những môi trường đó là những người dùng khác
nhau và tùy biến có chút khác nhau, hạn chế environment check trong code.

[0] https://alexkrupp.typepad.com/sensemaking/2021/06/django-for-startup-founders-a-better-software-architecture-for-saas-startups-and-consumer-apps.html#rule2

[1] https://docs.djangoproject.com/en/4.1/ref/applications/#projects-and-applications

[2] https://github.com/getsentry/sentry

[3] https://github.com/Netflix/lemur

[4] https://github.com/Netflix/dispatch
