---
layout: post
title: Các Khái Niệm Căn Bản Về Javascript (P4) - Object
date:   2021-09-23
excerpt: Bàn về object trong Javascript.
tags: [javascript, object]
comments: true
---

Trong Javascript, một trong những khái niệm cốt lõi và cơ bản nhất của nó là khái niệm về Object, đây cũng chính là một kiểu dữ liệu trong JS – kiểu dữ liệu Object. Bạn cần hiểu được và nắm vững các khái niệm cơ bản của Js trước khi có ý định học các Framework của nó.

Nhắc lại về các kiểu dữ liệu trong Javascript, ta có 5 kiểu dữ liệu cơ bản và 1 kiểu dữ liệu phức hợp. 5 kiểu dữ liệu cơ bản bao gồm: Number, String, Boolean, Undefined và Null. Kiểu dữ liệu phức hợp là kiểu dữ liệu Object. Kiểu dữ liệu Object được sử dụng rất thường xuyên trong Js vì nó rất biến hoá và mạnh mẽ, đây là khái niệm cơ sở cho hàng loạt các đặc điểm nổi bật khác của Javascript, ta hãy thử xem nó là gì nào.

# Object là gì

Về mặt định nghĩa, một đối tượng (một object) là một danh sách các **item**, mỗi item là một cặp name-value, trong đó value có thể là: các kiểu dữ liệu cơ bản, function, hay cũng có thể là một object khác (kiểu dữ liệu phức hợp).

Ta gọi mỗi item là một **property** (thuộc tính) của object nếu value của item đó có kiểu dữ liệu là kiểu phức hợp hoặc các kiểu dữ liệu cơ bản, ngược lại nếu value của item nó là một hàm (một function) thì ta gọi nó là method của object (phương thức của object).
Ta xét một ví dụ sau:

```javascript
var myFirstObject = {
  firstName: "Richard",
  favoriteAuthor: "Conrad"
};
```

Ta thấy rằng, đối tượng object này là một danh sách các items, mỗi item (có thể là property hoặc method) được lưu trữ trong object bởi cặp giá trị name-value, trong trường hợp này thì các tên của property là `firstName` và `favoriteAuthor`, tương ứng với các tên này là các giá trị `Richard` và `Conrad`.

**Những cách để truy cập tới các thuộc tính của object**

Để lấy được các giá trị của thuộc tính trong object, ta có thể truy cập tới tên của thuộc tính bằng toán tử ngoặc vuông “[ ]” hoặc dấu chấm “.”, minh hoạ như sau:

```javascript
myFirstObject.firstName;        //Richard
myFirstObject['favoriteAuthor'];  //Conrad
```

Truy cập tới các thuộc tính không tồn tại của object sẽ cho ta giá trị là undefined.
Có một trường hợp nên chú ý, nếu tên của thuộc tính là số (tức là number), thì ta chỉ có thể truy cập bằng cách dùng dấu ngoặc vuông `[]` mà không thể dùng dấu chấm `.`, xem minh hoạ sau để hiểu rõ:

```javascript
var myObject = {10: 'test1'}
myObject.10;             //Throw Error
myObject['10'];          //test1
```

# Cách để tạo ra Object

Thông thường có hai cách tạo object : **object literals** và **object constructor**.

## Object literals

Mình không rõ tiếng Việt gọi **literal** là gì, nhưng đại khái là sẽ dùng cặp ngoặc nhọn `{ }` để tạo một object. **Literal** có thể hiểu theo ngữ cảnh nào đó là việc “sử dụng chuỗi thuần tuý”. Đoạn code sau minh hoạ cho điều này:

```javascript
var myBook = {10: 'test1'};  // object have attribute

// object have an attribute and a method
var myCar = {
   brand: 'Toyota',
   run: function(){
      console.log('running');12
 };
```

## Object constructor

Cách này sẽ sử dụng phương thức khởi tạo **constructor** của kiểu dữ liệu Object để tạo ra các object. Phương thức khởi tạo này là một hàm để tạo ra các object mới, ta dùng kèm từ khoá new:

```javascript
// Create new object
var myApple = new Object();
 
 
// Add attributes for object
myApple.color = 'red';
myApple.shape = 'round';
myApple.howSweet = function(){
   console.log('I am sweet');
};
```

Trong Js, thì các thuộc tính của object có thể là các kiểu dữ liệu cơ bản, các phương thức, hay cũng có thể là các object con. Kiểu dữ liệu Object cho ta một cách sử dụng rất linh hoạt và mạnh mẽ.

## Tạo nhiều object

Ở trên, ta đã biết cách để tạo ra từng object riêng biệt, thế còn việc tạo hàng loạt các object giống nhau (có cùng tên thuộc tính và số lượng thuộc tính) như thế nào? Ta cũng có 2 cách để làm việc này: sử dụng mẫu khởi tạo, và sử dụng prototype.

### Sử dụng mẫu khởi tạo

Cách này sử dụng từ khoá function để tạo ra một hàm khởi tạo đối tượng, dùng từ khoá this để gán các thuộc tính cho đối tượng:

```javascript
function Fruit (_color, _name){
   this.color = _color;
   this.name = _name;
 
   this.showName = function(){
      console.log(this.name);
   }
};
```

Với hàm khởi tạo này, ta có thể tạo hàng loạt các đối tượng kiểu `Fruit` như sau:

```javascript
var mangoFruit = new Fruit('yellow', 'Mango');
var appleFruit = new Fruit('red', 'Apple');
```

### Sử dụng prototype
Thuộc tính prototype là một thứ rất đặc biệt trong Javascript, ta sẽ bàn về nó sau, ở đây ta sẽ sử dụng nó để tạo hàng loạt đối tượng giống nhau như sau:

```javascript
function Fruit (_color, _name) {};
 
Fruit.prototype.color = 'general_color';
Fruit.prototype.name = 'general_name';
Fruit.prototype.showName = function(){
  console.log(this.name);
};
```

Với cách này thì ta cũng vẫn sẽ sử dụng hàm `Fruit()` để tạo ra các đối tượng object như cách ở trên:

```javascript
var mangoFruit = new Fruit('yellow', 'Mango');
var appleFruit = new Fruit('red', 'Apple');
```

Thuộc tính prototype không chỉ được dùng duy nhất trong tình huống này. Do Javascript không phải là ngôn ngữ class-based (như PHP, C++, C#, …) mà là kiểu object-based, do đó chúng ta không thể thực hiện việc tạo class và kế thừa như các ngôn ngữ hướng đối tượng thuần tuý. Tuy nhiên, thuộc tính prototype cho phép ta thực hiện nhiều việc *tương tự* như class và kế thừa trong Javascript, nhưng thôi ta sẽ bàn chi tiết về nó ở một topic khác.

# Chi tiết về Object

Ta biết rằng Object là kiểu dữ liệu phức hợp trong Javascript, bởi vì phức hợp nên nó sẽ có nhiều điều thú vị.

## Kiểu dữ liệu tham trị và tham chiếu

Sự khác biệt cơ bản giữa kiểu dữ liệu tham trị và kiểu tham chiếu đó là: giá trị của kiểu dữ liệu tham chiếu được lưu trữ như là **một tham chiếu**, tức là giá trị của biến sẽ không được lưu trực tiếp tại biến, mà biến đó sẽ lưu một tham chiếu tới giá trị thực.
Các kiểu dữ liệu cơ bản trong Javascript là các kiểu dữ liệu tham trị, còn kiểu dữ liệu Object sẽ là kiểu dữ liệu tham chiếu. Minh hoạ kiểu tham trị như sau:

```javascript
// Basic data type save by values
var num = 1;
var otherNum = num;  // now otherNum = 1
 
// Change values
num = 2;
console.log(num);        // 2
console.log(otherNum);  // 1
```

Như đã thấy ở trên, các kiểu dữ liệu cơ bản trong Javascript sẽ lưu giá trị theo kiểu tham trị. Ngược lại, kiểu dữ liệu Object sẽ lưu giá trị kiểu tham chiếu:

```javascript
// Declare 2 object type Object
var person = {name: "John"};
var otherPerson = person;
 
// Change values
person.name = "Peter";
console.log(person.name);        //Peter
console.log(otherPerson.name);  //Peter
```

Như đã thấy, mặc dù thay đổi giá trị của biến `person`, nhưng giá trị của biến `otherPerson` cũng bị thay đổi. Nguyên do của việc này là bởi các đối tượng Object lưu giá trị theo kiểu tham chiếu, tức là 2 biến này cùng tham chiếu tới 1 giá trị, thay đổi giá trị này sẽ thay đổi giá trị thuộc tính của tất cả những đối tượng khác đang tham chiếu tới nó.

## Thuộc tính riêng và thuộc tính kế thừa

Một cách khái quát, thuộc tính riêng (own property) là thuộc tính được định nghĩa tại bản thân của đối tượng (tại bản thân object), thuộc tính kế thừa (inherited property) là những thuộc tính được kế thừa từ đối tượng prototype của object đó. 

Để kiểm tra một thuộc tính có thuộc object hay không (kể cả thuộc tính riêng và thuộc tính kế thừa), ta dùng toán tử `in`:

```javascript
var school = {schoolName: 'Havard'};

// Test attributes
console.log('schoolName' in school);  //true
console.log('schoolType' in school);  //false
```

Để kiểm tra một thuộc tính có phải là thuộc tính riêng hay không, ta có thể dùng phương thức `hasOwnProperty` của Object:

```javascript
// create template init
function Fruit(){
};
Fruit.prototype.color = 'general_color';
 
// create object 
var apple = new Fruit();
// create private attribute of object
apple.name = 'ownName';
// test attributes
console.log(apple.hasOwnProperty('color'));  //false
console.log(apple.hasOwnProperty('name'));  //true
```

## Các đặc tính của thuộc tính 

Ta đã hiểu thế nào là các thuộc tính của object, bây giờ ta hãy xem xét chi tiết hơn về các đặc tính của nó.

Trong tiếng Anh, người ta gọi các thuộc tính là **property**, còn đặc tính thì được gọi là **attribute**. Một thuộc tính sẽ có 4 đặc tính sau đây:

- Giá trị (value): Đây là đặc tính rõ thấy nhất, bởi vì mỗi thuộc tính đều mang 1 giá trị nào đó, có thể là giá trị cơ bản hoặc method.
- Tính Enumable: mang giá trị true/false, cho phép một thuộc tính được duyệt qua trong vòng lặp for-in.
- Tính configable: mang giá trị true/false, nói lên khả năng config như delete thuộc tính, thay đổi các đặc tính khác của thuộc tính, …
- Tính writable: mang giá trị true/false, cho phép ta thay đổi giá trị của thuộc tính hay không.

Nói một chút về việc xoá một thuộc tính của object, ta có thể dùng toán tử `delete` để xoá một thuộc tính của đối tượng. Để lệnh delete được thực thi, ta cần xoá đúng vị trí thuộc tính được định nghĩa, tức là: thuộc tính riêng thì xoá tại đối tượng, thuộc tính kế thừa thì xoá tại đối tượng prototype.

Nắm rõ được khái niệm Object trong Javascript, ta sẽ đủ cơ sở để có thể tìm hiểu những khái niệm lắt léo khác của Javascript.

## Tham khảo

[kipalog](https://kipalog.com/posts/Ban-ve-khai-niem-Object-trong-Javascript)
