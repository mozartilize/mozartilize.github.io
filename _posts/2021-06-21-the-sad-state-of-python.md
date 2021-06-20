---
layout: post
title: "The sad state of Python"
---

Mình may mắn vào nghề khi Python 3 ra được khá lâu và đa số các thư viện lớn đã support, tuy nhiên đến tận đầu năm nay thì Python mới chính thức ngừng hỗ trợ Python 2. Do có quá nhiều "breaking changes" nên việc chuyển đổi sang Python 3 tốn quá nhiều công sức và thời gian của các nhà phát triển trên thế giới. Và một lần nữa hồi chuông đó lại gióng lên trên khắp các cộng đồng Python khi async/await được hỗ trợ từ phiên bản 3.5.

Concurrency trên Python được thực hiện bằng thread. Tất nhiên process cũng làm được nhưng sẽ tốn nhiều resource hơn. Nói chung nếu là I/O bound task thì dùng thread, còn CPU bound sẽ dùng process. Chính vì vậy định nghĩa concurrency và parrellism giống nhau nhưng về mặc kĩ thuật lại hoàn toàn khác. Và thread trong Python chính là OS thread, cộng với sự hạn chế của "the GIL" nên context switch - chuyển đổi qua lại giữa các thread, khá tốn resource. Việc ra đời của async/await giải quyết được vấn đề này.

Tuy nhiên để support async/await thì bạn phải viết code mới hoàn toàn. Cùng một vấn đề nhưng bây giờ chúng ta cần 2 thư viện khác nhau. Dễ nhận ra nhất là sự ra đời của hàng loạt các asyncio web framework như aiohttp, sanic, fastapi... Song song với đó vẫn có những developer tìm ra cách sáng tạo để không phải viết lại toàn bộ code của họ mà vẫn có thể support async/await như sqlalchemy. Mike Bayer đã tận dụng thư viện greenlet để internal code của sqlalchemy có thể gọi đến các async driver - theo mình biết thì hiện tại đang support asyncpg, aiomysql; và viết thêm một tầng async code cho engine, connection mà không phải tốn quá nhiều công sức. Flask 2.0 đã support asyncio nhưng vẫn chậm hơn so với thread, vì task executor được implement theo kiểu workaround bằng cách [sử dụng duy nhất 1 asyncio thread](https://github.com/pallets/flask/pull/3412#issuecomment-548772028).

Api design của Asyncio trong Python được cho là không ổn định và chưa đủ tốt. Developer dễ dàng viết code
không có tính scalable nếu họ không hiểu sâu về backpressure và flow control
như trong [bài viết](https://lucumr.pocoo.org/2020/1/1/async-pressure/) mà author của Flask đề cập. Ngoài ra Trio là một thư viện asyncio giúp cho bạn viết concurrency code theo structure. Nghe có vẻ tốt nhưng Trio vẫn chưa được sử dụng rộng rãi. Uvicorn, một asyncio web server vẫn chưa support Trio. Thư viện này vẫn gặp phải vấn đề về performance khi nó vẫn chậm hơn asyncio.

Python asyncio có được sử dụng rộng rãi trong tương lai chắc phải đợi kha khá
thời gian nữa mới biết câu trả lời. Vậy trước đó chúng ta có giải pháp gì? Đó là
greenlet, nó được nhắc đến ở trên như một cứu cánh cho sqlalchemy.

Greenlet là một coroutine library cho phép xây dựng custom scheduler phân chia
task là những coroutine đó trên chính ngôn ngữ lập trình chứ không dựa vào OS,
tương tự như goroutine, JVM, Erlang VM... Gevent, Eventlet... là những scheduler
xây dựng trên greenlet được sử dụng rộng rãi cho đến bây giờ. Bài viết này sẽ
tập trung vào gevent.

Với gevent bạn đơn giản chỉ cần viết code bình thường, sau đó đặt hàm monkey
patch khi chương trình khởi động là xong. Thư viện requests hoàn toàn tương
thích với gevent theo cách như vậy mà không cần phải thêm "specific code" nào cả.

Tuy nhiên, các thư viện được viết bằng C thực hiện blocking IO bound không thể
patch bằng gevent được. Chúng ta phải thay thế bằng các thư viện được viết bằng
pure Python và có khi chúng không phổ biến bằng.

Thật sự với một dự án start nhưng không có mindset về concurrency, việc đưa
gevent vào sẽ rất đắn đo không biết sẽ phát sinh bug ngoài ý muốn không. Nhiều
developer vẫn xem monkey patching của gevent quá ảo diệu và họ không tin tưởng
nó hoạt động giống với std lib.

Gevent vẫn có nhiều success story và được các công ty lớn sử dụng. Nó vẫn sẽ có chỗ đứng trong cộng động Python về lâu dài. Lyft vừa đây có [một serie blog](https://eng.lyft.com/what-the-heck-is-gevent-4e87db98a8) về cách họ áp dụng gevent cho một microservice. Với lượng request lớn đã gây ra độ trễ cao từ service đó.
Nó không hoàn toàn là lỗi của gevent, mà do gunicorn không sử dụng gevent đúng
cách gây nên back pressure trên một worker.

Qua đây chúng ta thấy có nhiều cách để viết concurrency code bằng Python, tuy
nhiên việc phân mảnh hoàn toàn không tốt cho ecosystem. Chất lượng các lib giảm,
tốc độ phổ biến chậm... Mình vẫn chưa có cơ hội thử hết tất cả những gì được đề
cập trong bài viết này, nhưng vẫn theo dõi tiến độ để xem nên chọn phương án nào
tối ưu nhất cho dự án tiếp theo.

Cũng có ý kiến tại sao không sử dụng Golang hay một ngôn ngữ nào đó support
concurrency tốt hơn? Thật ra ngôn ngữ nào cũng có điểm mạnh điểm yếu, và nếu bạn
code với ngôn ngữ đó đủ lâu chúng ta sẽ lại phân vân về [green vs brown
programming languages](https://earthly.dev/blog/brown-green-language/).
