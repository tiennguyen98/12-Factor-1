# 12-factor

## Giới thiệu
Trong thời buổi hiện đại ngày nay, phần mềm thường được cung cấp dưới dạng dịch vụ và được gọi chung là các ứng dụng web hoặc "Phần mềm như một dịch vụ". Phần mềm tuân theo "12 quy chuẩn" là một phương pháp luận để xây dựng các ứng dụng dạng "Phần mềm như một dịch vụ" mà thỏa mãn các yếu tố sau:

- Sử dụng các định dạng được khai báo để phục vụ cho việc tự động hóa cài đặt. Giảm thiểu tối đa thời gian và chi phí để các nhà phát triển khác tham gia dự án;
- Có các quy ước rõ ràng với hệ điều hành bên dưới, tối ưu hóa tính di dộng giữa các môi trường thực thi.
- Phù hợp để triển khai trên các nền tảng đám mây hiện đại, giảm bớt yêu cầu từ server và các hệ thống quản trị.
- Giảm thiểu tối đa sự khác biệt giữa giai đoạn phát triển và thực thi, tạo điều kiện cho việc phát triển liên tục một cách nhanh nhất.
- Và có thể mở rộng mà không cần những thay đổi đáng kể liên quan tới công cụ, kiến trúc hoặc phong cách/thói quen phát triển.

Phương pháp luận "12 quy chuẩn" có thể áp dụng cho các ứng dụng viết bằng bất kỳ ngôn ngữ lập trình nào và sử dụng bất kỳ các dịch vụ nền nào (database, queue, memory cache, v.v).

## Cơ sở của bài viết
Các tác giả đóng góp cho tài liệu này đã trực tiếp tham gia vào các quá trình phát triển và triển khai của hàng trăm ứng dụng. Họ cũng gián tiếp chứng kiến quá trình phát triển, vận hành và mở rộng của hàng trăm nghìn ứng dụng thông qua nền tảng Heroku.

Tài liệu này tổng hợp tất cả các kinh nghiệm và quan sát của chúng tôi trên rất nhiều ứng dụng dạng "phần mềm như một dịch vụ". Nó bao gồm 3 yếu tố quan trọng mà bất kỳ quy trình phát triển ứng dụng lý tưởng nào cũng cần có. Nó bao gồm việc tập trung vào sự phát triển linh động của một ứng dụng theo thời gian, tính linh động trong việc cộng tác giữa các nhà phát triển trên nền code của ứng dụng, và tránh các chi phí phát sinh tạo ra bởi sự hao mòn của phần mềm.

Mục đích của chúng tôi là nâng cao nhận thức về những vấn đề có tính hệ thống mà chúng tôi đã chứng kiến trong việc phát triển các ứng dụng hiện đại. Ngoài ra, chúng tôi cũng mong muốn cung cấp các từ vựng chung để sử dụng trong việc bàn luận về những vấn đề này, và để mang đến một tập các giải pháp rộng lớn có tính khái niệm cho các vấn đề với các thuật ngữ tương ứng. Định dạng của tài liệu này được lấy cảm hứng từ cuốn sách của Martin Fowler, "Patterns of Enterprise Application Architecture and Refactoring".

## Ai nên đọc tài liệu này?
Bất kỳ nhà phát triển nào đang xây dựng các ứng dụng mà chạy như một dịch vụ. Các kĩ sư Ops triển khai hoặc quản lý các ứng dụng tương tự.

#### I. Nền code
###### Một nền code cần được theo dõi trong các hệ thống quản lý sửa đổi và sẽ có nhiều bản triển khai .

Một ứng dụng tuân theo "12 quy chuẩn" luôn cần được theo dõi trong một hệ thống quản lý sửa đổi như Git, Mercurial, hoặc Subversion. Một bản sao xét duyệt của cơ sở dữ liệu theo dõi được gọi là một code repository, thường được viết tắt là code repo hoặc repo.

Một nền code có thể là bất cứ repo đơn lẻ nào (trong một hệ thống quản lý sửa đổi phân quyền tập trung như Subversion), hoặc có thể là một tập bất kỳ các repo chia sẻ cùng một commit gốc (trong một hệ thống quản lý sửa đổi phân quyền phân cấp như Git). 

Luôn luôn có sự tương quan một-một giữa nền code và ứng dụng:
- Nếu có nhiều nền code thì đó không phải là một ứng dụng - nó là một hệ thống phân cấp. Mỗi thành phần là một hệ thống được phân cấp trong một ứng dụng và mỗi thành phần có thể tuân theo "12 quy chuẩn" một cách riêng biệt.
- Nhiều ứng dụng chia sẻ cùng đoạn code là một sự vi phạm trong "12 quy chuẩn". Giải pháp ở đây là quản lý các đoạn code được dùng chung thành các thư viện mà ta có thể sử dụng thông qua dependency manager.

Chỉ có duy nhất một nền code cho mỗi ứng dụng, nhưng sẽ có nhiều bản triển khải của ứng dụng. Một bản triển khai là một thực thể đang chạy của ứng dụng. Đây thường trang trong môi trường thực thi (production) và một hoặc nhiều phía trang staging. Ngoài ra, mỗi nhà phát triển có một bản sao của ứng dụng đang chạy trên môi trường phát triển nội bộ của họ, mỗi bản sao đó cũng đủ tiêu chuẩn để có thể triển khai thực tế.

Nền code là chung giữa tất cả các triển khai, mặc dù các phiên bản khác nhau có thể hoạt động hoặc không hoạt động trong mỗi triển khai. Ví dụ, một nhà phát triển có vài commit chưa triển khai đến staging, staging có vài commit chưa được triển khai tới production. Nhưng chúng đều dùng chung nền codebase, điều này làm chúng được phân biệt là các triển khai khác nhau của cùng một ứng dụng

#### II. Các phụ thuộc
###### Khai báo rõ ràng và tách biệt các phụ thuộc

Hầu hết các ngôn ngữ lập trình đều cung cấp một hệ thống đóng gói cho các thư viện có hỗ trợ việc phân phối như CPAN cho Perl hoặc Rubygems cho Ruby. Các thư viện được cài đặt thông qua hệ thống packaging có thể được cài đặt ở mức hệ thống ( được gọi là các “site package”) hoặc chỉ thu hẹp phạm vi trong thư mục chứa ứng dụng ( được gọi là “vendoring” hoặc “bundling”).

Một ứng dụng tuân theo "12 quy chuẩn" không bao giờ phụ thuộc vào sự tồn tại ngầm của các package ở mức hệ thống. Nó sẽ khai báo tất cả các phụ thuộc một hoàn thiện và chính xác thông qua một bản khai báo phụ thuộc. Ngoài ra, nó sử dụng một công cụ cô lập các phụ thuộc trong quá trình thực thi để đảm bảo rằng không có phụ thuộc ngầm nào “lọt vào”  từ các hệ thống xung quanh. Đặc tả phụ thuộc đầy đủ và chi tiết được áp dụng một cách thống nhất trong cả môi trường thực thi và phát triển.

Ví dụ, Bundler cho Ruby cung cấp định dạng biểu thị `Gemfile` cho việc khai báo các phụ thuộc và `bundle exec` cho việc cô lập phụ thuộc. Trong Python có 2 công cụ riêng biệt cho các bước này - Pip được sử dụng để khai báo và Virtualenv để cô lập. Thâm chí C có Autoconf dùng cho việc khai báo phụ thuộc và liên kết tĩnh có thể mang lại sự cô lập hóa các phụ thuộc. Bất kể tập công cụ nào được sử dụng, khai báo và cô lập phụ thuộc luôn phải được sử dụng cùng nhau - chỉ một là không đủ để thỏa mãn bộ 12 quy chuẩn".

Một lợi ích của việc khai báo phụ thuộc rõ ràng là nó đơn giản hóa công việc cài đặt cho những người phát triển mới làm quen với ứng dụng. Những nhà phát triển mới có thể lấy nền code của ứng dụng về máy của họ để phát triển, với điều kiện tiên quyết là ngôn ngữ runtime và trình quản lý phụ thuộc đã được cài đặt trước đó. Họ sẽ có thể cài đặt bất cứ thứ gì cần thiết để chạy code của ứng dụng với một lệnh build mà đã được xác định từ trước. Ví dụ, lệnh build cho Ruby/Bundler là `bundle install`, còn đối với Clojureojure/Leiningen là `lein dép`.

Các ứng dụng tuân theo bộ "12 quy chuẩn" cũng không phụ thuộc vào sự tồn tại ngầm của bất kỳ công cụ hệ thống nào. Ví dụ như công cụ hệ thống ImageMagick hoặc curl. Trong khi các công cụ này có thể tồn tại trên nhiều hay thậm chí là hầu hết các hệ thống, không có gì bảo đảm rằng chúng sẽ tồn tại trên tất cả các hệ thống mà ứng dụng có thể chạy trên trong tương lai, hoặc liệu một phiên bản được tìm thấy trong hệ thống tương lai có thể tương thích với ứng dụng hay không. Nếu ứng dụng cần một công cụ hệ thống, công cụ đó cần được gán vào ứng dụng.

#### III. Cấu hình
###### Lưu cấu hình trong môi trường

Cấu hình một ứng dụng là bất cứ thứ gì tạo ra sự khác biệt nằm trong các giai đoạn triển khai (môi trường staging, môi trường thực thi, môi trường nhà phát triển, v.v). Nó bao gồm:

- Tài nguyên xử lý cơ sở dữ liệu, Memcaches, và các dịch vụ hỗ trợ khác
- Các xác thực cho các dịch vụ bên ngoài như Amazon S3 hoặc Twitter
- Các giá trị của mỗi triển khai như là tên máy chủ của triển khai

Các ứng dụng đôi khi lưu trữ các cấu hình như các hằng trong mã nguồn. Điều này là một vi phạm trong bộ "12 quy chuẩn". Bộ quy chuẩn yêu cầu sự phân chia rõ ràng giữa cấu hình và mã nguồn. Về cơ bản, cấu hình thay đổi giữa các triển khai còn code thì không.

Một test để kiểm tra xem liệu ứng dụng đã có tất cả các cấu hình mà được tách biệt với code được cài đặt chính xác hay chưa là xác định xem liệu nền code có thể trở thành mã nguồn mở bất cứ lúc nào mà không ảnh hưởng đến bất cứ thông tin xác thực nào hay không.

Lưu ý rằng định nghĩa `cấu hình` không bao gồm các cấu hình nội bộ của ứng dụng, ví dụ như là `config/routes.rb` trong Rails, hoặc cách các module được kết nối trong Spring. Loại cấu hình này không thay đổi giữa các triển khai, và ví thế chúng là tốt nhất trong code.

Một cách tiếp cận khác với cấu hình là sử dụng các file cấu hình mà không được kiểm soát trong hệ thống quản lý sửa đổi như `config/database.yml` trong Rails. Đây là một cải thiện lớn so với việc sử dụng các hằng số được sử dụng trong code repo, tuy nhiên chúng vẫn còn những nhược điểm: việc thêm một file config vào repo là việc rất dễ xảy ra, đôi khi dễ xảy ra việc các file cấu hình nằm rải rác ở những chỗ khác nhau và có những định dạng khác nhau. Điều này tạo ra khó khăn để thấy và kiểm soát tất cả các cấu hình trong cùng một chỗ. Hơn nữa những định dạng này có thường sẽ khác biệt đối với từng ngôn ngữ hay framework.

Ứng dụng tuân theo bộ "12 quy chuẩn" lưu trữ cấu trình trong các biến môi trường (thường đọc tắt là env vars hoặc env). Env vars có thể dễ dàng thay đổi giữa các triển khai mà không phải thay đổi code; không giống như các file cấu hình, khả năng chúng được thêm vào code repo một cách vô tình là rất thấp; và không giống như các file cấu hình thông thường, các cơ chế cấu hình khác như Java System Properties, chúng là các chuẩn ngôn ngữ và OS-agnostic.

Một khía cạnh khác của quản lý cấu hình là gom nhóm. Đôi khi các ứng dụng gom cấu hình thành các tên nhóm (thường gọi là “các môi trường”) và được đặt tên dựa trên các triển khai cụ thể như môi trường `development`, `test`, và `production` trong Rails. Phương pháp này sẽ không được gọn khi mở rộng: vì khi có nhiều triển khai của ứng dụng được tạo ra, các tên môi trường mới cũng sẽ cần được tạo như `staging` hoặc `qa`. Khi dự án sẽ càng phát triển hơn nữa, các nhà phát triển có thể sẽ thêm các môi trường đặc biệt riêng của họ như `joes-staging`, điều này tạo ra một kết hợp các cấu hình khổng lồ làm cho việc quản lý các triển khai của ứng dụng trở nên vô cùng dễ khó khăn và dễ xảy ra lỗi.

Trong các ứng dụng tuân theo bộ "12 quy chuẩn", env vars đóng vai trò là các điều khiển, mỗi env vars liên kết đầy đủ với các env vars khác. Chúng không vào giờ được nhóm lại với nhau như là các "môi trường", nhưng thay vào đó chúng quản lý riêng biệt cho từng triển khai. Đây là một mô hình tạo nên sự mượt mà khi nâng cấp hay mở rộng vì ứng dụng tự mở rộng thành các triển khai khác trong vòng đời của nó.