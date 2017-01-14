---
layout: post
title:  "Tôi đã kết thúc với Rails"
---
*Bài viết được dịch từ [http://solnic.eu/2016/05/22/my-time-with-rails-is-up.html](http://solnic.eu/2016/05/22/my-time-with-rails-is-up.html)*

---

Năm ngoái tôi đã quyết định mình sẽ không sử dụng Rails nữa, cũng không hỗ trợ những gem cho Rails mà tôi bảo trì. Hơn nữa, tôi sẽ cố gắng hết sức để không phải đụng đến Rails.

<!-- more -->

Từ khi tham gia vào các dự án Ruby, mọi người rất hay hỏi tôi vì sao không thích Rails, những vấn đề tôi hay gặp phải với Rails là gì, vân vân. Vì vậy tôi quyết định viết bài này để giải đáp những câu hỏi đó.

Bài viết này có cả phần kỹ thuật, phần cá nhân, và cũng có phần cường điệu. Tôi không viết nó để gây sự chú ý, tăng lượt truy cập hay gì cả, mà là vì tôi muốn chấm dứt những tranh cãi của tôi về Rails cũng như có lời đáp nếu ai đó tiếp tục hỏi.

Tôi cũng muốn đề cập một vài câu chuyện mà những lập trình viên còn mới với Rails có thể chưa hề biết và làm nổi bật một vài vấn đề đủ quan trọng để suy nghĩ đến.

### Những cái tốt

Tôi sẽ không giả vờ rằng mọi thứ trong Rails đều tệ, sai lầm hay xấu. Như vậy thì không công bằng và thật ngu ngốc. Có nhiều cái tốt khi nói về Rails và tôi sẽ đề cập dưới đây.

Rails làm cho Ruby phổ biến, thật vậy. Trở thành một lập trình viên Ruby và điều đó đã thay đổi sự nghiệp của tôi, và mang đến cho tôi rất nhiều những cơ hội tuyệt vời. Cảm ơn Rails. Nhiều lập trình viên Ruby (Rubyist) ngày nay đã trải qua còn đường đó. Chúng tôi ở đây để cảm ơn Rails. Một số trường hợp Rails còn ảnh hưởng lớn đến cuộc sống của nhiều người, làm chúng tốt đẹp hơn. Mọi người có công việc tốt hơn, những cơ hội rộng mở hơn, kiếm được nhiều tiền hơn. Nó là chìa khóa vàng với đa số trong chúng ta.

Trong nhiều năm, Rails và DHH truyền cảm hứng cho nhiều người, ngay cả những người ở các cộng đồng khác, khiến họ suy nghĩ lại về những điều đang làm. Ví dụ như Rails đã góp phần cải thiện cộng đồng PHP (Symfony framework lấy rất nhiều cảm hứng từ Rails). Tương tự với Java (tất nhiên rồi), Play framework là một ví dụ.

Nếu để những vấn đề về thiết kế kiến trúc sang một bên, việc đó cũng tốt. Việc truyền cảm hứng để nghĩ xa hơn và làm gì đó mới mẻ và có giá trị. Rails đã góp công rất lớn vào quá trình đó.

Còn vài khía cạnh khác mà Rails rất tuyệt vời. Vì Rails luôn tập trung vào tính dễ sử dụng và khả năng xây dựng một ứng dụng web nhanh chóng. Rails Girls thành công là điều có thể đoán trước được. Rails chứng minh cho mọi người thấy rằng ai cũng có thể có khả năng tự tạo ra thứ gì đó mà không cần đến khả năng lập trình, trong khoảng thời gian ngắn. Điều đó thật đáng kinh ngạc, vì nó là cánh cổng đưa mọi người đến thế giới của lập trình dễ dàng mà không phải băn khoăn phải trở thành một lập trình viên.

### Chuyến phiêu lưu của tôi

Trước tiên, hãy để tôi giới thiệu về bản thân mình một chút.

Tôi bắt đầu làm việc với Ruby vào cuối năm 2006, vì luận án cử nhân của tôi là về Rails. Tôi đã học Ruby khi viết luận án. Nó rất vui và hào hứng và có gì đó mới mẻ đối với tôi. Trước đó, công việc của tôi là một lập trình viên PHP. Là một lập trình viên PHP điển hình vào những năm 2005~2006, tôi làm những việc rất cơ bản, viết truy vấn SQL thuần trên view template, sau khi chết trong các thủ tục của PHP, tôi tự viết framework cho mình cũng như ORM (Object-relational mapping), rồi cảm thấy thất vọng và ngao ngán. Mặc dù tôi biết về C, C++, Java và Python nhưng tôi vẫn quyết định chọn Ruby, bởi vì Rails. Tôi đã chọn nó cho đề tài luận án và vô tình thấy thông tin tuyển dụng của một cửa hàng sử dụng Rails. Tôi nộp đơn và họ đã thuê tôi. Đó là vào tháng ba năm 2007.

Kể từ đó, tôi bắt đầu làm việc thực sự với Ruby và từ năm 2009, tôi tham gia vào các dự án mã nguồn mở Ruby (OSS Ruby projects). Tôi đã làm việc liên tục trong 3.5 năm, chủ yếu là các dự án lớn và phức tạp. Rồi tôi trở thành freelancer trong vài năm, làm việc với nhiều loại khách hàng, mở công ty riêng, làm việc full-time, rồi làm freelancer và bây giờ tôi tiếp tục là nhân viên toàn thời gian. Tôi xây dựng các ứng dụng rails độc lập (greenfield) và hỗ trợ các ứng dụng rails từ trung bình đến rất lớn khác.

Để tôi kể với các bạn điều gì có thể xảy ra trong một ứng dụng Rails phức tạp. Hồi đó tôi tham gia vào một dự án đang triển khai. Đó là một ứng dụng mua bán trực tuyến vô cùng lớn. Các sản phẩm, chương trình quản cáo, quy trình sản xuất, người dùng, tin nhắn... Nó có hết. Tôi tham gia vào giúp đỡ viết vài chức năng mới. Một trong số nhiệm vụ của tôi là thêm đường link vào một trang. Tôi mất phải vài ngày mới thêm được đường link ngu ngốc đó. Tại sao ư? Vì ứng dụng là một quả cầu lớn đầy những logic nghiệp vụ rải rác trong các view template. Đến việc tìm đúng template để thêm cái link kia còn phức tạp. Vì tôi cần thêm dữ liệu để tạo đường link đó, tôi không biết phải nên làm thế nào. Đó là do các thiếu sót trong API của ActiveRecord khiến nó trở nên cực kì khó khăn. Tôi không nói đùa đâu.

Nỗi sợ của tôi với Rails bắt đầu. Tôi cảm thấy không hài lòng với ActiveRecord sau khoảng 6 tháng dùng nó. Tôi không hề thích cách Rails xử lý Javascript và AJAX. Nếu bạn không nhớ, hoặc không biết thì trước khi Rails sử dụng UJS (một chủ đề nổi trội vào 2007~2008 với những bài viết, hội thảo... về nó), inline Javascript đã được sử tạo ra bằng hàng tá helper vớ vẩn. Mọi thứ với Rails, lúc đầu sẽ là tuyệt vời và dễ dàng nhưng sau đó sẽ thành một đống khó kiểm soát. Cuối cùng Rails sử dụng UJS từ phiên bản 3.0 và mọi người đồng tình rằng cách này tốt hơn. Đó cũng là thời điểm là Merb được (bị giết) đưa vào Rails. Bạn không biết đến Merb? Được rồi, hãy nói về nó.

### Tại sao tôi lại hào hứng với Merb & DataMapper

Merb là dự án được tạo bởi Ezra Zygmuntowicz. Nó là một kiểu hack giúp upload file nhanh hơn và thread-safe. Nó đã đi từ kiểu hack đó sang một framework full-stack, module hóa, thread-safe và nhanh nhạy. Tôi còn nhớ mọi người còn bàn tán nhiều về nó vào năm 2008 và cảm giác đó thật tuyệt khi có gì đó mới mẻ đang diễn ra.

Bạn hẳn hào hứng khi Rails có "chế độ API" đúng không? Merb có hẳn 3 chế độ: full-stack, API và micro được chia nhỏ ra và tôi vẫn nhớ nó là thứ nhanh nhất từng xảy ra trong thế giới Ruby. Đã 7 năm rồi. Hãy suy nghĩ thử.

Cùng thời điểm đó, một dự án mới nữa là DataMapper. Nó trở thành một phần của The Merb Stack và là ORM của nó. Tôi thực sự hào hứng, vì nó giải quyết những vấn đề mà ActiveRecord mắc phải. DataMapper vào năm 2009 đã có attribute definition trong model, các kiểu dữ liệu tùy chọn và lazy query, truy vấn DSL mạnh mẽ hơn...

Tôi đã rất hứng khởi vì Merb và DataMapper mang lại hi vọng về những điều tốt đẹp, tạo ra sự cạnh tranh lành mạnh với Rails. Tôi đã rất hứng khởi vì cả 2 dự án đều theo kiểu module hóa, thread-safe, các tiêu chuẩn code tốt hơn.

Nhưng rồi cả hai dự án đều bị chôn vùi bởi Rails khi Merb được merge vào Rails, nên Rails 3.0 cải tiến lại rất nhiều. DataMapper mất đi sự chú ý của cộng đồng và không có sự hỗ trợ nào, nó sẽ không lụi tàn nếu Merb không bị đưa vào Rails.

Với quyết định đó, hệ sinh thái Ruby đã mất đi nhiều dự án quan trọng và chỉ có Rails là được hưởng lợi từ điều đó. Việc Merb biến mất là tốt hay xấu tùy vào suy nghĩ của mỗi người, chúng ta đều có thể dự đoán những gì có thể xảy ra nếu không có quyết định đó. Có sự cạnh tranh thì mới lành mạnh. Cạnh tranh giúp đẩy nhanh tiến độ và xuất hiện nhiều phát kiến, tạo ra một hệ sinh thái lành mạnh hơn. Nó cho phép mọi người hợp tác với nhau, chia sẻ những điểm tương đồng, xây dựng nền tảng tốt hơn. Nhưng điều đó lại không xảy đến với cộng đồng Ruby.

Sau khi Merb và DataMapper biến mất, xây dựng thứ gì đó với Ruby trở nên cực kì khó khăn. Vì mọi người tập trung vào Rails, phần lớn dự án bị ảnh hưởng bởi Rails. Những ý tưởng mới đi vào ngõ cụt vì ai cũng muốn thứ bạn tạo ta phải giống Rails và làm việc tốt với Rails. Để thứ đó làm việc được với Rails không hề đơn giản, nhưng tôi sẽ đề cập sau.

Sau chừng ấy năm, toàn bộ một hệ sinh thái chỉ xoay quanh một framework, hàng ngàn nhà phát triển bị ảnh hưởng và tạo ra những tiêu chuẩn với nhiều nghi ngại. Chúng ta mất đi một hệ sinh thái đang bắt đầu phát triển vào năm 2008 và Rails đã cướp mất nó.

Những điều tôi nói ở trên chỉ là sự thật và cũng có phần cá nhân trong đó. Tôi bắt đầu tham gia hỗ trợ DataMapper vào cuối năm 2009 và nhìn nó bị hủy hoại thật sự rất buồn.

### Sự phức tạp

Sự phức tạp là kẻ thù lớn đối với chúng ta. Mọi người mất dần sự hào hứng với Rails, vì nó nhanh chóng trở nên phức tạp và để lại những câu hỏi không có lời giải. Những gì DHH và cộng sự mang lại chưa đủ để giải quyết những vấn đề mà các lập trình viên đương đầu trong năm 2007~2008. Với Merb và DataMapper, mọi người hi vọng sẽ có những cải tiến nhưng với những gì đã xảy ra, chúng ta quay lại sử dụng Rails vào năm 2010, khi phiên bản 3.0 được phát hành.

Vài ngày trước có người đăng trên [/r/ruby](https://www.reddit.com/r/ruby/comments/4ip6dj/organize_your_app_with_service_objects/) đường dẫn đến một bài viết về sử dụng Service Object trong Rails. Có nhiều bài viết như vậy. Nếu bạn nghĩ đó là xu hướng mới thì hãy đọc bài của James Golick vào **tháng 3 năm 2010** - [Crazy, Heretical, and Awesome: The Way I Write Rails Apps](http://jamesgolick.com/2010/3/14/crazy-heretical-and-awesome-the-way-i-write-rails-apps.html).

Chúng ta bàn đến việc cải tiến cấu trúc của Rails trong vòng 6 năm. Tôi cũng bàn luận nhiều về nó trong các bài viết, hội thảo cũng như các dự án mã nguồn mở nhằm giải quyết các vấn đề khó như vậy.

Nhưng những ý tưởng và cuộc tranh luận của mọi người đều bị đội ngũ phát triển Rails đem ra làm trò cười, đặc biệt là DHH. Điều đó làm tôi mất đi động lực hỗ trợ Rails. Tôi chắc chắn rằng những đề xuất của tôi sẽ bị downvote một cách kịch liệt. Monkey-patch? Nào, chúng tôi thích kiểu `10.years.ago` của mình. Lớp trừu tượng mới? Ai cần chứ, Rails đơn giản mà. TDD? [Nó chết rồi](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html), quên chuyện đó đi. ActiveRecord quá cồng kềnh. Sao chứ? Nó rất tiện lợi, hãy [thêm vài chức năng](https://github.com/rails/rails/pull/18910) nữa đi!

Hệ sinh thái Rails, đặc biệt là đội ngũ phát triển, không hề gây ấn tượng tốt với tôi. Và tôi cũng thừa nhận mình cũng ngại đưa ra những đề xuất để thay đổi. Nếu có thì có lẽ tôi nên yêu cầu "Làm ơn hãy xóa ActiveSupport đi" (haha, hãy tưởng tượng mà xem).

Được rồi, hãy đi sâu vào kĩ thuật chút nữa.

### Thiết kế theo hướng thuận tiện của Rails

Như tôi đã nói, Rails xây dựng dựa trên tính dễ sử dụng. Đừng nhầm lẫn nó với sự đơn giản.

![tweat](https://pbs.twimg.com/media/CizxHyHUUAAZkIL.jpg:medium)

Đó là cách Rails hoạt động, một ví dụ đơn giản:

```ruby
User.create(params[:user])
```

Bạn thấy một dòng code đơn giản và bạn có thể nói ngay nó làm gì. Vấn đề ở đây là mọi người nhầm tưởng giữa đơn giản và thuận tiện. Nó thuận tiện khi viết trên controller và thế là xong, đúng không?

Nhưng dòng code đó không hề đơn giản, nó dễ viết nhưng rất phức tạp ở bên trong vì:

- param phải chuyển đổi kiểu dữ liệu phù hợp với db
- param phải được kiểm tra tính hợp lệ
- param có thể thay đổi qua callback, hệ thống bên ngoài cũng gây ra hiệu ứng phụ
- dữ liệu không đúng sẽ có thông báo lỗi, và nó phụ thuộc vào hệ thống bên ngoài (như là i18n)
- param hợp lệ sẽ được đặt cho trạng thái của đối tượng, cũng có thể cho các đối tượng có mối quan hệ tới nó nữa
- một đối tượng đơn lẻ hay toàn bộ tập hợp đối tượng quan hệ với nó phải được lưu trong database

Nếu không phân chia những thứ quan trọng ra sẽ dễ dẫn đến sự phức tạp với mọi dự án. Nó gia tăng mối liên kết và gây trở ngại khi thay đổi cũng như mở rộng.

Nhưng với Rails, đó không phải vấn đề. Với nó, nhưng quy tắc thiết kế căn bản như SRP (và SOLID nói chung) bị chế nhạo và được xem là cồng kềnh, không cần thiết vì có thể gây ra sự phức tạp. Nếu bạn muốn định nghĩa thêm đối tượng để chia nhỏ phần phức tạp, Rails leader sẽ nói bạn YAGNI (you aren't gonna need it). Nếu bạn muốn sử dụng [composition](https://en.wikipedia.org/wiki/Object_composition) để code minh bạch hơn, Rails leader (trừ [tenderlove](https://twitter.com/tenderlove/status/734437704893505536)) sẽ bảo bạn dùng `ActiveSupport::Concerns`

Với đội ngũ phát triển Rails, chẳng có vấn đề gì khi dữ liệu được chuyển thẳng từ web form sâu xuống ActiveRecord, nơi mà có trời mới biết điều gì sẽ diễn ra.

Phần thử thách thực sự trong cuộc thảo luận này có thể sẽ giải thích nó là vấn đề hàng đầu. Mọi người bị lôi cuốn bởi Rails vì nó khiến bạn nghĩ sai về sự đơn giản, trong khi những gì diễn ra là sự phức tạp được che đậy dưới bề mặt thuận tiện. Bề mặt đó dựa trên nhiều sự lầm tưởng rằng bạn sẽ xây dựng và thiết kế ứng dụng của mình. ActiveRecord chỉ là một ví dụ tiêu biểu, nhưng Rails được xây dựng dựa trên nguyên lý đó rồi, từng phần của Rails đều hoạt động như vậy.

Tôi cũng muốn nói là có nhiều nỗ lực giúp ActiveRecord trở nên tốt hơn, như là Attributes API. Nhưng không may, miễn là nó còn có hơn 200 phương thức public, và khuyến khích sử dụng callback cũng như concern thì nó sẽ luôn là một ORM không thể giải quyết được sự phức tạp có thể gia tăng, nó chỉ có thể làm nó phức tạp hơn và mọi chuyện tệ hơn.

Điều đó sẽ thay đổi trong Rails không? Tôi nghĩ là không. Tôi không có bằng chứng nào cho thấy sự cải tiến từ đội ngũ phát triển. Một minh chứng đơn giản là một tính năng gây tranh cãi gần đây, `ActiveRecord.suppress` được [DHH đề xuất](https://github.com/rails/rails/issues/18847). Anh ta một lần nữa lấy standalone Ruby class ra làm trò hề khi nói "Đúng, bạn có thể làm tương tự bằng cách tạo một factory CreateCommentWithNotificationsFactory". Ôi trời.

### ActiveCoupling

Rails có nên là ứng dụng của bạn không? Nhiều người sẽ tự thắc mắc sau khi xem [bài phát biểu của Uncle Bob](https://www.youtube.com/watch?v=WpkDN78P884), ông đã đề xuất một sự phân tách rõ ràng giữa phần web và phần ứng dụng. Tạm thời bỏ qua khía cạnh kĩ thuật, đó là lời khuyên tốt, nhưng Rails không được thiết kế như vậy. Nếu bạn làm vậy, bạn sẽ bỏ lỡ toàn bộ quan điểm của framework này.

Rails là ứng dụng của bạn, luôn luôn là vậy, trừ khi bạn không nỗ lực sử dụng nó theo cách không phải của nó.

Hãy xem qua những điều sau:

- **ActiveRecord là trung tâm của xử lý toàn bộ logic.** Đó là lý do vì sao có có hàng tá các giao diện và chức năng. Bạn chỉ chia nhỏ mọi thứ và tách phần logic sang các thành phần riêng lẽ khi nó có nghĩa, nhưng nguyên lý của Rails là đưa mọi thứ vào ActiveRecord, không quan tâm đến SRP, đến LoD, đến mối liên kết chặc chẽ giữa logic và thời gian tồn tại của đối tượng... Đó là cách bạn sử dụng Rails hiệu quả.
- Toàn bộ lớp view trong Rails liên kết với ActiveRecord, do đó nó liên kết với ActiceRecord ORM.
- Controller, hay là web API endpoint, phần quan trọng của Rails, cũng có một liên kết chặc chẽ ở đây.
- Helper, giúp bạn xử lý những template phức tạp trong Rails,
- Mọi thứ trong Rails, và trong phần lớn các gem được viết cho Rails, là thông qua kế thừa (cả mixin hay class). Rails và gem của nó không cung cấp đối tượng riêng lẽ, tái sử dụng được, mà cung cấp các lớp trừu tượng để đối tượng của bạn kế thừa, đó lại là một dạng liên kết chặc chẽ nữa.

Nếu hiểu như vậy thì thật điên rồ mà nói Rails không phải là ứng dụng của bạn. Nếu bạn cố tránh những liên kết kia, bạn có thể tưởng tượng ra rằng bạn sẽ bỏ lỡ bao nhiêu nỗ lực cũng như các tính năng xây dựng sẵn - và đó chính xác là lý do những phương pháp thay thế khác trong Rails tạo ra những mớ hỗn độn, không cần thiết làm mọi người nghĩ đến kỉ nguyên đáng sợ của Java. Rails không được xây dựng trên liên kết lỏng cũng như hướng thành phần từ đầu.

Đừng kháng cự. Hãy chấp nhận nó.

### Không phải là công dân tốt

Phải nói là cái gai trong mắt tôi là ActiveSupport, vì tôi đã [nói về nó](http://solnic.eu/2015/06/06/cutting-corners-or-why-rails-may-kill-ruby.html) nên không cần phải nhắc lại nữa. Tôi cũng khuyên nên đọc phần bình luận của bài viết.

Vì ActiceSupport, tôi cho rằng Rails không phải là một công dân tốt trong hệ sinh thái Ruby. Thư viện này là tất cả những gì sai trái của Rails đối với tôi. Không có giải pháp cụ thể, không có lớp trừu tượng tường minh, chỉ là những cách tạm bợ để giải quyết nhanh chóng vấn đề, những cách tạm bợ trở thành API chính thức và kết quả sẽ là mớ tạp nham. Kinh khủng.

Rails là một hệ sinh thái đóng và nó bắt những dự án khác đi theo con đường sai trái của nó. Nếu bạn muốn tạo ra thứ gì đó làm việc được với Rails, bạn phải quan tâm đến việc nó hoạt động được với ActiveSupport hay nó có thể hoạt động với việc tự động tải lại code ở chế độ development hay đối tượng được cung cấp toàn cục vì bạn biết đấy, trong Rails, mọi thứ phải tồn tại, cho thuận tiện.

Cách Rails hoạt động đòi hỏi nhiều nỗ lực từ developer để xây dựng gem của mình. Trước tiên gem của bạn phải hoạt động với Rails (vì rõ ràng ai rồi cũng sẽ dùng nó với Rails) và điều đó gây ra khó khăn. Bạn có một thư viện làm việc với cơ sở dữ liệu và muốn nó làm việc với Rails? Vậy thì bạn phải làm nó giống như ActiveRecord, vì giao diện tương tác là ActiveModel, căn bản dựa trên ActiveRecord từ trước phiên bản 3.0

Có nhiều thứ ràng buộc khiến mọi việc khó khăn khi cung cấp khả năng tương tác với Rails.

Bạn không biết rằng có nhiều vấn đề phải đối mặt khi code tự động tải lại ở chế độ development. Rails muốn một môi trường hoạt động toàn cục, có thể thay đổi. Để tăng độ khó, họ còn giới thiệu thêm Spring. Gem này gây ra những lỗi không ngờ tới với gem của bạn. Tôi đã kết thúc với nó. Không chỉ việc tải lại code không đáng tin cậy mà nó còn tạo ra sự phức tạp không đáng có cho gem và ứng dụng của chúng ta. Không ai trong đội ngũ của Rails suy nghĩ đến giải pháp tốt hơn. Nếu ai đó muốn code tải nhanh hơn, chúng ta có thể dựa vào việc khởi động lại tiến trình. Bên cạnh đó bạn nên dùng test tự động để xem những thay đổi có thực sự hoạt động không, thay vì nhấn F5.

Tôi biết nó nghe có vẻ như mình đang phàn nàn, mà đúng là vậy. Tôi đã cố hỗ trợ Rails và nó đã làm tôi thấy khó chịu. Tôi đã từ bỏ, tôi không muốn làm nữa.

Vì giải pháp của tôi là không dùng ActiveSupport, gỡ bỏ ActiveRecord và thêm một lớp view thực sự mà không liên kết với bất kì ORM nào, tôi nhận ra điều đó là không thể xảy ra với Rails.
