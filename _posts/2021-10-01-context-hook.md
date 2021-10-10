---
layout: post
title: Quản Lý State Với Context API (P2)
date:   2021-10-01
excerpt: Sử dụng context để quản lý state trong React
tags: [react, context, hooks]
comments: true
---

Trong trường hợp, với một ứng dụng có quy mô nhỏ không cần phải sử dụng đến redux nhưng vẫn cần phải phân tách các state thật rõ ràng liệu ta có thể quản lý state một cách tối ưu chỉ với React Context không ?
Câu trả lời chính là các hooks context. Ở phần 1 ta đã biết đến các API hỗ trợ Context tuy nhiên nó vẫn có thể được dùng bởi các hook. Ở bài viết này ta sẽ tìm hiểu cách quản lý state với các hook là useContext và useReducer.

# useContext là gì ?

Nó tương tự với `Context.Consumer` hay `Class.contextType` có nhiệm vụ là lấy giá trị từ context cho component hiện tại sử dụng. Song vì là hook nên nó chỉ sử dụng được Function Component. Một ví dụ về cách sử dụng useContext như sau :

```jsx
import React, { useContext } from 'react';

const ExampleContext = React.createContext();

const App = () => {
  return (
    <ExampleContext.Provider value={{ color: 'red' }}>
      <div className='App'>
        <ChildComponent />
      </div>
    </ExampleContext.Provider>
  );
};

const ChildComponent = () => {
  const { color } = useContext(ExampleContext);

  return <p style={{ color }}>This text is {color}</p>;
};

export default App;
```

Trong đoạn code trên, ta khởi tạo context bằng API `createContext`, sau đó tạo `Provider` cũng theo API được hỗ trợ. Về phần khởi tạo thì giữa Context API và Hook không có gì khác biệt. Điểm khác chỉ nằm ở phần tiếp nhận dữ liệu và gán giá trị cho component. 

# useReducer là gì ?

Chỉ cần nghe là biết, hook này đảm nhận nhiệm vụ tương tự reducer ở redux. Nó nhận vào một reducer dạng `(state, action)` và trả ra một `newState`. Khi sử dụng chúng ta sẽ nhận được một cặp bao gồm current state và dispatch function.

```jsx
import React from 'react';

const countReducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT': {
      return {
        ...state,
        count: state.count + action.step,
      };
    }
    case 'DECREMENT': {
      return {
        ...state,
        count: state.count - action.step,
      };
    }
    default:
      return state;
  }
};

const App = ({ initialCount = 0, step = 1 }) => {
  const [state, dispatch] = useReducer(countReducer, {count:initialCount});
  const { count } = state;
  const increment = () => dispatch({ type: 'INCREMENT', step });
  const decrement = () => dispatch({ type: 'DECREMENT', step });
  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
};

export default App;
```

Trên đây là hai ví dụ về cách hoạt động của `useContext` và `useReducer`. Bây giờ ta tìm hiểu làm thế nào mà nó có thể thay thế redux.

# Yêu cầu bài toán

{: .box-note}
Hãy tạo một app, dùng để đăng ký thành viên bằng tên và email. Hiển thị tất cả thành viên trong bảng và cho phép xoá thành viên.

## Thay thế Redux

Để thay thế redux, cách làm của hai hook này là :
- Sử dụng một context chung bọc cả ứng dụng lại để làm state.
- Truyền vào value mặc định là state và dispatch dùng `useReducer`.
- Viết hàm action ứng với từng dispatch.
- Cập nhật state và gọi dispatch bằng `useContext`.

## Tạo store

```jsx
import React, { useReducer, createContext } from "react";

export const ContactContext = createContext();
```

## Tạo reducer 

Để thuận tiện thì thay vì dùng API, ta sẽ tạo một vài dữ liệu mặc định trong reducer.

```jsx
const initialState = {
  contacts: [
    {
      id: "098",
      name: "Diana Prince",
      email: "diana@us.army.mil"
    },
    {
      id: "099",
      name: "Bruce Wayne",
      email: "bruce@batmail.com"
    },
    {
      id: "100",
      name: "Clark Kent",
      email: "clark@metropolitan.com"
    }
  ],
  loading: false,
  error: null
};
```

Bây giờ chúng ta sẽ phân các thay đổi state ứng với từng hành động, nói ngắn gọn là tạo reducer :

```jsx
const reducer = (state, action) => {
  switch (action.type) {
    case "ADD_CONTACT":
      return {
        contacts: [...state.contacts, action.payload]
      };
    case "DEL_CONTACT":
      return {
        contacts: state.contacts.filter(
          contact => contact.id !== action.payload
        )
      };
    case "START":
      return {
        loading: true
      };
    case "COMPLETE":
      return {
        loading: false
      };
    default:
      throw new Error();
  }
};
```

Sau khi tạo reducer rồi, ta dùng `useReducer` để lấy state và dispatch. Rồi dùng nó làm giá trị mặc định cho context Provider.

```jsx
export const ContactContextProvider = props => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <ContactContext.Provider value={[state, dispatch]}>
      {props.children}
    </ContactContext.Provider>
  );
};
```

Bây giờ ta sử dụng Provider này để bọc cả app, xem như chỉ có một nơi duy nhất để thay đổi và cập nhật state.

```jsx
import React from "react";
import { Segment, Header } from "semantic-ui-react";
import ContactForm from "../components/contact-form";
import ContactTable from "../components/contact-table";
import { ContactContextProvider } from "../context/contact-context";

export default function App() {
  return (
    <ContactContextProvider>
      <Segment basic>
        <Header as="h3">Contacts</Header>
        <ContactForm />
        <ContactTable />
      </Segment>
    </ContactContextProvider>
  );
}
```

Theo yêu cầu của bài thì ta cần một form để thêm và một bảng để quản lý. Form sẽ có action là thêm vào :

```jsx
import React, { useState, useContext } from "react";
import { Segment, Form, Input, Button } from "semantic-ui-react";
import _ from "lodash";
import { ContactContext } from "../context/contact-context";

export default function ContactForm() {
  const name = useFormInput("");
  const email = useFormInput("");
  // eslint-disable-next-line no-unused-vars
  const [state, dispatch] = useContext(ContactContext);

  const onSubmit = () => {
    dispatch({
      type: "ADD_CONTACT",
      payload: { id: _.uniqueId(10), name: name.value, email: email.value }
    });
    name.onReset();
    email.onReset();
  };

  return (
    <Segment basic>
      <Form onSubmit={onSubmit}>
        <Form.Group widths="3">
          <Form.Field width={6}>
            <Input placeholder="Enter Name" {...name} required />
          </Form.Field>
          <Form.Field width={6}>
            <Input placeholder="Enter Email" {...email} type="email" required />
          </Form.Field>
          <Form.Field width={4}>
            <Button fluid primary>
              New Contact
            </Button>
          </Form.Field>
        </Form.Group>
      </Form>
    </Segment>
  );
}

function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  const handleChange = e => {
    setValue(e.target.value);
  };

  const handleReset = () => {
    setValue("");
  };

  return {
    value,
    onChange: handleChange,
    onReset: handleReset
  };
}
```

Phần bảng quản lý sẽ hiển thị dữ liệu từ state và dispatch một hành động xoá: 

```jsx
import React, { useState, useContext } from "react";
import { Segment, Table, Button, Icon } from "semantic-ui-react";
import { ContactContext } from "../context/contact-context";

export default function ContactTable() {
  const [state, dispatch] = useContext(ContactContext);
  const [selectedId, setSelectedId] = useState();

  const delContact = id => {
    dispatch({
      type: "DEL_CONTACT",
      payload: id
    });
  };

  const onRemoveUser = () => {
    delContact(selectedId);
    setSelectedId(null);
  };

  const rows = state.contacts.map(contact => (
    <Table.Row
      key={contact.id}
      onClick={() => setSelectedId(contact.id)}
      active={contact.id === selectedId}
    >
      <Table.Cell>{contact.id}</Table.Cell>
      <Table.Cell>{contact.name}</Table.Cell>
      <Table.Cell>{contact.email}</Table.Cell>
    </Table.Row>
  ));

  return (
    <Segment>
      <Table celled striped selectable>
        <Table.Header>
          <Table.Row>
            <Table.HeaderCell>Id</Table.HeaderCell>
            <Table.HeaderCell>Name</Table.HeaderCell>
            <Table.HeaderCell>Email</Table.HeaderCell>
          </Table.Row>
        </Table.Header>
        <Table.Body>{rows}</Table.Body>
        <Table.Footer fullWidth>
          <Table.Row>
            <Table.HeaderCell />
            <Table.HeaderCell colSpan="4">
              <Button
                floated="right"
                icon
                labelPosition="left"
                color="red"
                size="small"
                disabled={!selectedId}
                onClick={onRemoveUser}
              >
                <Icon name="trash" /> Remove User
              </Button>
            </Table.HeaderCell>
          </Table.Row>
        </Table.Footer>
      </Table>
    </Segment>
  );
}
```

Như vậy là xong ta có ứng dụng demo như sau :

![Demo](https://github.com/Ren0503/moon/blob/master/assets/img/redux/demo.gif)

# Tham khảo

[viblo](https://viblo.asia/p/react-hooks-su-dung-usecontext-va-usereducer-de-thay-the-redux-yMnKM89m57P)


[sitepoint](https://www.sitepoint.com/replace-redux-react-hooks-context-api/)
