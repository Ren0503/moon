---
layout: post
title: Quản Lý State Với Context API (P1)
date:   2021-10-01
excerpt: Sử dụng context để quản lý state trong React
tags: [react, context]
comments: true
---

Khi sử dụng React đơn thuần ta thường quản lý state bằng cách nào ? Truyền props từ component cha sang cho component con. Đa số đều làm vậy vì nó đơn giản, nhưng liệu nó có thực sự tốt không ?

![context1](https://static.wixstatic.com/media/48e12d_a0a5a12735c64014bd64effaa047d669~mv2.png/v1/fill/w_836,h_598,al_c,q_95/48e12d_a0a5a12735c64014bd64effaa047d669~mv2.webp)

Như trên hình ta sẽ thấy, các component từ cha sang con rồi từ con sang cháu, nên nếu quản lý state theo cách trên thì props sẽ được truyền từ trên xuống ví dụ như từ `Node` sang `Child 2` thì nó sẽ phải chạy qua tất cả component ở nhánh bên trái. Trong trường hợp chỉ có bốn component như trong hình thì nó có thể không thành vấn đề. Nhưng khi con số đấy tăng lên gấp đôi hay gấp ba thì nó thực sự là một vấn đề lớn. 
Để giải quyết vấn đề trên từ phiên bản 16.3 React đã cập nhật một tính năng giúp ta xử lý state một cách gọn gàng và đơn giản. Đấy chính là **Context API**.

# Context API
Ý tưởng của nó vô cùng đơn giản, là nó sẽ lưu trữ tất cả state vào một nơi và cung cấp cơ chế cho phép mỗi component có thể lấy state cũng như cập nhật state trực tiếp tại đó mà không cần qua các component trung gian.

![context2](https://static.wixstatic.com/media/48e12d_bb414bc5ca9440de927964dbd8b3f35c~mv2.png/v1/fill/w_925,h_529,al_c,q_95/48e12d_bb414bc5ca9440de927964dbd8b3f35c~mv2.webp)

So với cách thông thường, cách làm này có ưu nhược điểm là :

- **Ưu điểm**: hạn chế lặp lại code, hạn chế việc truyền props không cần thiết và quản lý state dễ dàng hơn.
- **Nhược điểm**: khó tái sử dụng component vì state tập trung tại một chỗ, tương tự như việc bạn sử dụng biến toàn cục vậy.

# Các API của React Context
## React.createContext

Đây là API *buộc phải có* vì nó có ý nghĩa khởi tạo một object đóng vai trò là context. Context này sẽ là nơi cung cấp state cho tất các component khác sử dung.

```jsx
const MyContext = React.createContext(defaultValue);
```
trong cú pháp trên, khi `MyContext` được tạo nếu có một **Provider** bên ngoài nó thì nó sẽ mang giá trị của context Provider gần nó nhất. Còn nếu không nó sẽ có giá trị của `defaultValue`.

## Context.Provider

Với mỗi một đối tượng Context sẽ tồn tại một đối tượng Provider. Đối tượng này có thuộc tính là `value`. Giá trị của `value` được hiểu là giá trị của Context. Mỗi khi giá trị của `value` này thay đổi thì các thành phần bên trong Provider này sẽ re-render lại. Vì vậy, giá trị của value sẽ tương ứng với state của chương trình.

```jsx
<MyContext.Provider value={/* some value */}>
```

## Class.contextType

```jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* perform a side-effect at mount using the value of MyContext */
  }
  componentDidUpdate() {
    let value = this.context;/* ... */
  }
  componentWillUnmount() {
    let value = this.context;/* ... */
  }
  render() {
    let value = this.context;
    /* render something based on the value of MyContext */
  }
}

MyClass.contextType = MyContext;
```

Nếu như hai API trên là để khởi tạo Context và truyền giá trị cho Context thì API này dùng để sử dụng giá trị của Context. Ở đây nó được sử dụng cho Class Component, contextType sẽ được gán giá trị là MyContext - thành phần được khởi tạo bởi React.createContext() bên trên. Lúc này ở component `MyClass` ta có thể lấy tất cả những giá trị ta cần từ Context thông qua `this.context` ở bất kỳ phương thức nào thuộc React Lifecycle hay render().

## Context.Consumer

Nếu contextType được dùng cho Class Component, thì với Function Component ta có thể sử dụng Context.Consumer. Tuy nhiên Context.Consumer không bị giới hạn trong Function, nó có thể sử dụng ở bất kỳ component nào trả về một jsx.

```jsx
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```

Trong đó, value chính là giá trị của Context. Dĩ nhiên, khi bên ngoài component hiện tại không có Provider nào thì value chính là `defaultValue`.

# Ví dụ
## Bài toán

Bài toán được cho ở đây như sau :
- **component** : Ta có 3 component là Granda, Father và Son. Component Granda chứa Fathe, còn Father thì chứa Son.
- **state** : là một con số - number.
- **handle** : hiển thị ở component Granda và Son, và tăng giảm ở component Father.

Ta sẽ giải quyết bài toán theo step-by-step như sau :

### Khởi tạo Context
```jsx
const AppContext = React.createContext();
```

### Tạo Provider
```jsx
class AppProvider extends React.Component {
  state = {
    number: 10,
    inc: () => {
      this.setState({ number: this.state.number + 1 });
    },
    dec: () => {
      this.setState({ number: this.state.number - 1 });
    }
  };

  render() {
    return (
      <AppContext.Provider value={this.state}>
        {this.props.children}
      </AppContext.Provider>
    );
  }
}
```

AppProvider chính là một **Higher-Order Component** dùng để đóng gọi lại `AppContext.Provider`. Trong ví dụ trên thì AppProvider chính là Component lớn nhất chứa tất cả các Component còn lại. Do đó, ta sẽ định nghĩa state của App tại đây.

Dễ thấy, state bao gồm:

- **number**: dùng để hiển thị ở component Granda và Son.
- **inc()**: dùng để tăng giá trị state number lên 1 đơn vị.
- **dec()**: dùng để giảm giá trị state number xuống 1 đơn vị.
Cuối cùng, bên trong hàm render(), ta truyền giá trị `this.state` vào value của `AppContext.Provider`.

### Sử dụng AppProvider

Bây giờ ta sẽ sử dụng AppProvider làm trung tâm quản lý state, để có thể sử dụng ta chỉ việc đưa component vào phần children của `AppProvider` như các cách lồng component thông thường.

```jsx
const App = () => (
  <AppProvider>
    <Granda />
  </AppProvider>
);

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

### Sử dụng contextType với component Granda

```jsx
class Granda extends React.Component {
  render() {
    return (
      <div className="granda">
        {this.context.number}
        <Father />
      </div>
    );
  }
}

Granda.contextType = AppContext;
```
Ở đây, sử dụng Class.contextType nên cần khai báo Granda dạng class component. 

Lưu ý là, `this.context` tương ứng với giá trị của context tại Provider gần nhất. Đó chính là giá trị `this.state` mà ta đã truyền vào `AppContext.Provider` bên trên.

### Sử dụng Consumer tại component Father

```jsx
const Father = () => (
  <div className="father">
    <AppContext.Consumer>
      {context => (
        <>
          <button onClick={context.inc}>INC</button>
          <button onClick={context.dec}>DEC</button>
        </>
      )}
    </AppContext.Consumer>
    <Son/>
  </div>
);
```

Ở component Father, ta sẽ thử khai báo dạng Functional Component là một jsx thuần. Tương tự như component `Granda`, **context** bên trong `AppContext.Consumer` chính là giá trị của context tại Provider gần nhất. Đó cũng chính là giá trị `this.state` mà ta truyền vào `AppContext.Provider` bên trên.

### Sử dụng consumer tại component Son

Như ở trên đã nói, Consume có thể được sử dụng tại nơi nào có jsx, nên ta có thể dùng chúng ở phần render của class component.
```jsx
class Son extends React.Component {
  render() {
    return (
      <div className="son">
        <AppContext.Consumer>
          {context => context.number}
        </AppContext.Consumer>
      </div>
    );
  }
}
```

Như vậy thì ta đã hiểu về cách hoạt động của Context API, cơ bản thì nó cần 3 thành phần :

- `React.createContext()`: khởi tạo context.
- `<MyContext.Provider>`: nơi tạo giá trị ban đầu cho context.
- `<MyContext.Consumer>` hay `MyClass.contextType` : nơi muốn lấy giá trị từ context.

## Serie

1. [Context API](https://ren0503.github.io/context-api/)
2. [Context Hook](https://ren0503.github.io/context-hook/)

## Nguồn

[**completejavascript**](https://completejavascript.com/quan-ly-state-react-voi-react-context-api)
