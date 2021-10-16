---
layout: post
title: Các Phương Pháp Xác Thực Người Dùng
date:   2021-10-10
excerpt: Xác thực người dùng với jwt, token hay oauth2.
tags: [authenticate, node.js]
comments: true
---

Ở bài viết này chúng ta sẽ tìm hiểu về một trong những vấn đề quan trọng nhất trong việc xây dựng các ứng dụng web, đấy chính là phương pháp xác nhận danh tính người dùng. Trước tiên ta cần phải hiểu về hai khái niệm xác thực người dùng và uỷ quyền người dùng.

# Authentication vs Authorization
- **Authentication** hay quyền xác thực người dùng, là quá trình xác minh thông tin đăng nhập của một người dùng hoặc thiết bị cố gắng truy cập vào một hệ thống

- **Authorization** hay uỷ quyền, là quá trình xác minh xem người dùng hoặc thiết bị có được phép thực hiện các tác vụ nhất định trên hệ thống nhất định hay không.

![auth](/assets/img/auth/authen_author.png)

Hiểu đơn giản

- Authentication là câu hỏi *Bạn Là Ai ?*

- Authorization là câu hỏi *Bạn có thể làm gì ?*

Dễ dàng thấy rằng authentication sẽ luôn diễn ra trước authorization. Tức là phải xác định được danh tính của bạn trước khi thực hiện uỷ quyền. Vậy với các ứng dụng web, ta có những phương thức xác nhận danh tính hay uỷ quyền nào.

# HTTP/HTTPS

Để hiểu về cách xác định danh tính với web thì trước tiên phải hiểu giao thức mà các trang web đang dùng. Ở đây chúng là HTTP hay HTTPS, sự khác biệt của hai giao thức này nằm ở việc dữ liệu của HTTPS được bảo mật, tuy nhiên cả hai đều thực hiện request/response giữa client và server một cách độc lập. Tức là khi client thực hiện một request đến server (ở đây có thể là một HTTP POST để đăng ký hay đăng nhập). Server thực hiện tạo tài khoản ở cơ sở dữ liệu sau đấy gửi về response là `HTTP Status 200 OK`. Tuy nhiên khi đến câu lệnh request tiếp theo thì server không thể nhận biết được, client gửi request này có phải là client trước đó hay không. 

Ví dụ ở một trang blog mà người dùng tạo tài khoản sau đấy tiến hành đăng bài, thì server sẽ không xác định được request tạo tài khoản trước đó, và request tạo bài viết hiện tại có phải cùng một người không.

Để giải quyết vấn đề này, HTTP tích hợp xác thực vào trong phần header của mỗi request.

```
"Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=" your-website.com
```

Các thông tin đăng nhập của người dùng sẽ được nối lại với nhau và mã hoá (ở đây chính là đoạn `dXNlcm5hbWU6cGFzc3dvcmQ`), phương pháp mã hoá được dùng ở đây là **base64**.

```javascript
let auth = 'username:password';
let encode = btoa(auth);
let decode = atob(encode);
```

Ví dụ trong Chrome ta sẽ có phần header của request như sau

![authHttp](/assets/img/auth/authHttp.png)

## Hoạt động

1. Phía client thực hiện yêu cầu truy cập một tài nguyên bị hạn chế.
2. Server gửi về HTTP 401 Unauthorized với tiêu đề `WWW-Authenticate` có giá trị là Basic.
3. Sau đấy client sẽ mã hoá username và password rồi request về server.
4. Khi server xác nhận được danh tính người dùng, thì với mỗi request chúng sẽ được thêm phần `Authorization: Basic dcdvcmQ=` vào.

![authHttp](/assets/img/auth/authHttpflow.png)

## Ưu điểm

- Không mất nhiều thời gian, hoạt động nhanh.
- Dễ triển khai.
- Có thể thực hiện trên mọi trình duyệt.

## Khuyết điểm
- Phương pháp mã hoá base64 có thể bị giải mã dễ dàng. Vì phần username và password là thông tin cần cho đăng nhập, nếu nó bị tấn công và giải mã thì người dùng sẽ mất hoàn toàn tài khoản của mình.
- Phải xác thực danh tính với mỗi request.
- Người dùng chỉ có thể đăng xuất bằng cách đăng nhập thông tin không hợp lệ.

Code demo:

```javascript
import basicAuth from 'basic-auth';

function unauthorized(res) {
    res.set('WWW-Authenticate', 'Basic realm=Authorization Required');
    return res.send(401);
};

export default function auth(req, res, next) {
    const {name, pass} = basicAuth(req) || {};

    if (!name || !pass) {
        return unauthorized(res);
    };

    if (name === 'john' && pass === 'secret') {
        return next();
    }

    return unauthorized(res);
};
```

*Notes*: Trong vài trường hợp người ta có thể dùng md5 để mã hoá thay cho base64, điều này giúp thông tin được bảo mật hơn nhưng hiệu quả vấn chưa cao. 

# Session-Cookies

Với xác thực dựa trên session-cookies, trạng thái của người dùng sẽ được lưu trên máy chủ. Tức là nó không yêu cầu username hay password sau mỗi lần request mà thay vào đó, sau lần đăng nhập hợp lệ đầu tiên, nó sẽ tạo sessionId cho người dùng. Và gửi nó cho client, phía client cụ thể là browser sẽ lưu sessionId vàp trong cookies. Như vậy mỗi là cần có yêu cầu đến server nó chỉ cần gửi theo sessionId.

Thông tin về session được lưu trong cookies:

![cookie](/assets/img/auth/cookie.png)

## Flow

1. Client gửi một thông tin xác nhận hợp lệ về phía server.
2. Sau khi server xác định danh tính nó tạo ra một sessionId và lưu nó. Và rồi phản hồi client bằng cách thêm nó vào HTTP với `Set-Cookie` ở Header.
3. Client nhận được sessionId sẽ lưu ở cookie của browser. Sau đó với mỗi lần request tiếp theo sẽ gửi về server.

![session](/assets/img/auth/session_auth.png)

## Ưu điểm

- Vì thông tin nằm trong các request của HTTP lúc này chỉ còn là sessionId, nên sẽ bảo mật tốt hơn so với phương thức HTTP ở trên.
- Các lần đăng nhập tiếp theo nhanh hơn, vì thông tin đăng nhập không bắt buộc.
- Khá dễ thực hiện.

## Khuyết điểm

- Phía server sẽ phải lưu trữ session cho tất cả client, tức là nó sẽ phải lưu trữ một số lượng lớn các SessionId, điều này sẽ gây ra áp lực server quá mức. Nếu server mà chúng ta đang triển khai dạng cluster (cụm), để đồng bộ hóa trạng thái đăng nhập, SessionId cần phải được đồng bộ hóa cho từng máy, điều này vô hình trung làm tăng chi phí bảo trì server. 
- Sẽ gặp khó khi triển khai sang các nền tảng khác (như ứng dụng di động vì nó không cookie để lưu).
- Dễ bị tấn công CSRF.

## Code demo

```javascript
const express = require('express');
const sessions = require('express-session');
const app = express();

const oneDay = 1000 * 60 * 60 * 24;
app.use(sessions({
    secret: "thisismysecrctekeyfhrgfgrfrty84fwir767",
    saveUninitialized:true,
    cookie: { maxAge: oneDay },
    resave: false 
}));
```

# Token

Phương pháp này thay vì sử dụng cookie thì ở đây ta sẽ dùng token. Người dùng sẽ gửi thông tin đăng nhập hợp lệ và server sẽ trả về một token. Token này sẽ được dùng cho các yêu cầu xác thực tiếp theo. Phần lớn token được sử dụng hiện tại đều là Jsonwebtoken(JWT). IETF định nghĩa JWT như sau:

> JSON Web Mã (JWT) là một chuẩn mở (RFC 7519) định nghĩa một cách nhỏ gọn và khép kín để truyền một cách an toàn thông tin giữa các bên dưới dạng đối tượng JSON. Thông tin này có thể được xác minh và đáng tin cậy vì nó có chứa chữ ký số. JWTs có thể được ký bằng một thuật toán bí mật (với thuật toán HMAC) hoặc một public/private key sử dụng mã hoá RSA.

![jwt](/assets/img/auth/jwt.jpeg)

Một chuỗi JWT bao gồm 3 phần là:

- **header**: chứa kiểu dữ liệu , và thuật toán sử dụng để mã hóa ra chuỗi JWT.
- **payload**: chứa các thông tin mình muốn đặt trong chuỗi token như username , userId , author ,...
- **signature**: được tạo ra bằng cách mã hóa phần header , payload kèm theo một chuỗi secret (khóa bí mật)

## Flow

1. Client sẽ gửi thông tin đăng nhập hợp lệ cho phía server.
2. Server sau khi xác thực được người dùng sẽ gửi về cho client một token.
3. Client sẽ lưu token này ở phía mình, với từng yêu cầu xác thực tiếp theo client sẽ cần gửi token về server.

![token](/assets/img/auth/token_auth.png)

## Ưu điểm

- Vì token được lưu ở client nên nó sẽ giảm chi phí cho server. Các request cũng sẽ nhanh hơn vì dựa trên chữ ký số nên không cần phải truy vấn cơ sở dữ liệu. Đồng thời cũng thuận lợi cho phát triển ứng dụng di động vì nó có thể lưu ở AsyncStorage
- Phù hợp với kiến trúc RESTful API và Single-Page-Application.

## Khuyết điểm

- Vẫn có thể bị tấn công XSS(vào localStorage) hay CSRF(vào cookie).
- Các token không thể xoá mà chỉ có thể hết hạn, nên cần thiết lập thời hạn token ở mức ngắn (để tránh tình trạng kẻ xấu lấy được token và làm bậy)

## Code demo

```javascript
const jwt = require('jsonwebtoken');

JWT_SECRET = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9'
JWT_EXPIRE = '1h'

const generateToken = (payload) => {
    return jwt.sign(payload, JWT_SECRET, {
        expiresIn: JWT_EXPIRE,
    });
};

module.exports = generateToken;
```

# One Time Password

OTP (One Time Password) nghĩa là mật khẩu sử dụng một lần. Đây là một dãy các ký tự hoặc chữ số ngẫu nhiên được gửi đến điện thoại của bạn để xác nhận bổ sung khi thực hiện giao dịch, thanh toán qua Internet. Mỗi mã OTP chỉ có thể sử dụng một lần và sẽ mất hiệu lực trong vài phút.

# OAuth2/OpenID

OAuth2 và OpenID là những phương pháp xác thực và uỷ quyền danh tính người dùng phổ biến. Chúng là hình thức đăng nhập một lần(SSO) bằng cách sử dụng thông tin hiện có từ một dịch vụ mạng xã hội như Facebook, Github hoặc Google, để đăng nhập vào trang web của bên thứ ba thay vì tạo tài khoản đăng nhập mới dành riêng cho trang web đó.
