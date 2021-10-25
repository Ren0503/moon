---
layout: post
title: Tuỳ Chỉnh React Component Với Styled-Components
date:   2021-10-13
excerpt: Sử dụng styled-component để tạo css trong react.
tags: [react, css, styled-components]
comments: true
---

**Styled-Components** là một thư viện giúp bạn tổ chức và quản lý code CSS một cách dễ dàng trong React. Nó được xây dựng với mục tiêu giữ cho các styles của các components trong React gắn liền với chính các components đó. Nó cung cấp một interface rõ ràng và dễ sử dụng cho cả React và React Native. Styled-Component viết các styles css bằng cú pháp js, nên thay đổi hoàn toàn tư duy cho việc xây dựng styles với component.

---
### Danh mục

1. [Chức năng](#chức-năng)
2. [Cài đặt](#cài-đặt)
3. [Bắt đầu](#bắt-đầu)
4. [Chuyển đổi props](#chuyển-đổi-dựa-trên-props)
5. [Mở rộng styles](#mở-rộng-styles)
6. [Styling any component](#styling-any-component)
7. [Truyền props](#truyền-props)
8. [Hoạt động](#cách-styled-components-hoạt-động-trong-component)
9. [Định nghĩa](#định-nghĩa-styled-component-bên-ngoài-phương-thức-render)
10. [Thêm props](#thêm-props-bổ-sung)
11. [Animation](#animation)
12. [React Native](#react-native)
---
# Chức năng

- **Tự động giới hạn CSS**: styled-component theo dõi các component, nếu component được render ra trang web nó sẽ tự động chèn styles vào nó. Ngược lại thì không. Điều đó giúp tránh việc thêm styles cho component vẫn chưa được render, tối ưu hoá việc load css.
- **Tránh lỗi className**: styled-component tự động tạo ra mỗi tên không trùng với mỗi classNames. Nên không cần lo lắng về vấn đề các component gặp lỗi css do trùng tên.
- **Xoá CSS dễ dàng hơn**: thông thường, việc xoá css cho một component sẽ khá khó khăn vì ta không biết class css còn được sử dụng ở đâu. Nhưng với styled-component mọi thứ đơn giản hơn, vì các style sẽ gắn liền với một component cụ thế. Nếu một component không cần tới, ta có thể xoá nó cùng với các style của nó.
- **Thiết kế động**: vì viết bằng js, nên ta có thể truyền props hay global theme mà không cần quản lý tất cả các class theo cách thủ công.
- **Dễ dàng bảo trì**: không cần tìm các file khác nhau để xem styles nào ảnh hưởng đến component của bạn. Việc bảo trì sẽ dễ như ăn bánh dù dự án có lớn đến đâu.
- **Tự động thêm prefix**: viết css theo tiêu chuẩn của bạn và styled-component sẽ xử lý phần còn lại.

# Cài đặt

Cài đặt styled-component như những package thông thường:

```
# with npm
npm install --save styled-components

# with yarn
yarn add styled-components
```

# Bắt đầu

## Hello World

Để sử dụng **styled-component**, ta sử dụng các mẫu là thẻ html như `div`, `h1`, `img`, `section`,... và chèn đoạn mã css mong muốn vào component đấy. Các styled component cũng giống như những component thông thường trong React.

```jsx
import React from 'react';
import styled from 'styled-components';

// Create a Title component that'll render an <h1> tag with some styles
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: white;
`;

// Create a Wrapper component that'll render a <section> tag with some styles
const Wrapper = styled.section`
  padding: 4em;
  background: purple;
`;

// Use Title and Wrapper like any other React component – except they're styled!
const HelloWorld = () => {
  return (
    <Wrapper>
      <Title>
        Hello World!
      </Title>
    </Wrapper>
  );
};

export default HelloWorld;
```

Trong đoạn code trên, ta dùng component `Title` để hiển thị tiêu đề **Hello World** và dùng component `Wrapper` để tạo màu nền. Kết quả ta được:

![hello_world](/assets/img/stycomp/helloworld.png)

> Notes: Styled-component sử dụng package stylis.js, sẽ tự động tạo prefix theo các các quy tắc css, bạn không phải lo về vấn đề đấy. Xem repo của stylis.js tại [đây](https://github.com/thysultan/stylis.js)

# Chuyển đổi dựa trên props

Giống như những component thông thường trong React, ta có thể truyền props vào các styled components như sau:

```jsx
const Button = styled.button`
  /* Adapt the colors based on primary prop */
  background: ${props => props.primary ? "purple" : "white"};
  color: ${props => props.primary ? "white" : "purple"};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid purple;
  border-radius: 3px;
`;

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
  </div>
);
```

Trong ví dụ trên, các component nhận vào props `primary`, nếu có `primary` nó hiển thị nền tím chữ trắng, nếu không có sẽ là nền trắng chữ tím.

![button](/assets/img/stycomp/button.png)

# Mở rộng styles

Các styles component không chỉ dựa trên các mẫu là các thẻ html, mà nó còn có thể có mẫu là các styled component khác.

```jsx
// The Button from the last section without the interpolations
const Button = styled.button`
  color: purple;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid purple;
  border-radius: 3px;
`;

// A new component based on Button, but with some override styles
const CrimsonButton = styled(Button)`
  color: crimson;
  border-color: crimson;
`;

render(
  <div>
    <Button>Normal Button</Button>
    <CrimsonButton>Crimson Button</CrimsonButton>
  </div>
);
```

Ví dụ ta tạo ra button `CrimsonButton` dựa trên `Button` chỉ thay đổi màu. Sự khác biệt trong cú pháp là ta dùng `.` với thẻ html, và dùng `()` với các  component. Ngược lại ta cũng có thể dùng `()` với các thẻ html nhưng không thể dùng `.` với component.

![extend](/assets/img/stycomp/extend.png)

Trong một số trường hợp ta cần phải thay đổi thẻ hoặc style của component. Ví dụ như một thanh navbar, ta cần có sự kết hợp giữa link và button, mà vẫn đảm bảo style của chúng phải giống nhau.

Đối với trường hợp đấy ta có thể dùng `as` để giải quyết vấn đề như sau. 

```jsx
const Button = styled.button`
  display: inline-block;
  color: purple;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid purple;
  border-radius: 3px;
  display: block;
`;

const CrimsonButton = styled(Button)`
  color: crimson;
  border-color: crimson;
`;

render(
  <div>
    <Button>Normal Button</Button>
    <Button as="a" href="#">Link with Button styles</Button>
    <CrimsonButton as="a" href="#">Link with Crimson Button styles</CrimsonButton>
  </div>
);
```

![link](/assets/img/stycomp/link.png)

Và `as` cũng sẽ hoạt động tốt với những component mà ta tự viết:

```jsx
const Button = styled.button`
  display: inline-block;
  color: purple;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid purple;
  border-radius: 3px;
  display: block;
`;

const ReversedButton = props => <Button {...props} children={props.children.split('').reverse()} />

render(
  <div>
    <Button>Normal Button</Button>
    <Button as={ReversedButton}>Custom Button with Normal Button styles</Button>
  </div>
);
```

![reverse](/assets/img/stycomp/reverse.png)

> Notes: Nếu bạn đang dùng phiên bản < 4.x, bạn có thể sử dụng [.withComponent](https://styled-components.com/docs/api#withcomponent) hoặc [.extend](https://styled-components.com/docs/api#deprecated-extend) để có kết quả tương tự "as". Tuy nhiên điều này không khuyến khích vì .extend đã bị loại bỏ ở các phiên bản mới hơn còn .withComponent sẽ bị ngừng hỗ trợ trong tương lai.

# Styling any component

Phương thức styles có thể hoạt động hoàn hảo trên tất cả component của bạn, kể cả là component bên thứ ba, miễn là chúng có kèm theo className để truyền vào DOM element. 

> Notes: Nếu đang sử dụng React-Native, hãy dùng style thay vì className.

```jsx
// This could be react-router-dom's Link for example
const Link = ({ className, children }) => (
  <a className={className}>
    {children}
  </a>
);

const StyledLink = styled(Link)`
  color: purple;
  font-weight: bold;
`;

render(
  <div>
    <Link>Unstyled, boring Link</Link>
    <br />
    <StyledLink>Styled, exciting Link</StyledLink>
  </div>
);
```

![any](/assets/img/stycomp/any.png)

# Truyền props

Nếu styles là một element (vd như `styled.div`) thì styled-component sẽ truyền tất cả thuộc tính HTML sang DOM.
Còn nếu styles là một component (vd như `styled(MyComponent)`) thì styled-component sẽ truyền tất cả qua props.

Ví dụ dưới đây cho thấy, các props của component Input sẽ được truyền sang DOM khi mounted, như là những element.

```jsx
// Create an Input component that'll render an <input> tag with some styles
const Input = styled.input`
  padding: 0.5em;
  margin: 0.5em;
  color: ${props => props.inputColor || "white"};
  background: purple;
  border: none;
  border-radius: 3px;
`;

// Render a styled text input with the standard input color, and one with a custom input color
render(
  <div>
    <Input defaultValue="@default" type="text" />
    <Input defaultValue="@yellow" type="text" inputColor="yellow" />
  </div>
);
```

![props](/assets/img/stycomp/props.png)

> Notes: Khi truyền sang DOM chỉ có defaultValue và type được truyền đi, styled-component sẽ tự động lọc các thuộc tính không chuẩn (ở đầy là inputColor).

# Cách styled-components hoạt động trong component

Ở cách truyền thống, ta thêm css vào component bằng cách import file css, ở đây tiện lợi nhất là sử dụng CSS Module, ví dụ như sau:

```jsx
import React from 'react'
import styles from './styles.module.css'

export default class Counter extends React.Component {
  state = { count: 0 }

  increment = () => this.setState({ count: this.state.count + 1 })
  decrement = () => this.setState({ count: this.state.count - 1 })

  render() {
    return (
      <div className={styles.counter}>
        <p className={styles.paragraph}>{this.state.count}</p>
        <button className={styles.button} onClick={this.increment}>
          +
        </button>
        <button className={styles.button} onClick={this.decrement}>
          -
        </button>
      </div>
    )
  }
}
```

Bởi vì styled-component là sự *kết hợp* của tất cả các element và style của nó. Nên ta có thể viết lại `Counter` như sau:

```jsx
import React from 'react'
import styled from 'styled-components'

const StyledCounter = styled.div`
  /* ... */
`
const Paragraph = styled.p`
  /* ... */
`
const Button = styled.button`
  /* ... */
`

export default class Counter extends React.Component {
  state = { count: 0 }

  increment = () => this.setState({ count: this.state.count + 1 })
  decrement = () => this.setState({ count: this.state.count - 1 })

  render() {
    return (
      <StyledCounter>
        <Paragraph>{this.state.count}</Paragraph>
        <Button onClick={this.increment}>+</Button>
        <Button onClick={this.decrement}>-</Button>
      </StyledCounter>
    )
  }
}
```

> Notes: Ta đặt tên styled-component là "StyledCounter" để không bị trùng với component "Counter", nhưng vẫn có thể nhận dạng trong React Developer Tools và Web Inspector.

# Định nghĩa Styled Component bên ngoài phương thức render

Một điều quan trọng phải nhớ nữa là phải định nghĩa styled component bên ngoài phương thức render. Nếu không, nó sẽ được tạo mới lại sau mỗi lần re-render, việc này sẽ ảnh hưởng đáng kể đến bộ nhớ đệm và tốc độ render. Thế nên ta phải viết như thế này:

```jsx
const StyledWrapper = styled.div`
  /* ... */
`

const Wrapper = ({ message }) => {
  return <StyledWrapper>{message}</StyledWrapper>
}
```

Chứ không được viết như này:

```jsx
const Wrapper = ({ message }) => {
  // WARNING: THIS IS VERY VERY BAD AND SLOW, DO NOT DO THIS!!!
  const StyledWrapper = styled.div`
    /* ... */
  `

  return <StyledWrapper>{message}</StyledWrapper>
}
```

## Pseudoelements, pseudoselectors, nesting

Dành cho những ai đã quên:
- preudo elements là phần theo sau dấu `::` của element. Vd: `::after`, `::before`, `::first-letter`, ...
- preudo classes là phần theo sau dấu `:` của element. Vd: `:hover`, `:active`, `:focus`, ...
- nesting là cấu trúc viết gọn theo dạng phân cấp trong SCSS.

Ở đây bộ tiền xử lý mà styled-component sử dụng là **stylis.js**, hỗ trợ cú pháp giống như scss để tự động lồng các style.

Dấu (&) có thể được sử dụng để chuyển về component chính. Dưới đây là một ví dụ đầy đủ về sử dụng `&`.

```jsx
const Thing = styled.div.attrs((/* props */) => ({ tabIndex: 0 }))`
  color: blue;

  &:hover {
    color: red; // <Thing> when hovered
  }

  & ~ & {
    background: tomato; // <Thing> as a sibling of <Thing>, but maybe not directly next to it
  }

  & + & {
    background: lime; // <Thing> next to <Thing>
  }

  &.something {
    background: orange; // <Thing> tagged with an additional CSS class ".something"
  }

  .something-else & {
    border: 1px solid; // <Thing> inside another element labeled ".something-else"
  }
`

render(
  <React.Fragment>
    <Thing>Hello world!</Thing>
    <Thing>How ya doing?</Thing>
    <Thing className="something">The sun is shining...</Thing>
    <div>Pretty nice day today.</div>
    <Thing>Don't you think?</Thing>
    <div className="something-else">
      <Thing>Splendid.</Thing>
    </div>
  </React.Fragment>
)
```

![hover](/assets/img/stycomp/hover.gif)

Nếu bạn sử dụng selector mà không có dấu `&`, chúng sẽ tham chiếu đến phần tử con của component. 

```jsx
const Thing = styled.div`
  color: purple;

  .something {
    border: 1px solid; // an element labeled ".something" inside <Thing>
    display: block;
  }
`

render(
  <Thing>
    <label htmlFor="foo-button" className="something">Mystery button</label>
    <button id="foo-button">What do I do?</button>
  </Thing>
)
```

![label](/assets/img/stycomp/label.png)

Cuối cùng, dấu & có thể được sử dụng để tăng tính cụ thể cho các style component; điều này sẽ hữu ích nếu bạn đang ở trong môi trường hỗn hợp giữa styled-component và CSS thuần, nơi có thể dẫn đến các xung đột:

```jsx
const Thing = styled.div`
  && {
    color: blue;
  }
`

const GlobalStyle = createGlobalStyle`
  div${Thing} {
    color: red;
  }
`

render(
  <React.Fragment>
    <GlobalStyle />
    <Thing>
      I'm blue, da ba dee da ba daa
    </Thing>
  </React.Fragment>
)
```

Kết quả dòng chữ có màu xanh thay vì màu đỏ:

![blue](/assets/img/stycomp/blue.png)

# Thêm props bổ sung

Styled component cung cấp `.attrs` cho phép ta đính kèm các props (hoặc thuộc tính) vào component.

Bằng cách này, ta có thể đính kèm props tĩnh vào một phần tử hoặc một bên thứ ba, vd như `activeClassName` vào Link component của React Router. Không chỉ thế ta còn có thể đính kèm một props động. Đối tường `.attrs` có thể nhận vào một hàm, hàm này nhận vào một props, thực hiện các thao tác logic và trả về kết quả cho `.attrs`.

Ví dụ dưới đây, Input được đính kèm một thuộc tính động và một thuộc tính tĩnh.

```jsx
const Input = styled.input.attrs(props => ({
  // we can define static props
  type: "text",

  // or we can define dynamic ones
  size: props.size || "1em",
}))`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid purple;
  border-radius: 3px;

  /* here we use the dynamically computed prop */
  margin: ${props => props.size};
  padding: ${props => props.size};
`;

render(
  <div>
    <Input placeholder="A small text input" />
    <br />
    <Input placeholder="A bigger text input" size="2em" />
  </div>
);
```

![input](/assets/img/stycomp/input.png)

## Overidding attr

Lưu ý, khi wrappping một styled compponents, `.attrs` sẽ được áp dụng từ styled component trong cùng đến các styled component ngoài cùng.

Điều này cho phép các component bên ngoài **ghi đè** lên `.attrs` của component trong nó, nó cũng tương tự các khai báo css sau sẽ ghi đè lên các khai báo trước đó.

`.attrs` của Input sẽ được áp dụng trước sau đấy là `.attrs` của PasswordInput:

```jsx
const Input = styled.input.attrs(props => ({
  type: "text",
  size: props.size || "1em",
}))`
  border: 2px solid purple;
  margin: ${props => props.size};
  padding: ${props => props.size};
`;

// Input's attrs will be applied first, and then this attrs obj
const PasswordInput = styled(Input).attrs({
  type: "password",
})`
  // similarly, border will override Input's border
  border: 2px solid aqua;
`;

render(
  <div>
    <Input placeholder="A bigger text input" size="2em" />
    <br />
    {/* Notice we can still use the size attr from Input */}
    <PasswordInput placeholder="A bigger password input" size="2em" />
  </div>
);
```

![textinput](/assets/img/stycomp/textinput.png)

Đấy là lý do tại sao PasswordInput có `type=password` trong khi size vẫn giống với Input.

# Animation

Nếu ta tạo ảnh động bằng `@keyframes` thông thường có thể dẫn đến các xung đột về tên gọi, do đó ta phải dùng keyframes thông qua một đối tượng khác để có thể sử dụng xuyên suốt ứng dụng.

```jsx
// Create the keyframes
const rotate = keyframes`
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
`;

// Here we create a component that will rotate everything we pass in over two seconds
const Rotate = styled.div`
  display: inline-block;
  animation: ${rotate} 2s linear infinite;
  padding: 2rem 1rem;
  font-size: 1.2rem;
`;

render(
  <Rotate>&lt; 💅🏾 &gt;</Rotate>
);
```

![animation](/assets/img/stycomp/animation.gif)

> Notes: keyframe không được hỗ trợ trong React Native. Xem [ReactNative.AnimatefAPI](https://stackoverflow.com/questions/50891046/rotate-an-svg-in-react-native/50891225#50891225)

Keyframe được sử dụng để code-splitted hay lazy-load, thế nên ta cần sử dụng css cho từng fragment được chia sẻ:

```jsx
const rotate = keyframes``

// ❌ This will throw an error!
const styles = `
  animation: ${rotate} 2s linear infinite;
`

// ✅ This will work as intended
const styles = css`
  animation: ${rotate} 2s linear infinite;
`
```

# React Native

Styled-component cũng có thể được sử dụng tương tự trên React Native. Xem vd ở [đây](https://snack.expo.dev/@danielmschmidt/styled-components)

```jsx
import React from 'react'
import styled from 'styled-components/native'

const StyledView = styled.View`
  background-color: purple;
`

const StyledText = styled.Text`
  color: white;
`

class MyReactNativeComponent extends React.Component {
  render() {
    return (
      <StyledView>
        <StyledText>Hello World!</StyledText>
      </StyledView>
    )
  }
}
```

# Tổng kết

Trên đây là những kiến thức cơ bản về sử dụng styled-components. Hy vọng bài viết sẽ có ích cho những ai đang cần nó.

# Nguồn

[**styled-components**](https://styled-components.com/docs/basics)
