---
layout: post
title: "Making a dream Vietnamese input method for Linux"
---

Cũng đã vài năm trôi qua từ ngày tôi bắt đầu thực hiện hóa ước mơ bộ gõ
tiếng Việt trên Linux.
Nhớ lại những ngày ấy với niềm háo hức còn trào dâng rồi dần dà vơi đi khi mà
việc nắm bắt kiến trúc bộ gõ trên Linux còn rất nhiều thử thách.

Là một người dùng Linux khá lâu nhưng cái cảm giác ngờ ngợ khi lần đầu cài đặt
và sử dụng ibus-unikey trên Ubuntu rồi nhìn thấy dấu gạch chân (preedit) xuất
hiện là một trải nghiệm không thể quên.
Hồi đó qua phim ảnh cũng biết được khi gõ tiếng Hàn, Nhật hay Trung đều có dấu
gạch chân này nhưng tại sao gõ tiếng Việt lại dùng nó.

Và cũng rất nhiều người có câu hỏi tương tự. Một [blog post](https://lewtds.github.io/2014/07/31/uoc-mo-bo-go-kieu-unikey/) khai sáng cho tôi
giải thích rất chi tiết về lịch sử, kiến trúc, cách hoạt động cũng như giới hạn
của bộ gõ trên Linux. Bài viết rất hay và tôi vẫn thường mở ra đọc
sau khi cau mày gõ một nội dung trên trình duyệt chờ nhấn space cho
suggestion/history hiện lên để còn truy cập nhanh vào đường link mình muốn.

<!-- more -->

Không chỉ dừng lại ở ibus-unikey, tôi từng thử fcitx, nimf hay ibus-bamboo.
Nhưng những vấn đề xưa cũ vẫn ở đó, tất cả hoạt động không đồng bộ,
có app gõ được có app không và implementation chính vẫn là preedit.

![](/images/posts/2026-05-29-making-a-dream-linux-input-method/nimf.png)

Đây là lần sử dụng nimf và báo cáo lỗi cho bộ gõ này. Repo bây giờ đã bị xóa
(close source) và chỉ còn lại những email thông báo reply từ tác giả. Quan điểm
của họ về dbus có lý khi nó bị đánh giá là phức tạp và cồng kềnh trong các
ticket của sway hay trên các diễn đàn về linux.

Khi wayland dần có chỗ đứng, đặc biệt là sự ra đời của sway--drop-in i3
replacement, tôi đã chuyển qua dùng nó. Ước mơ bộ gõ tiếng Việt trên Linux lại
quay về thôi thúc tôi viết một cái cho riêng mình.

Hay chăm đọc ticket của sway mà tôi phát hiện ra một bộ gõ tiếng Nhật [anthywl](https://github.com/tadeokondrak/anthywl)
đơn giản vừa đủ để có thể học hỏi và bắt chước làm theo.

Tôi có trao đổi với tác giả về vài vấn đề của bộ gõ tiếng Việt. Bây giờ nhận ra
mình chưa có đủ kiến thức để đặt câu hỏi. Và phải quay về sử dụng preedit.

![](/images/posts/2026-05-29-making-a-dream-linux-input-method/surrounding-text.png)

Hồi đó surrounding text vẫn là thứ gì đó xa xỉ để implement ở phía ứng dụng.

![](/images/posts/2026-05-29-making-a-dream-linux-input-method/protocol-bug.png)

Báo cáo một vấn đề về commit text khi di chuyển cursor trong preedit với
tiếng Việt. Cái này được ông ấy đưa vào thảo luận trong ticket của
wayland-protocols. Tôi có vào bình luận giải thích thêm và để lại cả
link bài blog khai sáng đó.

@dcz là một trong những contributor chính của text input/input method protocol
đã xác nhận vấn đề, ổng đưa ra bản vá và gần đây đã được [merge](https://gitlab.freedesktop.org/wayland/wayland-protocols/-/merge_requests/282) vào version mới
nhất của text-input protocol.

[Daklak](https://github.com/mozartilize/daklak) bản viết bằng C copy architecture từ anthywl và tôi chủ yếu code phần
engine xử lý tiếng Việt, và chỉ mỗi chế độ gõ Telex. Sau rồi nhận ra phần code
pha trộn giữa input method và engine nên cũng đành bỏ dở.

Một ngày tháng 5 oi bức, sau khi cau mày gõ tiếng Việt với preedit (fcitx5)
trên kde, tôi không còn mở blog post đó ra đọc nữa. Thay vào đó tôi mở claude.ai
và nói nó lên plan viết một bộ gõ tiếng Việt bằng Rust cho Linux. Và phần mở đầu
của nó làm tôi kinh ngạc.

>bắt đầu xây dựng bộ gõ tiếng Việt cho wayland, đặc biệt là kde và sway, hoạt
>động như google gboard, không dùng preedit -- có thể học hỏi từ [kime](https://github.com/Riey/kime) để hỗ trợ
>nhiều môi trường desktop

>Surrounding text đã được hỗ trợ tốt trên nhiều ứng dụng và toolkits. Async
>ordering risk của forward key được giải quyết vì các sự kiện zwp_text_input_v3
>được serialize trong một wl_display flush, không còn race condition như X11.

Ước mơ về bộ gõ tiếng Việt trên Linux giờ đây có thể thực hiện được. Phần lớn
các ứng dụng cũng như GUI toolkit GTK-3/GTK-4/QT6, chromium, firefox đều đã
implement text-input-v3 protocol nên đảm bảo surrounding text hoạt động được và
đã phổ biến.

Ngay cả khi forward key không hoạt động được, tôi cũng đã yêu cầu Claude lên
plan sử dụng cách mà ydotool làm để inject keystroke.

Với việc đi theo con đường của kime, có nghĩa là [daklak](https://github.com/mozartilize/daklak-rs) không phải chỉ là một
input method của hai framework ibus hoặc fcitx. Tôi có thể tự do thiết
kế nó hoạt động theo cách mình muốn và quan trọng nhất là bỏ đi nhiều thành phần
của các bộ gõ CJK mà bộ gõ tiếng Việt không cần đến.

Để kiểm tra lại plan có gì sai sót, tôi đưa nó cho ChatGPT. Nó chỉ ra plan này
không có phần proof of concept hay probe -- có nghĩa là cần phải
xác minh thử là những gì chúng ta muốn/sẽ làm có thực sự chạy được không. Và nó
đã đúng, AIs được train với dữ liệu cũ, có những thứ đã lạc hậu, hoặc không
chính xác, việc này giúp đảm bảo khi code xong là xài được, không phải code ra
hoàn chỉnh rồi phát hiện những kiến thức, suy luận trong plan là sai.

Những cái sai đầu tiên:

- sway không hỗ trợ forward key trong input-method-v2, phải dùng qua
  virtual-keyboard-v1
- kwin không implement input-method-v2, thay vào đó là input-method-v1
- forward backspace như cách của ydotool không hề đơn giản, nó ở 2 tầng kiến
  trúc khác nhau, hoạt động được nhưng không tin cậy.

Và AI giúp tăng tốc độ kiểm chứng rất nhanh, nó có thể viết những script trong
tích tắc và tôi chỉ cần chạy chúng rồi gửi lại kết quả cho nó phân tích. Nếu một
ý tưởng không được, nó chỉ cần thử ý tưởng khác. Nhờ có AI mà một trong những
khâu trong phát triển phần mềm là vòng phản hồi (feedback iteration) tăng năng
suất rõ rệt.

Rust được chọn vì AIs làm việc tốt nhất với nó. Một ngôn ngữ static type, memory
safe và có compiler mạnh có thể bắt được nhiều bug trước khi bạn chạy chương
trình. Thông báo lỗi của compiler cũng là một nguồn thông tin hữu ích với AIs.
Nếu biên dịch thành công, một tính năng đã hoàn thành 95%.

Một điều phấn khích khi dùng AIs là tôi có thể hỏi mọi câu hỏi ngu ngốc ngay từ
đầu để tìm hiểu thêm về cái mình đang làm. Nó không quan tâm là tôi có hiểu hay
là đã hỏi nó chưa, tôi chỉ việc hỏi nếu lần trước đó tôi đã quên câu trả lời
hoặc vẫn chưa hiểu.

AIs phân tích log một cách nhẹ nhàng. Tôi chỉ cần cung cấp các bước để tái hiện
bug, cung cấp log và nó có thể chỉ ra lỗi và fix chỉ trong tích tắc. Vấn đề của
bộ gõ là log chứa những hành động nhấn nhả phím xảy ra trong vòng mili giây, nếu
có quá nhiều tao tác bộ não con người có thể sẽ quá tải khi xử lý những thông
tin như vậy trong thời gian ngắn. Khi bug quá khó, nó sẽ đoán và nếu quá 3 lần
không thể fix được root cause, tôi phải nghĩ cách khác. Ví dụ liên quan đến ứng
dụng (chromium) và cách các compositor implement protocol, tôi phải cung cấp
thêm log từ ứng dụng, pull code của compositor (sway, wlroots, kwin) để nó có
thể check. Bug phải được tái hiện ít bước nhất có thể để log không quá dài và
chứa những phần không cần thiết. Tôi phải giữ log trong khoảng 500 đến 800 dòng
và phải xóa bớt các log không liên quan đi.

AIs làm việc quá tốt và quá nhanh khiến nhiều code cũng như các quyết định kỹ
thuật tôi vẫn chưa nắm hết. Tính năng chính của [daklak](https://github.com/mozartilize/daklak-rs) đã hoàn thành, lúc này là
lúc tôi dừng lại để học codebase--cùng với AIs.
