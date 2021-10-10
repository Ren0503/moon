---
layout: post
title: Hiểu Sâu Về API
date:   2021-09-28
excerpt: API là gì - định nghĩa, kiểu, thông số và các khái niệm khác.
tags: [api]
comments: true
---

Nếu bạn đã từng đọc qua các tài liệu công nghệ hay docs hướng dẫn, ít nhiều bạn đều một lần thấy từ viết tắt API. Nghe thật hay, nhưng mà nó có nghĩa là gì và liệu ta có cần hiểu nó ?

Lấy ví dụ trong giao tiếp giữa người với người. Chúng ta có thể bày tỏ suy nghĩ, nhu cầu và ý tưởng của mình thông qua ngôn ngữ (viết và nói), cử chỉ hoặc nét mặt. Nếu tương tác với máy tính, ứng dụng và trang web thì có thể thông qua các thành phần giao diện người dùng - màn hình có menu, các phần tử đồ hoạ, bàn phím và chuột.

Vậy các phần mềm không cần giao diện người dùng đồ hoạ làm thế nào để giao tiếp với nhau?

Các sản phẩm phần mềm trao đổi dữ liệu và chức năng với nhau thông qua một giao diện mà máy móc đọc được gọi là - *API (giao diện lập trình ứng dụng)*.

# API là gì

**API** là một bộ mã lập trình cho phép truyền dữ liệu giữa một phần mềm này với một phần mềm khác. Nó cũng chứa các điều khoản qui định về việc trao đổi dữ liệu đấy.

![api1](https://github.com/Ren0503/moon/blob/master/assets/img/api/api1.png)

API bao gồm hai thành phần:

- **Đặc tả kỹ thuật** mô tả các tùy chọn trao đổi dữ liệu giữa các giải pháp với đặc điểm kỹ thuật được thực hiện dưới dạng yêu cầu xử lý và giao thức cung cấp dữ liệu.

- **Giao diện phần mềm** được viết theo thông số kỹ thuật đại diện cho nó.

Phần mềm cần truy cập thông tin (ví dụ: giá phòng khách sạn X cho những ngày nhất định) hoặc chức năng (tức là tuyến đường từ điểm A đến điểm B trên bản đồ dựa trên vị trí của người dùng) từ một phần mềm khác, sẽ gọi API của nó đồng thời các chỉ định yêu cầu về dữ liệu/chức năng phải được cung cấp. Phần mềm khác trả về dữ liệu/chức năng do ứng dụng trước yêu cầu.

Và giao diện mà hai ứng dụng này giao tiếp là những gì API chỉ định.

Các chuyên gia của [Red Hat](https://www.redhat.com/en/topics/api/what-are-application-programming-interfaces) lưu ý rằng API đôi khi được coi là hợp đồng, trong đó tài liệu là thỏa thuận giữa các bên: *"Nếu bên thứ nhất gửi một yêu cầu từ xa có cấu trúc theo một cách cụ thể, thì đây là cách phần mềm của bên thứ hai sẽ phản hồi."* Tài liệu API là sổ tay dành cho các dev bao gồm tất cả thông tin cần thiết về cách làm việc với API và sử dụng các dịch vụ mà nó cung cấp. Chúng tôi sẽ nói thêm về tài liệu trong những phần tiếp theo.

Mỗi API chứa và được thực hiện bởi các lệnh **gọi hàm** - các câu lệnh yêu cầu phần mềm thực hiện các hành động và dịch vụ cụ thể. Lời gọi hàm là các cụm từ bao gồm động từ và danh từ, ví dụ:

- Bắt đầu hoặc kết thúc một phiên
- Nhận tiện nghi cho một loại phòng đơn
- Khôi phục hoặc truy xuất các đối tượng từ máy chủ.

Các lệnh gọi hàm được mô tả trong tài liệu API.

API phục vụ nhiều mục đích. Nói chung, chúng có thể đơn giản hóa và tăng tốc độ phát triển phần mềm. Các dev có thể thêm chức năng (tức là công cụ giới thiệu, đặt chỗ ở, nhận dạng hình ảnh, xử lý thanh toán) từ các nhà cung cấp khác vào các giải pháp hiện có hoặc xây dựng các ứng dụng mới bằng cách sử dụng dịch vụ của từ nhà cung cấp bên thứ ba. Trong tất cả những trường hợp này, các chuyên gia không phải xử lý mã nguồn, hay phải hiểu cách giải pháp hoạt động. Họ chỉ cần kết nối phần mềm của họ với một phần mềm khác. Nói cách khác, API đóng vai trò như một lớp trừu tượng giữa hai hệ thống, che giấu sự phức tạp và chi tiết hoạt động của hệ thống.

# Kiểu API

## Theo tính khả dụng - chính sách phát hành

Về chính sách phát hành, API có 3 loại là : private, partner và public

![api2](https://github.com/Ren0503/moon/blob/master/assets/img/api/api2.png)

**API riêng tư**. Các giao diện phần mềm ứng dụng này được thiết kế để cải thiện các giải pháp và dịch vụ trong một tổ chức. Các nhà phát triển hoặc nhà thầu nội bộ có thể sử dụng các API này để tích hợp các hệ thống hoặc ứng dụng CNTT của công ty, xây dựng các hệ thống mới hoặc các ứng dụng hướng tới khách hàng tận dụng các hệ thống hiện có. Ngay cả khi các ứng dụng công khai, bản thân giao diện vẫn chỉ có sẵn cho những người làm việc trực tiếp với nhà phát hành API. Chiến lược riêng tư cho phép một công ty kiểm soát hoàn toàn việc sử dụng API.

**API đối tác**. API đối tác được quảng bá công khai nhưng được chia sẻ với các đối tác kinh doanh đã ký thỏa thuận với nhà phát hành. Trường hợp sử dụng phổ biến cho các API đối tác là tích hợp phần mềm giữa hai bên. Một công ty cấp cho đối tác quyền truy cập vào dữ liệu hoặc khả năng được hưởng lợi từ các luồng doanh thu bổ sung. Đồng thời, nó có thể theo dõi cách sử dụng tài sản kỹ thuật số, đảm bảo liệu các giải pháp của bên thứ ba sử dụng API của họ có cung cấp trải nghiệm người dùng tốt hay không và duy trì danh tính công ty trong ứng dụng của họ.

**API công khai**. Còn được gọi là developer-facing, các API này có sẵn cho bất kỳ nhà phát triển bên thứ ba nào. Chương trình API công khai cho phép nâng cao nhận thức về thương hiệu và nhận thêm nguồn thu nhập khi được thực hiện đúng cách.

Có hai loại API công khai - **mở (miễn phí)** và **trả phí**. Một [API mở](https://github.com/okffi/open-api-definition/blob/master/en/open-api-definition.md) có nghĩa rằng tất cả các tính năng của một API như vậy là công khai và có thể được sử dụng mà không có các điều khoản và điều kiện hạn chế. Ví dụ: có thể xây dựng một ứng dụng sử dụng API mà không cần sự chấp thuận rõ ràng từ nhà cung cấp API hoặc phí cấp phép bắt buộc. Định nghĩa cũng nêu rõ rằng mô tả API và bất kỳ tài liệu liên quan nào phải có sẵn công khai và API có thể được sử dụng tự do để tạo và thử nghiệm các ứng dụng.

Người dùng API trả phí đăng ký hoặc sử dụng API trên cơ sở *pay-as-you-go* (dùng đến đâu thanh toán đến đó). Một cách tiếp cận phổ biến giữa các nhà phát hành là cung cấp bản dùng thử miễn phí để người dùng có thể đánh giá các API trước khi mua. Tìm hiểu thêm về cách các doanh nghiệp hưởng lợi từ việc mở API của họ để sử dụng công khai trong bài viết chi tiết của chúng tôi về [API economy](https://www.altexsoft.com/blog/api-economy/).

## Theo cách sử dụng 

API có thể được phân loại theo hệ thống mà chúng được thiết kế.

**Database API**. Các API cơ sở dữ liệu cho phép giao tiếp giữa ứng dụng và hệ thống quản lý cơ sở dữ liệu. Các nhà phát triển làm việc với cơ sở dữ liệu bằng cách viết các truy vấn để truy cập dữ liệu, thay đổi bảng, v.v. Ví dụ: API cơ sở dữ liệu Drupal 7 cho phép người dùng viết các truy vấn hợp nhất cho các cơ sở dữ liệu khác nhau, cả độc quyền và mã nguồn mở (Oracle, MongoDB, PostgreSQL, MySQL, CouchDB và MSSQL).

Một ví dụ khác là ORDS Database API, được nhúng vào Dịch vụ dữ liệu Oracle REST.

**Operating System API**. Nhóm API này xác định cách ứng dụng sử dụng tài nguyên và dịch vụ của hệ điều hành. Mỗi hệ điều hành đều có bộ API của nó, chẳng hạn như API Windows hoặc API Linux ([kernel-user space api](https://www.kernel.org/doc/html/v4.15/userspace-api/index.html) và [kernel internal api](https://www.kernel.org/doc/html/latest/driver-api/media/index.html)).

Apple cung cấp tham chiếu API cho macOS và iOS trong tài liệu dành cho các dev của họ. Các API để xây dựng ứng dụng cho hệ điều hành máy tính để bàn macOS của Apple được bao gồm trong bộ công cụ dành cho nhà phát triển Cocoa. Những ứng dụng xây dựng cho hệ điều hành di động iOS sử dụng Cocoa Touch - một phiên bản sửa đổi của Cocoa.

**Remotes API**. Các API từ xa xác định các tiêu chuẩn tương tác cho các ứng dụng chạy trên các máy khác nhau. Nói cách khác, một sản phẩm phần mềm truy cập các tài nguyên nằm bên ngoài thiết bị yêu cầu chúng. Vì hai ứng dụng được định vị từ xa được kết nối qua mạng truyền thông, đặc biệt là internet, nên hầu hết các API từ xa đều được viết dựa trên các tiêu chuẩn web. [Java Database Connectivity API](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/)  và [Java Remote Method Invocation API](https://docs.oracle.com/javase/8/docs/technotes/guides/rmi/index.html) là hai ví dụ về API từ xa.

**Web API**. Kiểu API này là phổ biến nhất. Web API cung cấp dữ liệu có thể đọc được bằng máy và truyền chức năng giữa các hệ thống dựa trên web đại diện cho [kiến trúc client-server](https://www.techopedia.com/definition/438/clientserver-architecture). Các API này chủ yếu gửi yêu cầu từ các ứng dụng web và phản hồi từ các máy chủ sử dụng Giao thức truyền tải siêu văn bản (HTTP).

Các nhà phát triển có thể sử dụng các web API để mở rộng chức năng của các ứng dụng hoặc trang web của họ. Ví dụ: [Pinterest API](https://developers.pinterest.com) đi kèm với các công cụ để thêm dữ liệu Pinterest của người dùng như bảng hoặc Ghim vào trang web. [Google Maps API](https://developers.google.com/maps/) cho phép thêm bản đồ với vị trí của tổ chức.

Hầu hết các doanh nghiệp sử dụng nhiều hơn một API để kết nối các ứng dụng và chia sẻ thông tin. Một số cuối cùng cần một công cụ quản lý API để giúp họ kiểm soát, phân phối và phân tích các API khác nhau. Tìm hiểu thêm về quản lý API trong bài viết [này](https://www.altexsoft.com/blog/api-management/).

# Giao thức API

Mục tiêu của các đặc tả API là chuẩn hóa việc trao đổi dữ liệu giữa các dịch vụ web. Trong trường hợp này, chuẩn hóa có nghĩa là khả năng của các hệ thống đa dạng, được viết bằng các ngôn ngữ lập trình khác nhau hoặc chạy trên các hệ điều hành khác nhau hoặc sử dụng các công nghệ khác nhau, có thể giao tiếp liền mạch với nhau.

[![Comparing](https://img.youtube.com/vi/NFw0HznpLlM/0.jpg)](https://www.youtube.com/watch?v=NFw0HznpLlM){: .mx-auto.d-block :}

## Remote Procedure Call (RPC)

Các web API có thể tuân thủ các nguyên tắc trao đổi tài nguyên dựa trên Remote Procedure Call. Giao thức này chỉ định sự tương tác giữa các ứng dụng dựa trên client-server. Một chương trình (client) yêu cầu dữ liệu hoặc chức năng từ một chương trình (server) khác, nằm trong một máy tính khác trên mạng và máy chủ sẽ gửi phản hồi cần thiết.

![rpc](https://github.com/Ren0503/moon/blob/master/assets/img/api/rpc.png)

RPC còn được gọi là chương trình con hoặc lệnh gọi hàm. Một trong hai cách để thực hiện Remote Procedure Call là SOAP.

## Service Object Access Protocol (SOAP)

SOAP là một giao thức nhẹ để trao đổi thông tin có cấu trúc trong một môi trường phân tán, phi tập trung, theo định nghĩa của [Microsoft](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wusp/5daaa9d9-26aa-42fc-a431-c011166dc58f) đã phát triển nó. Nói chung, đặc tả này chứa các quy tắc cú pháp cho các thông báo yêu cầu và phản hồi được gửi bởi các ứng dụng web. Các API tuân thủ các nguyên tắc của SOAP cho phép trao đổi XML giữa các hệ thống thông qua HTTP hoặc Giao thức truyền thư đơn giản (SMTP) để gửi.

Ngôn ngữ đánh dấu mở rộng ([XML](https://www.w3.org/XML/)) là một định dạng văn bản đơn giản và rất linh hoạt được sử dụng rộng rãi để lưu trữ và trao đổi dữ liệu qua internet hoặc các mạng khác. XML xác định một tập hợp các quy tắc để mã hóa tài liệu ở định dạng mà cả người và máy đều có thể đọc được. Ngôn ngữ đánh dấu là một tập hợp các ký hiệu có thể được đặt trong văn bản để phân định và gắn nhãn các phần của tài liệu văn bản. Các tài liệu văn bản XML chứa các thẻ tự mô tả của các đối tượng dữ liệu, điều này làm cho chúng dễ đọc.

![xml](https://github.com/Ren0503/moon/blob/master/assets/img/api/xml.png)

SOAP hầu hết được sử dụng với phần mềm dựa trên web doanh nghiệp để đảm bảo tính bảo mật cao cho dữ liệu được truyền. API SOAP được ưu tiên trong số các nhà cung cấp cổng thanh toán, quản lý danh tính và giải pháp CRM, cũng như các dịch vụ tài chính và viễn thông. API công khai của [PayPal](https://developer.paypal.com/docs/nvp-soap-api/PayPalSOAPAPIArchitecture/) là một trong những SOAP API thường được biết đến. Nó cũng thường được sử dụng để hỗ trợ hệ thống cũ.

## Representational State Transfer (REST)

Thuật ngữ REST được giới thiệu bởi nhà khoa học máy tính Roy Fielding trong một [luận văn](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) năm 2000. Không giống như SOAP, là một giao thức, REST là một kiểu kiến ​​trúc phần mềm với [sáu ràng buộc](https://www.geeksforgeeks.org/rest-api-architectural-constraints/) để xây dựng các ứng dụng hoạt động qua HTTP, thường là các dịch vụ web. World Wide Web là ứng dụng phổ biến nhất của phong cách kiến ​​trúc này.

[REST](https://www.altexsoft.com/blog/rest-api-design/) được coi là một giải pháp thay thế đơn giản hơn cho SOAP, mà nhiều nhà phát triển cảm thấy [khó sử dụng](https://smartbear.com/blog/test-and-monitor/understanding-soap-and-rest-basics/) vì nó yêu cầu viết rất nhiều mã để hoàn thành mọi tác vụ và tuân theo cấu trúc XML cho mọi thông báo được gửi đi. REST tuân theo một logic khác vì nó cung cấp dữ liệu dưới dạng tài nguyên. Mỗi tài nguyên được đại diện bởi một URL duy nhất và người ta có thể yêu cầu tài nguyên này bằng cách cung cấp URL của nó.

Các web API tuân thủ các ràng buộc kiến ​​trúc REST được gọi là RESTful API. Các API này sử dụng các yêu cầu HTTP (phương thức hoặc động từ) để làm việc với các tài nguyên: GET, PUT, HEAD, POST, PATCH, CONNECT, TRACE, OPTIONS và DELETE.

Hệ thống RESTful hỗ trợ trao đổi ở các định dạng khác nhau, chẳng hạn như văn bản thuần túy, HTML, YAML, XML và JSON, trong khi SOAP chỉ cho phép XML. Khả năng hỗ trợ nhiều định dạng để lưu trữ và trao đổi dữ liệu là một trong những lý do khiến REST là một lựa chọn phổ biến để xây dựng các API công khai ngày nay.

Những gã khổng lồ về truyền thông xã hội và các công ty du lịch cung cấp các API bên ngoài để thương hiệu của họ được hiển thị nhiều hơn nữa. [Twitter](https://developer.twitter.com/en/docs) có nhiều RESTful API; [Expedia](https://expediapartnersolutions.com/products) có cả SOAP API và RESTful cho các đối tác của mình. Nếu bạn cân nhắc việc xác định lại dịch vụ kinh doanh du lịch và khách sạn của mình, hãy đọc bài viết [API đặt chỗ và du lịch](https://www.altexsoft.com/blog/engineering/travel-and-booking-apis-for-online-travel-and-tourism-service-providers/).

JavaScript Object Notation ([JSON](https://www.altexsoft.com/blog/engineering/travel-and-booking-apis-for-online-travel-and-tourism-service-providers/)) là một định dạng văn bản nhẹ và dễ phân tích cú pháp để trao đổi dữ liệu. Cú pháp của nó dựa trên một tập hợp con của [Standard ECMA-262 3rd Edition](https://www.ecma-international.org/wp-content/uploads/ECMA-262_3rd_edition_december_1999.pdf). Mỗi tệp JSON chứa các tập hợp các cặp *name/value* và danh sách các giá trị có thứ tự. Vì đây là những cấu trúc dữ liệu phổ quát, nên định dạng này có thể được sử dụng với bất kỳ ngôn ngữ lập trình nào.

![rest](https://github.com/Ren0503/moon/blob/master/assets/img/api/rest.png)

JSON đã được áp dụng rộng rãi nhờ vào sự phổ biến của REST.

## gRPC

gRPC là một framework API mã nguồn mở cũng được phân loại theo RPC. Không giống như SOAP, gRPC mới hơn nhiều và được Google phát hành công khai vào năm 2015. Với gRPC, ứng dụng khách có thể gọi trực tiếp các phương thức từ một ứng dụng máy chủ nằm trên một máy tính khác như thể nó là một đối tượng cục bộ. Điều này giúp tạo các dịch vụ và ứng dụng phân tán dễ dàng hơn.

Giống như SOAP và REST, phương thức truyền tải cho gRPC là HTTP. Tuy nhiên, tương tự như RCP, gRPC cho phép các nhà phát triển xác định bất kỳ loại lệnh gọi hàm nào, thay vì chọn từ các tùy chọn được xác định trước như PUT và GET trong trường hợp REST.

Theo mặc định, gRPC sử dụng [bộ đệm giao thức](https://developers.google.com/protocol-buffers/docs/overview) thay vì JSON hoặc XML làm Ngôn ngữ Định nghĩa Giao diện (IDL) để tuần tự hóa dữ liệu có cấu trúc. Ở đây, trước tiên nhà phát triển cần xác định cấu trúc của dữ liệu mà họ muốn tuần tự hóa. Khi cấu trúc dữ liệu đã được chỉ định, chúng sử dụng trình biên dịch bộ đệm giao thức để tạo các lớp truy cập dữ liệu bằng ngôn ngữ lập trình mà bạn chọn. Dữ liệu sau đó được nén và tuần tự hóa ở định dạng nhị phân trong thời gian chạy. Tìm hiểu thêm về gRPC trong bài viết [chi tiết](https://www.altexsoft.com/blog/what-is-grpc/).

![grpc](https://github.com/Ren0503/moon/blob/master/assets/img/api/grpc.png)

gRPC chủ yếu được sử dụng để giao tiếp giữa các microservices vì nó có sẵn bằng nhiều ngôn ngữ và có hiệu suất cao.

## GraphQL

Nhu cầu phát triển tính năng nhanh hơn, tải dữ liệu hiệu quả hơn do việc sử dụng thiết bị di động ngày càng tăng và vô số khách hàng, đã khiến các nhà phát triển tìm kiếm các cách tiếp cận khác đối với kiến trúc phần mềm. GraphQL, ban đầu được Facebook tạo ra vào năm 2012 để sử dụng nội bộ, nưng sau đó nó đã được xem như là một REST mới với các tổ chức như Shopify, Yelp, GitHub, Coursera và The New York Times sử dụng nó để xây dựng API.

GraphQL là một ngôn ngữ truy vấn cho các API. Nó cho phép khách hàng chi tiết hóa dữ liệu chính xác mà nó cần và đơn giản hóa việc tổng hợp dữ liệu từ nhiều nguồn, vì vậy nhà phát triển có thể sử dụng một lệnh gọi API để yêu cầu tất cả dữ liệu cần thiết. Một tính năng đặc biệt khác của GraphQL là nó sử dụng *type system* để mô tả dữ liệu.

![graphql](https://github.com/Ren0503/moon/blob/master/assets/img/api/graphql.png)

Việc sử dụng *type* để mô tả dữ liệu cho phép ứng dụng chỉ định dữ liệu nào họ cần lấy

Các ứng dụng sử dụng GraphQL kiểm soát dữ liệu nào chúng cần tìm nạp từ máy chủ, cho phép chúng chạy nhanh ngay cả khi kết nối di động chậm. Bạn có thể xem GraphQL, REST, RPC và SOAP được so sánh như thế nào trong bài viết [này](https://www.altexsoft.com/blog/soap-vs-rest-vs-graphql-vs-rpc/).

# Tài liệu API

Cho dù có bao nhiêu cơ hội để tạo hoặc mở rộng sản phẩm phần mềm, API sẽ vẫn là một đoạn mã không sử dụng được nếu các nhà phát triển không hiểu cách làm việc với nó. Tài liệu API có cấu trúc và được viết tốt, giải thích cách sử dụng hiệu quả và tích hợp API một cách dễ hiểu sẽ khiến nhà phát triển hài lòng và mong muốn giới thiệu API cho các đồng nghiệp.

Tài liệu API là sổ tay tham khảo với tất cả thông tin cần thiết về API, bao gồm các hàm, lớp, kiểu trả về và đối số.

Nhiều yếu tố nội dung tạo nên tài liệu tốt, chẳng hạn như:
- hướng dẫn bắt đầu nhanh
- thông tin xác thực
- giải thích cho mọi lệnh gọi API (yêu cầu)
- ví dụ về mọi yêu cầu và trả về với mô tả phản hồi, thông báo lỗi, v.v.
- code mẫu cho các ngôn ngữ lập trình phổ biến như Python, Java, JavaScript hoặc PHP;
- hướng dẫn
- Ví dụ về SDK (nếu có sẵn SDK) minh họa cách truy cập tài nguyên, v.v.

Tài liệu có thể *static* và *interactive*. Loại interactive cho phép thử các API và xem kết quả trả về và thường bao gồm hai cột: human và machine. Cột human chứa mô tả API và machine có bảng điều khiển để thực hiện request và chứa thông tin mà máy khách và máy chủ sẽ quan tâm khi kiểm tra API.

![test](https://github.com/Ren0503/moon/blob/master/assets/img/api/test.jpeg)

Generation là quá trình tài liệu hóa các API của các nhà phát triển và người viết kỹ thuật. Các chuyên gia có thể sử dụng các giải pháp tài liệu API (tức là các công cụ Swagger, Postman, Slate hoặc ReDoc) để thống nhất cấu trúc và thiết kế tài liệu.

# Ví dụ về API

Dưới đây là một số ví dụ về các API nổi tiếng sử dụng các giao thức và thông số kỹ thuật khác nhau. Xem tài liệu của họ để có thêm thông tin và tài liệu tham khảo.

**Google Maps API**. Không có gì lạ khi Google, một trong những gã khổng lồ công nghệ, đã đặt ra các tiêu chuẩn trong cách các công ty khác hoạt động. Hầu hết các trang web có bản đồ tích hợp đang sử dụng [Google Maps API](https://developers.google.com/maps/documentation). Ví dụ: API chỉ đường của Google sử dụng yêu cầu HTTP để trả lại chỉ đường có định dạng XML hoặc JSON giữa các vị trí địa lý.

**Vulkan**. Vulkan là một API đa nền tảng hoạt động ở cấp hệ điều hành. Nó cho phép các nhà phát triển tạo đồ họa thời gian thực, chất lượng cao trong các ứng dụng và thúc đẩy giao tiếp giữa ứng dụng và đơn vị xử lý đồ họa. Xem thêm tài liệu [Vulkan API](https://devdocs.io/vulkan/) nếu bạn quan tâm.

**Tìm kiếm chuyến bay Skyscanner**. Skyscanner là một nền tảng metasearch cho phép khách du lịch tìm kiếm các chuyến bay với mức giá tốt nhất từ ​​cơ sở dữ liệu về giá của Skyscanner. Ngoài ra, Skyscanner cung cấp cho các đối tác liên kết của mình RESTful API hỗ trợ cả XML và JSON làm định dạng trao đổi dữ liệu. Để cải thiện tính bảo mật, họ khuyến khích các đối tác chỉ sử dụng giao thức HTTPS để đưa ra yêu cầu. Bạn có thể xem tài liệu của họ [ở đây](https://skyscanner.github.io/slate/#api-documentation).

**WeatherAPI**. Đây là nhà cung cấp thông tin thời tiết và vị trí địa lý miễn phí với rất nhiều API khác nhau, từ dự báo thời tiết, tra cứu IP, thể thao, thiên văn học, vị trí địa lý và múi giờ. Nó cung cấp quyền truy cập vào dữ liệu địa lý và thời tiết bằng cách sử dụng RESTful API JSON / XML. Các nhà phát triển có thể sử dụng HTTP hoặc HTTPS để yêu cầu API. Họ cung cấp cho các nhà phát triển [tài liệu chi tiết](https://www.weatherapi.com/docs/) về cách sử dụng tất cả các API của họ.

**Tính khả dụng của Sabre Air**. Đây là một SOAP API, được [Sabre](https://developer.sabre.com/home) sử dụng để tìm kiếm các chuyến bay và thông tin về tình trạng tương ứng cho các ngày, điểm xuất phát và điểm đến nhất định. Vì nó là một SOAP API, nên nó sử dụng XML làm định dạng trao đổi dữ liệu và các giao thức HTTP hoặc HTTPS cho các yêu cầu.

**Yelp API**. Đây là một [GraphQL API](https://www.yelp.com/developers/graphql/guides/intro) cung cấp cho người dùng các đề xuất và đánh giá về các nhà hàng tốt nhất, hoạt động giải trí, cuộc sống về đêm và hơn thế nữa. Nó sử dụng phương thức yêu cầu HTTP để truy cập dữ liệu từ các máy chủ. API kết nối với các nguồn dữ liệu thông qua các endpoint mà các nhà phát triển có thể thêm vào ứng dụng của họ. Nó sử dụng JSON làm định dạng trao đổi dữ liệu

# Tổng kết

Như vậy là kết thúc bài viết về API, hy vọng nó sẽ có ích cho những ai đang cần.

## Tham khảo

[altexsoft](https://www.altexsoft.com/blog/engineering/what-is-api-definition-types-specifications-documentation/)
