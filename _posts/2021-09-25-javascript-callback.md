---
layout: post
title: Các Khái Niệm Căn Bản Về Javascript (P6) - Callback
date:   2021-09-25
excerpt: Tất tần tật về callback trong Javascript.
tags: [javascript, callback]
comments: true
---

Callback là một khái niệm không mới. Tuy nhiên, nó là một trong những khái niệm khá lằng ngoằng và dễ nhầm lẫn trong lập trình. Trong bài viết này chúng ta sẽ tìm hiểu về callback funtion trong javascript, đồng thời học thêm cách tạo và sử dụng callback function trong Javascript từ căn bản đến nâng cao nhé!

# Callback Function là gì?

Callback function có thể được hiểu nôm na như sau: callback tức là ta truyền một đoạn code (**Hàm A**) này vào một đoạn code khác (**Hàm B**). Tới một thời điểm nào đó, hàm A sẽ được hàm B gọi lại (**callback**). Javascript là một ngôn ngữ lập trình **hướng sự kiện** và **bất đồng bộ** nên **callback function** đóng vai trò rất quan trọng, bạn sẽ truyền một callback function vào các sự kiện và xử lý bất đồng bộ đó..

Sau đây là một ví dụ đơn giản về callback function trong jQuery, trong ví dụ này thì phương thức click đã hỗ trợ bạn truyền một callback function.

```javascript
$('#test').click(function(){
  // This ís callback function
});
```

Như ví dụ trên bạn có thể thấy chúng ta truyền một function như một tham số cho hàm click.

**Một ví dụ khác hay ho hơn**: Bạn có việc phải đi công tác xa nhà. Bạn dặn người yêu, trong thời gian bạn đi, nếu như có ai giao quà tới, hãy đem qua tặng em gái dễ thương hàng xóm. **Hàm A** ở đây là việc tặng quà cho em hàng xóm:

```javascript
function sendGift(gift) {
  return console.log("Have send " + gift);  
}
```

**Hàm B** ở đây là việc người yêu bạn ở nhà. Ta truyền hàm A vào như 1 argument cho hàm B, tại 1 thời điểm nào đó, **hàm B sẽ gọi hàm A** (Tức người yêu bạn mang quà qua cho em hàng xóm).

```javascript
function inHome(to, sendGift) {
  var gift = "Gift was received";
  sendGift(gift);  
}
```

Thời điểm thực thi đoạn code truyền vào phụ thuộc vào hàm đó được định nghĩa như thế nào. Để rõ ràng hơn mình sẽ định nghĩa một hàm `sayHello()` nhé:

```javascript
function sayHello(name, callback) {
  var myName = name.toUpperCase() + ", Hello";
  return callback(myName);
}
 
var result = sayHello("Todd", function (arg) {
  return arg;
});
 
alert(result);
```

Bạn có thể thấy được thời điểm mà callback được thực thi là sau khi biến `myName` thực hiện. Và khi chúng ta gọi `sayHello` trả về sẽ alert với nội dung là **"Todd, Hello"**.

Việc sử dụng callback function phải hết sức cẩn thận, bạn phải tuân thủ đúng nguyên tắc mà hàm đó đưa ra, có hàm sẽ truyền thêm tham số cho hàm callback và có hàm thì không. Sau đây là một ví dụ về hàm `forEach`, hàm này sẽ có tác dụng lặp một mảng và có hai tham số callback function. Mỗi vòng lặp sẽ truyền hai tham số vào hàm callback function, tham số thứ nhất đó là giá trị của phần tử đang lặp, tham số thứ hai đó là vị trí (*index*) của phần tử đó.

```javascript
// Array
var keywords = ["Me", "callback", "function", "JavaScript"];
​
// Run loop each element and handle in  callback function
keywords.forEach(function (eachName, index){
  console.log(index + 1 + ". " + eachName);
});
```

Ok bây giờ chắc hẳn bạn đã biết callback function là gì rồi phải không nào, nếu vậy thì ta qua phần 2 tìm hiểu cách hoạt động của nó nhé.

# Cách Callback Function hoạt động

Một hàm hỗ trợ callback function thì chắc chắn trong code xử lý của nó sẽ có gọi đến để thực thi hàm callback đó, nhưng vấn đề nó gọi tại vị trí nào trong hàm là điều chúng ta không hề biết, trừ khi chúng ta tự viết nó. Như ở phần **callback là gì** mình có đưa ra một số ví dụ về truyền tham số cho callback function, các tham số này sẽ phụ thuộc vào hàm cha (*hàm xử lý chính*), nếu hàm cha cho phép bạn truyền 3 tham số thì bạn chỉ được truyền 3 tham số, nếu bạn truyền nhiều hơn thì cũng không có tác dụng gì. 

Chúng ta có thể truyền hàm callback vào tương tự như tham số bình thường, như cách chúng ta vẫn làm với các kiểu dữ liệu khác vậy.
Nghĩa là khi truyền một callback vào một hàm khác thì callback tuy nó là một hàm nhưng sẽ không có dấu `()` sau tên hàm.

Điều đó giúp cho hàm callback không được thực thi, mà chỉ được truyền vào như một tham số mà thôi.

```javascript
function doSomething() {
  alert('in');
}
function something(doCallback) {
  doCallback();
}
something(doSomething);
```

Trong ví dụ trên `doSomething` tuy là một hàm, nhưng khi được truyền vào hàm `something()` thì sẽ không có dấu `()` nữa, hàm `something()` muốn thực thi được `doSomething` thì chỉ cần thêm dấu `()` sau tên hàm là được.

# Nguyên tắc khi thực hiện Callback Function

## 1. Tham số truyền vào phải là một function

Điều này rất quan trọng bởi nếu bạn không kiểm tra giá trị mà người dùng truyền vào là một **function** thì bạn không thể thực thi được, đây là sự khác biệt giữa một lập trình viên non kinh nghiệm và nhiều kinh nghiệm. Xem ví dụ dưới đây để hiểu về cách kiểm tra:

```javascript
function showPopup(callback) {
  if (typeof callback !== 'function'){
    alert('You must pass a function');
    return false;
  }
  // do something
}
```

Thông qua ví dụ này ta thấy để kiểm tra một biến có phải là **function** hay không thì ta sử dụng **typeof**, nếu **typeof** có giá trị là `function` thì đó là một **function**.


## 2. Cẩn thận với this khi hàm callback nằm trong object

Hàm được xây dựng trong Object là hàm được định nghĩa thông qua key của object và giá trị của key là một hàm. Trong ví dụ này hàm `setName` được xây dựng bên trong object `personInfo`.

```javascript
var personInfo = {
  name: 'Me',
  setName : function (name) {
    this.name = name;
  }
}
```

Theo đúng nguyên tắc thì hàm **callback** là một hàm đơn phương nên khi bạn sử dụng từ khóa `this` trong hàm thì nó sẽ hiểu `this` lúc này chính là đối tượng `Window Object`, vì vậy cho dù bạn định nghĩa hàm callback nằm trong một object thì không thể truy cập đến dữ liệu của object thông qua từ khóa `this`.
Bạn hãy xem đoạn code sử dụng hàm `setName` là một callback function dưới đây để hiểu rõ hơn:

```javascript
// Object has callback function
var personInfo = {
  name : 'me',
  setName : function(name){
    // this values will useless with key name in this object
    // if we use it as a callback function
    this.name = name;
  }
};
 
// Function has params callback
function test(callback){
  callback('It is me');
}
 
// Call function and pass callback function 
test(personInfo.setName);
 
// Still old result, meaning callback function didn't working 
// with attribute name
document.write(personInfo.name); 
 
document.write('<br/>');

// New resule, meaning window object has created one key name 
// and it's value have been set in setName function
// => this is window object
document.write(window.name);
```

## 3. Khắc phục this khi hàm callback nằm trong object

Ở phần trên mình đã đưa ra lưu ý khi sử dụng **this** trong hàm callback thì this sẽ trỏ tới đối tượng **window** chứ không phải đối tượng chứa hàm **callback**, vậy có cách nào khắc phục tình trạng này không? Có đấy, chúng ta sẽ sử dụng phương thức `apply` của hàm callback. Cú pháp như sau:

```javascript
// Before
callback(var1, var2, ...);
 
// Now
callback.apply(callbackObject, [var1, var2, ... ]);
```

Dưới đây là đoạn code khắc phục lỗi ở trên :

```javascript
// Object has callback function
var personInfo = {
  name : 'me',
  setName : function(name){
    // this values will useless with key name in this object
    // if we use it as a callback function
    this.name = name;
  }
};
 
// Function has params callback
function test(callback, callbackObject){
  var name = "It is me";
  callback.apply(callbackObject, [name]);
}
 
// Call function and pass callback function 
test(personInfo.setName, personInfo);
// Result
document.write(personInfo.name);
```

## 4. Callback Hell

Như ta đã biết, hàm callback được thực thi bên trong 1 hàm khác, nếu ta tiếp tục có hàm callback bên trong một callback khác thì thế nào? Vòng lặp vô tận *callback bên trong callback bên trong callback …* sẽ có khả năng xảy ra. Thứ quái quỷ này được gọi là **callback hell** – địa ngục callback, ta sẽ rất hay gặp vấn đề này trong khi xử lí các lệnh bất đồng bộ, kiểu như:

```javascript
p_client.open(function(err, p_client) {
  p_client.dropDatabase(function(err, done) {
    p_client.createCollection('test_custom_key', function(err, collection) {
      collection.insert({'a':1}, function(err, docs) {
        // ...
        // and more 
      });
    });
  });
});
```

Khi callback hell xuất hiện, logic xử lí của chương trình sẽ trở nên cực kì phức tạp và khó nắm bắt, khi có lỗi xảy ra ta rất khó để debug cũng như giải quyết.

Bên cạnh đó, callback hell cũng làm cho tính thẩm mĩ của code giảm đi đáng kể, khó đọc, khó maintain. Ở các bài viết tiếp theo ta sẽ tìm hiểu thêm về promise và async/await để giải quyết vấn đề callback hell này nhé!

# Tổng kết

Như vậy chúng ta đã cùng nhau tìm hiểu về khái niệm, cách hoạt động và nhưng lưu ý khi thực hiện Callback Function trong Javascript. Mong là bài viết sẽ hữu ích cho những ai đang cần đến nó

## Tham khảo

[codelearn](https://codelearn.io/sharing/callback-function-trong-javascript)

