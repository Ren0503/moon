---
layout: post
title: Các Khái Niệm Căn Bản Về Javascript (P7) - Promise
date:   2021-09-26
excerpt: Tìm hiểu promise trong Javascript.
tags: [javascript, promise]
comments: true
---

Promise được giới thiệu trong phiên bản ES6 giúp giải quyết một số vấn đề mà callback function chưa thể xử lý được. Hôm nay chúng ta hãy cùng nhau đi vào tìm hiểu về khái niệm cũng như cách sử dụng promise trong ngôn ngữ lập trình Javascript để phát triển web nhé.

Trước khi đi vào định nghĩa **promise** thì mình có một ví dụ về đời sống để giúp bạn hiểu được khái niệm về nó một cách đơn giản hơn. Ví dụ:

Chị bạn đang làm việc ở nước ngoài và **hứa (promise)** sẽ gửi cho bạn một quyển sách lập trình mà bạn rất thích vào tháng tới.
Nhưng bạn sẽ không biết tháng tới mình có nhận được quyển sách đó hay không. Vì chị bạn có thể mua cho bạn hoặc vì bận công việc gì đó mà không thể gởi cho bạn vào tháng tới.

Lúc này lời hứa(promise) sẽ có 3 trạng thái là:

- ***Pending*** (Đang chờ xử lý): Bạn không biết mình có nhận được quyển sách vào tháng tới hay không.
- ***Fulfilled*** (Hoàn thành): Bạn đã nhận được sách từ chị của mình vào tháng tới.
- ***Rejected*** (Từ chối): Bạn không nhận được sách vì một lý do nào đó chị của bạn không thể gửi được.

Trong Javascript thì promise khi mới tạo sẽ có trạng thái là **pending**. Nếu kết quả là đã hoàn thành thì sẽ có trạng thái **fulfilled** hoặc xảy ra lỗi thì nó sẽ có trạng thái **rejected**.

Bây giờ chúng ta sẽ cùng nhau đi vào tìm hiểu cách tạo một promise nhé.

# Cách tạo Promise

Đầu tiên chúng ta sẽ đi vào tìm hiểu cú pháp tổng quát của **promise** ở dưới đây nhé:

```javascript
let promise = new Promise(function(resolve, reject){ 
  /*executor*/ 
})
```

Bây giờ để dễ hình dung thì chúng ta sẽ đi vào ví dụ chi tiết sau đây nhé:

```javascript
let receiveBook = true; 
let book = new Promise(function (resolve, reject){
  if(receiveBook) {
    resolve("Bạn đã được chị gửi sách. :)");
  } else {
    reject("Bạn không được chị gửi sách. :(");
  }
});
console.log(book)
```

Kết quả :

![promise1](/assets/img/javascript/promise1.png)

Thì lúc này bạn có thể thấy trạng thái của **promise** đã được thay đổi sang `resolved` và giá trị của **promise** là một chuỗi mà chúng ta đã truyền vào cho hàm `resolve()`. Bạn có thể hình dung cách chạy của nó như sau:

- **Exexutor** sẽ được gọi tự động và ngay lập tức khi `new Promise` được chạy.
- **Executor** nhận hai đối số là **resolve** và **reject**. Những hàm được truyền vào này sẽ được định nghĩa lại thông qua Javascript. Chúng ta chỉ gọi chúng sau khi đã sẵn sàng.
- Bên trong **executor** chúng ta sẽ gọi hàm `resolve()` nếu chương trình thực hiện thành công hoặc gọi hàm `reject()` nếu chương trình xảy ra lỗi
Bây giờ chúng ta sẽ đi vào xem trạng thái **pending** của nó bằng hàm `setTimeout` thông qua đoạn code dưới đây nhé:

```javascript
let receiveBook = true; 
let book = new Promise(function (resolve, reject){
  setTimeout(() => {
    if(receiveBook) {
      resolve("Bạn đã được chị gửi sách. :)");
    } else {
      reject("Bạn không được chị gửi sách. :(");
    }
  }, 3000)
});
console.log(book)
```

Kết quả :

![promise2](/assets/img/javascript/promise2.png)

Như vậy giá trị khi mà **promise** đang ở trạng thái `pending` là `undefined`. Giá trị của nó sẽ được trả về khi **promise** được hoàn thành sau khoảng thời gian 3s.
Tiếp theo chúng ta sẽ đi vào xem trạng thái của nó khi mà chương trình thực thi không thành công bằng cách gán `duocGoiSach = false`. Để hiểu rõ hơn bạn xem đoạn code sau đây nhé:

```javascript
let receiveBook = false; 
let book = new Promise(function (resolve, reject){
  if(receiveBook) {
    resolve("Bạn đã được chị gửi sách. :)");
  } else {
    reject("Bạn không được chị gửi sách. :(");
  }
});
console.log(book)
```

Kết quả :

![promise3](/assets/img/javascript/promise3.png)

Vậy qua 3 ví dụ trên thì chúng ta có thể rút ra một sơ đồ như sau:

![promise4](/assets/img/javascript/promise4.png)

Mình có một số lưu ý là khi promise đã chuyển sang trạng thái **fulfilled** hoặc **rejected** thì chúng ta khổng thể thay đối trạng thái cho nó. Nghĩa là bạn không thể chuyển từ trạng thái **fulfilled** sang **rejected** hoặc theo chiều ngược lại.

Do trạng thái và kết quả của đối tượng **promise** là nội bộ do đó chúng ta không thể sử dụng cách truy cập trực tiếp. Nhưng chúng ta có thể sử dụng các phương thức `.then`, `.catch` và `.finally` để làm điều đó. Để hiểu rõ hơn bạn cùng mình hãy cùng nhau đi vào tìm hiểu nhé.

# Phương thức then

Bây giờ chúng ta sẽ đi vào tìm hiểu cú pháp của nó nhé:

```javascript
promise.then(
  function(result) { /*Xử lý kết quả thành công*/ }
  function(err) { /*xử lý kết quả lỗi*/ }
);
```

Với tham số đầu tiên là hàm chúng ta sẽ chạy khi **promise** được hoàn thành (fulfilled).
Tham số thứ hai là hàm chúng ta sẽ chạy khi **promise** bị từ chối (rejected).
Đầu tiên sẽ tạo một hàm dùng để trả về đối tượng `Promise`:

```javascript
function createPromise(receiveBook) {
  return new Promise(function (resolve, reject){
    setTimeout(() => {
      if(receiveBook) {
        resolve("Bạn đã được chị gửi sách. :)");
      } else {
        reject("Bạn không được chị gửi sách. :(");
      }
    }, 3000);
  });
}
```

Sau đó chúng ta sẽ gọi hàm `createPromise` và thực thi phương thức `then` cho đối tượng **promise**:

```javascript
let book = createPromise(true);
book.then(
  result => console.log(result),
  error => console.log(error)
);
```

Bây giờ chúng ta sẽ kết hợp hai đoạn code trên để xem kết quả sao nhé:

```javascript
function createPromise(receiveBook) {
  return new Promise(function (resolve, reject){
    setTimeout(() => {
      if(receiveBook) {
        resolve("Bạn đã được chị gửi sách. :)");
      } else {
        reject("Bạn không được chị gửi sách. :(");
      }
    }, 3000);
  });
}

let book = createPromise(true);
book.then(
  result => console.log(result),
  error => console.log(error)
);
```

Kết quả :

![promise5](/assets/img/javascript/promise5.png)

Promise đang ở trạng thái `pending` là do chúng ta thiết lập thời gian chạy hàm bằng `setTimeout`. Sau 3s thì hàm phía trên sẽ chạy và trả về đối tượng **promise** với trạng thái thành công như bạn thấy ở hình trên. Sau đó phương thức `then` sẽ nhận giá trị từ đối tượng promise và hiển thị ra ngoài màn hình.
Ngoài ra phương thức `then` cũng cho phép bạn sử dụng một tham số để chỉ xử lý cho trường hợp thành công hoặc trường hợp lỗi:

```javascript
book.then(
  value => console.log(value)
);
```

# Phương thức catch

Nếu như bạn muốn hiển thị lỗi khi đối tượng **promise** ở trạng thái `reject` thì chúng ta có thể sử dụng hai cách sau:

## Sử dụng catch(hàm xử lý lỗi):

```javascript
book.catch( 
  error => console.log(error)
)
```

Và kết quả bạn sẽ được như hình sau đây:

![promise6](/assets/img/javascript/promise6.png)

## Sử dụng then(null, hàm xử lý lỗi):

```javascript
book.then( 
  null,
  error => console.log(error)
)
```

![promise7](/assets/img/javascript/promise7.png)

# Phương thức finally

Phương thức `finally()` sẽ trả về một **Promise**. Khi kết quả trả về của **promise** là `fulfilled` (hoàn thành) hay `rejected` (từ chối) thì đoạn code trong hàm callback của `finally` cũng sẽ được thực thi.
Để bạn dễ hiểu chúng ta sẽ đi vào ví dụ sau đây nhé:

```javascript
function createPromise(receiveBook) {
  return new Promise(function (resolve, reject){
    setTimeout(() => {
      if(receiveBook) {
        resolve("Bạn đã được chị gửi sách. :)");
      } else {
        reject("Bạn không được chị gửi sách. :(");
      }
    }, 3000);
  });
}

let book = createPromise(true);
  book 
    .finally(() => console.log("Kết quả sẽ có sau 3s"))
    .then(
      result => console.log(result),
      error => console.log(error)
    );
```

Kết quả

![promise8](/assets/img/javascript/promise8.png)

Ở đây mình có một số lưu ý sau là:

- `finally` sẽ không có tham số truyền vào. Nó sẽ được gọi mà không cần quan tâm đến kết quả trả về là thành công hay bị từ chối của **promise**.
- Nó không xử lý kết quả trả về từ **promise** mà chỉ chuyển sang các phương thức sau để xử lý.
- Nó giúp chúng ta hạn chế gọi cùng một hàm ở cả trong `then` và `catch`. Để hiểu rõ hơn bạn xem đoạn code sau nhé:

```javascript
function outCome() {
  /* ... */
}
book
  .then(
    (result) => {
      console.log(result);
      outCome();
    }
  ).catch(
    (error) => {
      console.log(error);
      outCome();
    }
  );
```

Như bạn thấy thì hai phương thức `catch` và `then` đều gọi hàm `outCome()`. Do đó chúng ta có thể sử dụng `finally` để giảm sự lặp lại bằng đoạn code như sau:

```javascript
book
  .then(result => console.log(result))
  .catch(error => console.log(error));
  .finally(() => outCome());
```

# Promise Chaining JavaScript

Nãy giờ thì chúng ta chỉ học các định nghĩa và phương thức của nó. Phần này chúng ta tìm hiểu về **promise chain** dùng để xử lý bắt đồng bộ trong Javascript giống như **callback function** đã làm được. Bây giờ bạn cùng mình cùng tìm hiểu qua ví dụ để nắm rõ hơn nhé.

Đầu tiên chúng ta sẽ tạo một `promise` với trạng thái hoàn thành và trả về giá trị là 10 sau 2s. Trong đoạn code mình sẽ dùng hàm `setTimeout` để mô phỏng sự bất đồng bộ từ đó giúp bạn thấy được cách xử lý thông qua `promise`. Để dễ hình dung hơn bạn xem đoạn code sau nhé :

```javascript
let objectPromise = new Promise((resolve, rejects) => {
  setTimeout(() => {
    resolve(8);
  }, 2000);
})
```

Sau đó chúng ta sử dụng phương thức `then()` để gọi hàm xử lý khi `objectPromise` trả về trạng thái thành công. Trong hàm đó thì chúng ta sẽ lấy kết quả của `objectPromise` cộng thêm cho 10. Để hiểu rõ hơn bạn xem đoạn code sau nhé:

```javascript
objectPromise.then((result) => {
  console.log(result);
  return result + 10;
});
```

Phương thức `then` thì sẽ trả về một đối tượng `promise` mới với giá trị là kết quả trả về khi thực hiện thành công.
Do đó bạn có thể sử dụng nhiều phương thức `then` liên tiếp với nhau như đoạn code sau đây:

```javascript
let objectPromise = new Promise((resolve, rejects) => {
  setTimeout(() => {
    resolve(8);
  }, 2000);
})

objectPromise.then((result) => {
  console.log(result);
  return result + 10;
})
.then((result) => {
  console.log(result);
  return result + 20;
})
```

Kết quả :

![promise9](/assets/img/javascript/promise9.png)

Ví dụ trên chính là minh họa cho **promise chain**. Các phương thức của `Promise` như `then()`, `catch()`, `finally` thì sẽ trả về một đối tượng `Promise`. Do đó chúng ta có thể gọi liên tiếp các phương thức này theo một cách tuần tự. Và như bạn cũng thấy là ở ví dụ trên chúng ta đã xử lý sự kiện bất đồng bộ trong Javascript bằng **promise chain**. Và dưới đây là hình ảnh minh họa mà nó hoạt động là:

![promise10](/assets/img/javascript/promise10.png)

Nếu bạn gọi các phương thức `then()` một cách độc lập thì chúng ta sẽ không thể truyền kết quả từ phương thức này sang phương thức khác vì nó không có mối quan hệ với nhau. Để hiểu rõ hơn bạn xem ví dụ sau đây nhé:

```javascript
let objectPromise = new Promise((resolve, rejects) => {
  setTimeout(() => {
    resolve(8);
  }, 2000);
})

objectPromise.then((result) => {
  console.log(result);
  return result + 10;
})

objectPromise.then((result) => {
   console.log(result);
   return result + 20;
})
```

![promise11](/assets/img/javascript/promise11.png)

Như bạn thấy thì kết quả sẽ không thay đổi khi chúng ta gọi các phương thức `then()` một cách độc lập. Để dễ hình dung bạn xem hình ảnh mô tả cách nó hoạt động sau nhé:

![promise12](/assets/img/javascript/promise12.png)

# Tổng kết

Bài viết về Promise đến đây là hết, mong là nó sẽ có ích cho những ai đang cần nó. Hẹn gặp lại ở bài viết về async/await

## Nguồn

[**niemvuilaptrinh**](https://www.niemvuilaptrinh.com/article/Tim-Hieu-Promise-Trong-Javascript)
