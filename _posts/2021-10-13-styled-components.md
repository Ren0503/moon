---
layout: post
title: Tuỳ Chỉnh React Component Với Styled-Components
date:   2021-10-13
excerpt: Sử dụng styled-component để tạo css trong react.
tags: [react, css, styled-components]
comments: true
---

**Styled-Components** là một thư viện giúp bạn tổ chức và quản lý code CSS một cách dễ dàng trong React. Nó được xây dựng với mục tiêu giữ cho các styles của các components trong React gắn liền với chính các components đó. Nó cung cấp một interface rõ ràng và dễ sử dụng cho cả React và React Native. Styled-Component viết các styles css bằng cú pháp js, nên thay đổi hoàn toàn tư duy cho việc xây dựng styles với component.

# Chức năng

- **Tự động giới hạn CSS**: styled-component theo dõi các component, nếu component được render ra trang web nó sẽ tự động chèn styles vào nó. Ngược lại thì không. Điều đó giúp tránh việc thêm styles cho component vẫn chưa được render, tối ưu hoá việc load css.
- **Tránh lỗi className**: styled-component tự động tạo ra mỗi tên không trùng với mỗi classNames. Nên không cần lo lắng về vấn đề các component gặp lỗi css do trùng tên.
- **Xoá CSS dễ dàng hơn**: thông thường, việc xoá css cho một component sẽ khá khó khăn vì ta không biết class còn được sử dụng ở đâu. Nhưng với styled-component mọi thứ đơn giản hơn, vì các style sẽ gắn liền với một component cụ thế. Nếu một component không cần tới, ta có thể xoá nó cùng với các style của nó.
- **Thiết kế động**: vì viết bằng js, nên ta có thể truyền props vào hay global theme mà không cần quản lý các class theo cách thủ công.
- **Dễ dàng bảo trì**: không cần tìm các file khác nhau để xem styles nào ảnh hưởng đến component của bạn. Việc bảo trì sẽ dễ như ăn bánh dù dự án có lớn đến đâu.
- **Tự động thêm tiền tố**: viết css theo tiêu chuẩn của bạn và styled-component sẽ xử lý phần còn lại.

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

Cú pháp để sử dụng styled-component:

```jsx
import React from 'react';
import styled from 'styled-components';

const Name = styled.div`
  // css code
  padding: 4em;
  background: papayawhip;
`

const Compt = () => {
  return (
    <Name>Hi</Name>
  )
}

export default Compt;
```

Ta lấy `styled` từ **styled-component**, cú pháp sau dấm `.` sẽ là `div` hoặc có thể là `h1`, `img`, `a`, ... Và trong đấy sẽ là đoạn mã css cho styles của component mà ta cần. Bây giờ ta sẽ thử viết **Hello World**:

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

Kết quả ta được:

![hello_world](/assets/img/stycomp/helloworld.png)

## Chuyển đổi dựa trên props

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

![button](/assets/img/stycomp/button.png)

## Mở rộng styles

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

![extend](/assets/img/stycomp/extend.png)

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

## Styling bất kỳ component

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

## Truyền props

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

## Pseudoelements, pseudoselectors, and nesting

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

![blue](/assets/img/stycomp/blue.png)

## Thêm props

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

### Overidding attr

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

# Animation

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

# Nguồn

[**styled-components**](https://styled-components.com/docs/basics)
