---
layout: post
title: Các Khái Niệm Căn Bản Về Javascript (P5) - Prototype
date:   2021-09-24
excerpt: Prototype là gì và tại sao nó quan trọng.
tags: [javascript, prototype]
comments: true
---

Prototype là một khái niệm cơ bản và cốt lõi của ngôn ngữ Javascript, bất kì ai muốn nắm vững ngôn ngữ này đều phải hiểu về khái niệm prototype trong Javascript. Javascript không kế thừa kiểu class-based mà kế thừa trong Javascript là dựa vào protype (từ ES5 trở về trước), điều này khiến prototype trở nên rất quan trọng.

# Prototype là gì 

Trước hết, hãy để ý rằng bản thân prototype là một đối tượng object trong JS, được gọi là prototype object (đối tượng prototype). (Cần chú ý điều này để tránh nhầm lẫn với thuộc tính prototype của function).

Tất cả các object trong Js đều có một prototype, và các object này kế thừa các thuộc tính (properties) cũng như phương thức (methods) từ prototype của mình.

Nếu bạn vẫn chưa hiểu được gì thì cũng đừng vội, javascript vốn dĩ lằng nhằng khó ưa, nhưng qua được đoạn đầu sẽ thấy dễ chịu hơn rất nhiều.

**Nhắc lại 1 chút về các khái niệm kì cục trong Javascript**

**Thứ 1**: Trong Js, bản thân 1 hàm (1 function) cũng được coi là 1 object, và function có một thuộc tính (property) gọi là **thuộc tính prototype**, bản thân thuộc tính prototype này mang giá trị là 1 object. (Chú ý: một instance object thì không có thuộc tính prototype)

**Thứ 2**: Bởi vì ta dùng function để tạo ra 1 mẫu khởi tạo đối tượng, do đó thuộc tính prototype của function có 1 khả năng đặc biệt: bạn sẽ thêm các thuộc tính (property) hoặc phương thức (method) vào thuộc tính prototype của function khởi tạo **để thực hiện kế thừa**, tất cả các đối tượng con tạo ra bởi hàm khởi tạo đều mang các giá trị trong thuộc tính prototype của hàm này.

Chính bởi 2 khái niệm trên, mà có thể xem mẫu khởi tạo (constructor function) là 1 **đối tượng prototype**, bản thân nó có **thuộc tính prototype**. **Kế thừa trong Javascript thuộc kiểu prototype-based** (không giống với class-based như các ngôn ngữ OOP khác).

**Thứ 3**: Các object trong Javascript có một khái niệm gọi là **đặc tính prototype của đối tượng** (prototype attribute), đặc tính này có giá trị trỏ tới **prototype object** mà nó kế thừa thuộc tính. Ta dùng thuộc tính `__proto__` để truy cập tới prototype object.

![prototype](/assets/img/javascript/prototype.png)

# Tạo ra propotype như thế nào?

Như đã nói tới ở trên, do hàm khởi tạo đối tượng cũng được xem là 1 đối tượng prototype, do đó các đơn giản để tạo ra 1 đối tượng prototype là khai báo một hàm khởi tạo:

```
// Create a template init, it also create a prototype object
function Person(_age, _name){
   this.age = _age;
   this.name = _name;
}

// Can add attributes in prototype attribute of function
Person.prototype.height = 0;

// Create a instance of Person
// Have 3 attributes of template init Person
var john_person = new Person(10, "John");
for (var att in john_person){
   console.log(att);
}

// Seen prototype object
// Xem đối tượng prototype của instance vừa tạo
john_person.__proto__;
```

Đoạn code trên vừa tạo ra một hàm khởi tạo là hàm `Person(_age, _name)`, thuộc tính **prototype** của hàm này lại chứa thuộc tính **height**. Do đó một đối tượng được tạo ra từ hàm khởi tạo này ta sẽ có 3 thuộc tính: age, name, và height.
Nếu truy cập **prototype object** của object vừa tạo (instance vừa tạo), thì ta thấy object này là một object chứa 1 hàm khởi tạo và 1 thuộc tính (thuộc tính **height**).

### Nhắc lại về cách tạo object
T
a có các cách sau để tạo ra object: dùng object literal, dùng constructor của đối tượng Object, hoặc tự tạo một constructor function (mẫu khởi tạo).

Những cách này về mặt cách thức thì khác nhau, nhưng về bản chất chúng đều dùng tới hàm khởi tạo và các thuộc tính prototype của hàm này để tạo ra một đối tượng mới: **object literal** và **Object constructor** sử dụng `Object()` và `Object.prototype`, nếu dùng mẫu khởi tạo thì là `mauKhoiTao()` và `mauKhoiTao.prototype`.

# Tại sao prototype lại quan trọng trong Javascript?

## Thuộc tính prototype của function: cơ chế kế thừa trong Javascript

Từ các phiên bản ES5 trở về trước, Javascript không có khái niệm class, và do vậy mà nó không thể thực hiện việc kế thừa để mở rộng ứng dụng như các ngôn ngữ OOP khác. Tuy nhiên, prototype giúp chúng ta thực hiện kế thừa theo một cách gần tương tự như thế: **Javascript thực hiện kế thừa theo cơ chế prototype-based**.

Nói ngắn gọn, để thực hiện kế thừa trong Js, bạn cần tạo 1 hàm khởi tạo, sau đó thêm các thuộc tính và phương thức vào thuộc tính **prototype** của hàm khởi tạo này. Các instance tạo ra bởi hàm khởi tạo này sẽ chứa các thuộc tính và phương thức được định nghĩa ở trên. Đoạn code sau minh hoạ cho điều này:

```
// Create a based function  
function Animal(_age){
   this.age = _age;
}
 
// Can add attribute in prototype attributes of constructor 
Animal.prototype.showAge = function(){
   console.log( this.age );
};

// Create a child function (use for inherit based class) 
function Bird(_color){
   this.color = _color;
}
// Inherit, assignment constructor of Animal for prototype of Bird
Bird.prototype = new Animal(5);
Bird.prototype.showColor = function(){
   console.log( this.color );
};
 
// Test inherit
var eagle = new Bird('red');
eagle.age = 5;
eagle.showAge();       //5
eagle.showColor();     //red
```

Ở ví dụ trên, đối tượng `eagle` sử dụng được hàm `showAge()` của **Animal prototype** bởi vì ta đã gán hàm khởi tạo của `Animal` vào prototype của `Bird`.
Đây chính là cơ chế kế thừa trong Javascript, đối tượng `eagle` đã kế thừa những gì có trong prototype của nó (là `Bird.prototype`), và nó cũng kế thừa luôn những gì có trong thuộc tính prototype của `Bird` (chính là `Animal.prototype`).

## Đặc tính prototype của object: truy cập vào các thuộc tính của đối tượng

Prototype rất quan trọng trong việc giúp ta truy cập tới các thuộc tính và phương thức của đối tượng. Đặc tính prototype của đối tượng (hay còn gọi là prototype object) là một **object cha** nơi chứa các thuộc tính và phương thức được kế thừa. Vì thế, khi ta gọi tới một thuộc tính của đối tượng (vd: eagle.age), ban đầu Js sẽ tìm trong thuộc tính riêng của đối tượng, nếu không tìm thấy, nó sẽ tiếp tục tìm trong prototype của đối tượng, và lặp lại tiếp với prototype của đối tượng prototype, … 

Quá trình lặp lại này được gọi là chuỗi prototype trong Javascript. Chính điều này + thuộc tính prototype của function tạo nên cơ chế kế thừa **prototype-based** cho Javascript.

## Serie Javascript

1. [Scope](https://ren0503.github.io/javascript-scope/)
2. [IIFE](https://ren0503.github.io/javascript-iife/)
3. [Hoisting](https://ren0503.github.io/javascript-hoisting/)
4. [Object](https://ren0503.github.io/javascript-object/)
5. [Prototype](https://ren0503.github.io/javascript-prototype/)
6. [Callbacks](https://ren0503.github.io/javascript-callback/)
7. [Promise](https://ren0503.github.io/javascript-promise/)
8. [Async/Await](https://ren0503.github.io/javascript-async-await/)

## Nguồn

[**nhungdongcodevui**](https://nhungdongcodevui.com/2017/05/24/javascript-prototype-trong-javascript-la-gi-va-tai-sao-no-lai-quan-trong/)
