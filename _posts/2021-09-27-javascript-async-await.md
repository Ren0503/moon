---
layout: post
title: Các Khái Niệm Căn Bản Về Javascript (P8) - Async/Await
date:   2021-09-27
subtitle: Giải thích về async/await trong javascript
excerpt: [javascript, async, await]
comments: true
---

Trong một thời gian rất dài, chúng ta phải dựa vào callback để làm việc với các đoạn code bất đồng bộ trong javascript. Kết quả là, rất nhiều người trong chúng ta đã có những trải nghiệm kinh khủng khi phải đối mặt với các hàm trông như [thế này](https://tutorialzine.com/media/2017/07/callback-hell.jpg).

**Callback** có rất nhiều nhược điểm. Khi ta có nhiều thao tác bất đồng bộ, các callback phải chờ nhau thực hiện, thời gian để hoàn thành sẽ bị kéo dài hơn. Ngoài ra, việc viết các callback lồng nhau cũng làm cho mã nguồn của ta rắc rối và khó bảo trì.

Thật may mắn, trong phiên bản ES6 , JavaScript đã được bổ xung thêm **( .then() ) Promise**. Nó là một thay thế tuyệt vời cho callbacks và hầu hết cộng đồng nhanh chóng chuyển sang sử dụng nó để thay thế cho callbacks. Code mới của chúng ta gần giống với code cũ, kết quả là trông dễ theo dõi và bảo trì hơn. Tuy nhiên các vấn đề của callback vẫn chưa được giải quyết triệt để.

Cuối cùng, trong phiên bản ES7 gần đây nhất, **Async/Await** đã được bổ sung để việc viết code bất đồng bộ trong JavaScript tốt hơn, code dễ nhìn hơn và dễ sử dụng hơn.

# Async/Await là gì?

**Async/Await** là một tính năng của JavaScript giúp chúng ta làm việc với các hàm bất đồng bộ theo cách thú vị hơn và dễ hiểu hơn. Nó được xây dựng trên Promises và tương thích với tất cả các Promise dựa trên API. Trong đó:

- **Async** : khai báo một hàm bất đồng bộ (async function someName(){...}).
    - Tự động biến đổi một hàm thông thường thành một Promise.
    - Khi gọi tới hàm async nó sẽ xử lý mọi thứ và được trả về kết quả trong hàm của nó.
    - Async cho phép sử dụng Await.
- **Await** : tạm dừng việc thực hiện các hàm async. (Var result = await someAsyncCall ().
    - Khi được đặt trước một Promise, nó sẽ đợi cho đến khi Promise kết thúc và trả về kết quả.
    - Await chỉ làm việc với Promises, nó không hoạt động với callbacks.
    - Await chỉ có thể được sử dụng bên trong các function async.

Dưới đây là một ví dụ đơn giản mà hy vọng sẽ rõ ràng những điều trên:

{: .box-note}
Giả sử chúng ta muốn lấy một số tệp JSON từ máy chủ của mình. Chúng ta sẽ viết một hàm sử dụng thư viện [axios](https://github.com/axios/axios) và gửi yêu cầu HTTP GET đến [example.json](https://tutorialzine.com/misc/files/example.json). Chúng ta phải chờ đợi phản hồi từ máy chủ, đây là một yêu cầu HTTP không đồng bộ.

Dưới đây chúng ta sẽ viết cùng một chức năng với 2 cách khác nhau: cách thứ 1 với Promises, và cách thứ 2 với Async/Await.

```javascript
// Way 1: 
function getJSON() {
  // To make the function blocking we manually create a Promise.
  return new Promise( function(resolve) {
    axios.get('https://tutorialzine.com/misc/files/example.json')
      .then( function(json) {
        // The data from the request is available in a .then block
        // We return the result using resolve.
        resolve(json);
      });
  });
}
// Way 2:
// Async/Await approach

// The async keyword will automatically create a new Promise and return it.
async function getJSONAsync() {
  // The await keyword saves us from having to write a .then() block.
  let json = await axios.get(
    'https://tutorialzine.com/misc/files/example.json'
  );

  // The result of the GET request is available in the json variable.
  // We return it just like in a regular synchronous function.
  return json;
}
```

Cả hai hàm bên trên đều thực hiện một chức năng hoàn toàn giống nhau - cả hai đều trả về Promises và giải quyết với phản hồi JSON từ `axios`. Tuy nhiên, phiên bản Async/Await ngắn hơn và dễ đọc hơn.

Chúng ta có thể gọi hàm async của chúng ta như sau:

```javascript
getJSONAsync().then( function(result) {
  // Do something with result.
});
```

# Async/Await có làm Promise lỗi thời ?

**Không hoàn toàn**. Khi làm việc với Async/Await, thật ra chúng ta vẫn đang sử dụng ngầm Promises. Vì thế, kể cả khi đang sử dụng Async/Await cần một sự hiểu biết tốt về Promises sẽ rất tốt cho chúng ta.

Ngoài ra, có những trường hợp mà Async/Await không sử dụng được và chúng ta phải sử dụng Promises. Ví dụ như khi chúng ta cần gọi nhiều thao tác bất đồng bộ và chờ cho tất cả chúng kết thúc. Nếu chúng ta thử và làm điều này với async và await, Điều gì sẽ xảy ra hãy xem ví dụ dưới đây các bạn sẽ thấy 

```javascript
async  function  getABC () {
  let A = await getValueA(); // getValueA takes 2 second to finish
  let B = await getValueB(); // getValueB takes 4 second to finish
  let C = await getValueC(); // getValueC takes 3 second to finish
  
  return A*B*C;
}
```

- Mỗi lần gọi tới hàm `await` sẽ đợi cho đến khi hàm `await` trước đó kết thúc. Vì các **wait** sẽ đợi và thực hiện tuần tự từng cái một, toàn bộ chức năng sẽ mất 9 giây để thực hiện xong hàm từ đầu đến cuối (2 + 4 + 3).

- Đây không phải là một giải pháp tối ưu vì A, B và C không phụ thuộc vào nhau, chúng ta không cần biết giá trị của A trước khi chúng ta có được B. Vì vậy, chúng ta có thể lấy chúng cùng một lúc và thời gian chờ đợi sẽ được giảm bớt đi.

- Trong trường hợp như thế này, sử dụng **Promise** sẽ thích hợp hơn. Để gửi tất cả các yêu cầu cùng lúc, chúng ta sử dụng `Promise.all()`. Việc sử dụng `Promise.all()` sẽ đảm bảo chúng ta có tất cả các kết quả trước khi tiếp tục thực thi code, nhưng việc gọi đến các hàm bất đồng bộ sẽ được chạy song song mà không phải tuần tự từng cái một.

```javascript
async  function  getABC () {
  // Promise.all() allows us to send all requests at the same time. 
  let results = await Promise.all([ getValueA, getValueB, getValueC ]); 
  
  return results.reduce((total,value) => total * value);
}
```

Bằng cách này, thời gian thực thi hàm sẽ mất it hơn. Hàm `getValueA` và `getValueC` sẽ thực hiện xong trước khi `getValueB` xong. Thay vì phải mất 9 giây để chờ từng hàm trả về giá trị như trên, chúng ta sẽ chỉ mất 4 giây để chờ cả 3 hàm trả về giá trị.

# Xử lý lỗi trong Async/Await

Một điều tuyệt vời khác về Async/Await là nó cho phép chúng ta bắt các lỗi không mong đợi bằng cách sử dụng *try/catch*. Chúng ta chỉ cần để các await call của chúng ta vào trong khối *try/catch* như sau:

```javascript
async function doSomethingAsync(){
  try {
    // This async call may fail.
    let result = await someAsyncCall();
  }
  catch(error) {
    // If it does we will catch the error here.
  }  
}
```

Mệnh đề `catch` sẽ xử lý các lỗi gây ra bởi các hàm bất đồng bộ hoặc bất kỳ lỗi nào chúng ta có thể đã viết bên trong khối `try`.

Trong một vài tình huống, chúng ta cũng có thể bắt các lỗi khi đang thực hiện **function async**. Vì tất cả các hàm **async** đều trả về **Promises**, chúng ta chỉ cần gọi thêm hàm `.catch()` khi gọi chúng:

```javascript
// Async function without a try/catch block.
async function doSomethingAsync(){
  // This async call may fail.
  let result = await someAsyncCall();
  return result;  
}

// We catch the error upon calling the function.
doSomethingAsync().
  .then(successHandler)
  .catch(errorHandler);
```

Dựa vào các tình huống cụ thể, chúng ta sẽ sử dụng **try/catch** hoặc `.catch()` để bắt và xử lý lỗi. Tuy nhiên, chúng ta không nên sử dụng cả 2 cùng một lúc vì nó có thể dẫn đến các vấn đề không mong muốn.

# Hỗ trợ trình duyệt

- Async/Await có thể sử dụng trong hầu hết các trình duyệt chính, ngoại trừ IE11 - tất cả các trình duyệt sẽ nhận ra mã async/await của bạn mà không cần các thư viện bên ngoài.

- Nếu các bạn dùng Node cũng có sử dụng async từ Node 8 trở lên. Nó sẽ trở thành LTS vào cuối năm nay.

- Nếu cái này không đáp ứng được nhu cầu của bạn, cũng có một số trình chuyển đổi JS như Babel và TypeScript và thư viện Node.js async/await cung cấp các phiên bản nền tảng riêng của tính năng này.

# Tổng kết

Với việc bổ sung Async/Await trong ngôn ngữ JavaScript có một bước nhảy vọt về khả năng dễ đọc và dễ sử dụng cho người mới bắt đầu lập trình JavaScript và người đã có kinh nghiệm. Đây cũng là bài viết cuối cùng trong serie tìm hiểu về Javascript, hy vọng nó sẽ có ích với những ai cần nó.


## Tham khảo

[viblo](https://viblo.asia/p/giai-thich-ve-asyncawait-javascript-trong-10-phut-1VgZvBn7ZAw)
