---
layout: post
title: Bên Trong Node.js (P1) - JavaScript Engine
date:   2021-10-05
excerpt: Hiểu hơn về cách hoạt động của JavaScript Engine.
tags: [javascript, engine, node.js]
comments: true
---

Kể từ khi ra đời vào năm 2009, Node.js đã đem đến một cuộc cách mạng thực sự, thay đổi hoàn toàn tầm vóc của Javascript. Ứng dụng node.js trải dài từ backend cho ứng dụng, xây dựng hệ thống thời gian thực đến cả kết nối vạn vật IoT. Không chỉ hoạt động trên được nhiều hệ điều hành, node.js còn giúp tiết kiệm được thời gian và đơn giản hoá công việc. Vậy node.js là gì mà thần thánh thế, để hiểu về nodejs là gì ta cần phải hiểu về cái thứ bên trong node.js trước hết là javascript engine.

# JavaScript Engine

![engine](/assets/img/javascript/engine.png)

JavaScript Engine là một chương trình máy tính thực thi các đoạn code JavaScript (JS).

JavaScript Engine đọc các đoạn mã JavaScript rồi chuyển nó sang mã máy để máy tính (hoặc phần mềm máy tính như trình duyệt web, server node.js…) có thể hiểu và chạy được.

Nếu bạn đã lập trình với Java hay C/C++ thì có thể hiểu JavaScript tương đương với JDK trong Java hay trình Compiler C/C++ trong lập trình C/C++.

## Ví dụ

Có nhiều bản JavaScript Engine được phát triển bởi các các vendor (nhà cung cấp) khác nhau. Một số JavaScript Engine phổ biến như:

- [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey): phiên bản Javascript engine đầu tiên, được dùng trên trình duyệt web đầu tiên trên thế giới(Netscape Navigator), hiện tại đang được sử dụng trên Firefox, viết bằng C và C++.
- [Chakra](https://en.wikipedia.org/wiki/Chakra_(JavaScript_engine)): là một Javascript engine cũng khá lâu đời, ban đầu được sử dụng trên Internet Explorer và biên dịch JScript, nay được dùng cho Microsoft Edge, viết bằng C++.
- [Rhino](https://en.wikipedia.org/wiki/Rhino_(JavaScript_engine)): một Engine viết hoàn toàn bằng Java, cũng có lịch sử phát triển lâu đời từ Netscape Navigator, hiện tại được phát triển bởi Mozilla Foundation.
- [Google V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)): ddược phát triển bới Google (Chromium Project).
Ngoài các JS Engine trên, còn rất nhiều JavaScript Engine khác như Carakan, JavaScriptCore, Tamarin, Nashorn…

## Cơ chế vận hành

Bài viết này sẽ nói dựa trên V8 engine và các trình duyệt dựa thuộc Chrome.

### Load

![engine1](/assets/img/javascript/engine1.gif)

Trình phân tích cú pháp HTML sẽ tìm thẻ tag là `script` với một source. Code từ source này sẽ được load từ **network**, **cache** hoặc từ một **service worker** đã cài trước đó. Response trả về sẽ là một đoạn script dưới dạng 1 **luồng byte**, phần này sẽ do byte stream decoder xử lý. **Byte stream decoder** sẽ decode chuỗi byte này ra khi nó được down về.

### Decode

![engine2](/assets/img/javascript/engine2.gif)

Byte stream decoder sẽ khởi tạo các mã **token** từ luồng byte đã được decode. Ví dụ, `0066` decode thành `f`, `0075` thành `u`, `006e` thành `n`, `0063` thành `c`, `0074` thành `t`, `0069` thành `i`, `006f` thành `o`, `006e` thành `n` với khoảng trắng. Đây là keyword dành riêng cho JavaScript, mã token được tạo và gửi đến parser (và cả các *pre-parser* nữa, tôi không có gif để mô tả kỹ hơn nhưng tôi sẽ giải thích thêm bên dưới). Và đối với phần còn lại của byte stream cũng sẽ diễn ra tương tự.

### Node

![engine3](/assets/img/javascript/engine3.gif)

Engine sử dụng 2 parser: **pre-parser** và **parser**. Pre-parser có nhiệm vụ kiểm tra lỗi cú pháp từ các mã token. Từ đó làm giảm thiểu được không ít thời gian trong việc tìm lỗi trong code, dù đương nhiên sẽ có trường hợp sau đó parser vẫn phát hiện thêm lỗi. 

Trường hợp nếu không có lỗi, parser sẽ tạo ra các node được dựa trên mã token nó nhận được từ byte stream decoder. Với các node này, nó tạo ra Abstract Syntax Tree (AST).

### Interpreter 

![engine4](/assets/img/javascript/engine4.gif)

Tiếp theo là đến công đoạn của **interpreter** (trình phiên dịch). Interpreter đi qua AST và tạo byte code dựa trên thông tin AST chứa. Sau khi hoàn thành công đoạn tạo **byte code**, AST sẽ bị xóa để giải phóng bộ nhớ. Cuối cùng chúng ta đã có “nguyên liệu” có thể làm việc máy.

### Byte code & feedback type

![engine5](/assets/img/javascript/engine5.gif)

Mặc dù byte code đã khá nhanh nhưng vẫn có thể tối ưu hóa tốc độ hơn nữa. Thông tin được tạo ra khi byte code hoạt động. Nó có thể phát hiện những hành vi nào thường diễn ra, và loại data được sử dụng. Có thể bạn thường lặp đi lặp lại một chức năng, thì đây cũng là lúc tối ưu hóa để đẩy nhanh tốc độ.

Byte code cùng với feedback type đã được tạo sẽ được gửi đến **optimizing compiler** (compiler tối ưu hoá). Optimizing compiler lấy byte code và type feedback để tạo nên machine code cực kỳ tối ưu hóa.

JavaScript là ngôn ngữ dạng dynamic typing, vì các loại data luôn thay đổi. Nếu JavaScript engine phải kiểm tra liên tục giá trị của mỗi loại data thì sẽ rất chậm. 

Thay vào đó engine có thể sử dụng bộ nhớ đệm nội tuyến (inline caching), có chức năng lưu trữ code trong bộ nhớ và hy vọng trả về cùng một giá trị với cùng một behavior trong tương lai. Giả sử một hàm nhất định được sử dụng 100 lần và luôn trả về cùng một giá trị, thì lần thứ 101 sử dụng nó cũng sẽ trả đúng về giá trị này.

Ví dụ như có hàm chức năng sum như sau, luôn được gọi với giá trị numerical (số) dưới dạng đối số (argument) mỗi lần:

```javascript
function sum(a, b) {
    return a+b;
}

sum(1, 2)
```

Kết quả sẽ trả về số 3! Lần tới, khi chúng ta nhập lại hàm này, nó sẽ giả định chúng ta lại nhập hai giá trị numerical.

Nếu đúng, thì nó sẽ yêu cầu thêm dynamic lookup, và chỉ có thể sử dụng kết quả được lưu trong bộ nhớ đã lưu trước đó. Nếu không phải, nó sẽ de-optimize (đảo ngược tối ưu hóa) code và trả lại về byte code ban đầu thay vì machine code đã được tối ưu hóa.

Ví dụ lần sau khi đã nhập, tôi sẽ truyền một string (chuỗi) thay vì một number (số). Vì JavaScript là dynamical type nên sẽ không xảy ra bất kỳ lỗi nào.

```javascript
function sum(a, b) {
    return a+b;
}

sum('1', 2)
```

Điều này nghĩa là số `2` sẽ bị ép thành một chuỗi và thay vào đó hàm sẽ trả về chuỗi `12`. Nó sẽ quay lại xử lý byte code đã nhập và cập nhật thêm type feedback.

# Tổng kết

Bài viết trên đây đã giới thiệu sơ qua về javascript engine và cách nó hoạt động, các bài viết tiếp theo sẽ tìm hiểu sâu về node.js. Mong là bài viết sẽ có ích cho những ai cần nó

# Serie

1. [Javascript Engine](https://ren0503.github.io/javascript-engine/)
2. [V8 Engine](https://ren0503.github.io/v8-engine/)
3. [Nodejs libuv](https://ren0503.github.io/nodejs-libuv/)
4. [Express (middleware, routing)](https://ren0503.github.io/node-express-p1/)
5. [Express (handle error, template, debug)](https://ren0503.github.io/node-express-p2/)

# Nguồn

[dev.to](https://dev.to/lydiahallie/javascript-visualized-the-javascript-engine-4cdf)
