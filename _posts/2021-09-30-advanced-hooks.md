---
layout: post
title: Cải Thiện Hiệu Suất Với React Hooks
date:   2021-09-30
excerpt: Cải thiện tốc độ, hiệu suất trang web với các react hooks
tags: [react, hooks]
comments: true
---

Bản thân React đã vốn rất nhanh và có hiệu năng tốt nếu phải đặt lên bàn cân so với những frontend framework, hay lib khác. Tuy nhiên trong quá trình làm việc không ít lần ta vướng phải các vấn đề như component bị re-render quá nhiều lần, và không cần thiết. Các thao tác xử lý toán hay logic quá mất thời gian và ảnh hưởng đến trải nghiệm người dùng. Để giải quyết các bài toán mang tên hiệu năng React đã cung cấp cho chúng ta 2 cái chìa khoá là useMemo và useCallback.

## Các vấn đề về hiệu năng

Khác với class component, các functional component không có các lifecycle như `constructor`, `componentDidMount`,... Mọi thứ nằm bên trong một functional component chính là body của hàm render ứng với người anh em class component. Điều này dẫn đến việc khi bạn sử dụng function component, bất cứ khi nào re-render lại component tất cả mọi thứ bên trong nó đều sẽ được lặp lại, bao gồm cả hàm logic và tính toán. 

### Vì sao lặp lại tính toán ?
> Lý do là các function logic và tính toán thường diễn ra với state. Mà nếu các hàm này không được thực thi sau khi re-render thì nó chỉ thực hiện trên các state cũ, điều đó dẫn đến tính toán sai.

> Vì bản thân các function cũng là object, ở mỗi lần re-render nó sẽ được tạo mới tương đượng một object được tạo mới. Trong trường hợp object hay function này được pass xuống component con, thì component con sẽ bị re-render, mặc cho component con đó có áp dụng React.memo hay React.PureComponent

### Vì sao re-render ?
> Re-render là một bước để React so sánh và quyết định có nên update thứ gì hay không. Quá trình re-render được handle bởi React và nó diễn ra rất nhanh, performance đã được đảm bảo tối ưu nhất có thể bởi React. Thường thì với các tác vụ render text, logic nho nhỏ, bạn không cần và không nên bận tâm đến việc có chạy dư 1, 2 lần re-render. Thậm chí việc áp dụng các kỹ thuật memoization vào các logic nhỏ như vậy càng làm performance tệ hơn, vì nó phát sinh thêm các phần việc kiểm tra, so sánh, ...

_Tuy nhiên_

> Với các UI library có animation, nó sẽ xảy ra tình trạng animation flicking.

> Với các tác vụ tính toán sort, filter nặng nề, nó sẽ chạy đi chạy lại gây hao tốn performance.

Vậy ta sẽ giải quyết các vấn đề này như thế nào ?

# useMemo

Trang React giới thiệu `useMemo` với cú pháp như sau : 

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

và dùng để **trả về một memoized value**

Tạm hiểu là nó trả về một giá trị, là kết quả của việc thực thi hàm `computeExpensiveValue`, tuy nhiên trước khi thực thi thì nó sẽ so sánh với các đối số dependenciens `[a, b]`, nếu một trong chúng thay đổi nó mới tiến hành tính toán còn không thì nó sẽ trả về kết quả cũ. Với tính năng trả ra giá trị trước khi dependencies không thay đổi, ta có thể tránh việc tạo mới các object không cần thiết, giúp tránh re-render không cần thiết.

`useMemo` tập trung vào việc tránh lặp đi lặp lại các logic tính toán nặng nề.

## Tránh tính toán nặng
### Ví dụ khi không dùng `useMemo` :

```jsx
const Example = () => {
  const [count, setCount] = useState(0);

  const getArray = () => {
    // Ví dụ như phải sắp xếp một mảng hàng trăm phần tử
    // tốn tầm 2s để thực thi.
    const result = filterAndSortAndDoSomething(...);

    return result;
  }

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <div>mảng phức tạp: {getArray()}</div>
    </div>
  )
}
```

Như vậy đồng nghĩa mỗi lần click vào `count`, component sẽ được re-render, và hàm `getArray` sẽ bị chạy lại, tốn 2s để có kết quả và render ra màn hình.

### Sử dụng useMemo

```jsx
const Example = () => {
  const [count, setCount] = useState(0);

  const getArray = useMemo(() => {
    // Ví dụ như phải sắp xếp một mảng hàng trăm phần tử
    // tốn tầm 2s để thực thi.
    const result = filterAndSortAndDoSomething(...);

    return result;
  }, []);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <div>mảng phức tạp: {getArray}</div>
    </div>
  )
}
```

Giờ đây, khi click vào `count`, hàm `getArray` ngay lập tức trả ra giá trị result trước đó mà không cần phải mất 2s tính toán. Để ý `getArray` lúc này không còn dấu gọi hàm nữa, vì bản thân useMemo đã chạy hàm ta pass vào, việc của chúng ta chỉ là nhận ra một memoized value mà thôi.

## Tránh re-render

### Không sử dụng useMemo

```jsx
const ComponentA = () => {
  const getStyle = () => {
    return {
      color: 'blue',
      background: 'gold',
    };
  };

  return (
    <div>
      I am suggar daddy
      <ComponentB myStyle={getStyle()} />
    </div>
  );
};

const ComponentB = React.memo(props => {
  return <div style={props.myStyle}>I am suggar baby</div>;
});
```

Với đoạn code trên, mỗi lần `ComponentA` re-render, hàm `getStyle` sẽ tạo ra một object mới và pass xuống `ComponentB`, khiến cho `ComponentB` bị re-render (mặc dù đã sử dụng React.memo)

### Sử dụng useMemo

```jsx
const ComponentA = () => {
  const getStyle = useMemo(() => {
    return {
      color: 'blue',
      background: 'gold',
    };
  }, []);

  return (
    <div>
      I am suggar daddy
      <ComponentB myStyle={getStyle()} />
    </div>
  );
};

const ComponentB = React.memo(props => {
  return <div style={props.myStyle}>I am suggar baby</div>;
});
```

Giờ đây, khi dùng useMemo cho hàm `getStyle`, (hàm `getStyle` không còn dấu gọi hàm, useMemo đã thực thi hàm mà chúng ta pass vào nên ta chỉ cần nhận kết quả - một memoized value mà thôi) ở các lần re-render sau của `ComponentA`, object style cũ sẽ được trả ra thay vì tạo mới -> `React.memo` ở ComponentB nhận thấy prop nhận vào không có sự thay đổi -> không re-render.

# useCallback

Trang React giới thiệu `useCallback` với cú pháp như sau : 

```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```
và dùng để **Trả về một memoized callback**

So với `useMemo`, thì `useCallback` tập trung giải quyết vấn đề về performance, khi mà các callback function được tạo ở functional component cha pass xuống component con luôn bị tạo mới, khiến cho con luôn bị re-render.

`useCallback` trả về một function, nó sẽ chỉ tạo callback function khi các dependencies nhận vào thay đổi, còn không nó sẽ trả về function cũ trước đó, đồng nghĩa với việc function truyền từ cha sang con không được tạo mới, mà không được tạo mới thì không cần phải re-render lại.

## Tránh re-render ở component con

### Ví dụ không sử dụng useCallback :

```jsx
function Parent({ ... }) {
  const [a, setA] = useState(0);
  const onChangeHandler = () => {
    doSomething(a);
  };
  ...
  return (
    ...
    //Pure là component con có sử dụng React.memo
    <Pure onChange={onChangeHandler} />
  );
}
```

Trong trường hợp này, dù ta đã sử dụng React.memo thì khi component `Parent` re-render, hàm `onChangeHandler` sẽ được tạo mới và truyền xuống cho component con và component `Pure` vẫn sẽ phải re-render.

### Dùng useCallback

```jsx
function Parent({ ... }) {
  const [a, setA] = useState(0);
  const onChangeHandler = useCallback(() => {
    doSomething(a);
  }, [a]);
  ...
  return (
    ...
    //Pure là component con có sử dụng React.memo
    <Pure onChange={onChangeHandler} />
  );
}
```

Nhờ sử dụng `useCallback`, giờ đây ở mỗi lần component `Parent` re-render, hàm onChangeHanlder sẽ không còn luôn luôn bị tạo mới nữa, mà sẽ chỉ được tạo mới khi depencency của nó là biến a thay đổi. Vì function không bị tạo mới tức object không bị tạo mới nên component con nhận vào cũng không bị re-render. 

{: .box-note}
**Note:** nếu nhìn qua các ví dụ cũng sẽ thấy `useMemo` và `useCallback` có vài điểm tương đồng với nhau. Nên ta có thể thay thế chúng lẫn nhau :

```jsx
useCallback(fn, deps)
//tương đương
useMemo(() => fn, deps).
```

# Tổng kết

Bài viết trên tìm hiểu hai hooks là useMemo và useCallback để giải quyết các vấn đề về performance khi làm việc với Function Component trong React. Hy vọng nó sẽ có ích với ai đó đang còn lăn tăn về các vấn đề trên. 

### Nguồn

[**kysumattien**](https://kysumattien.com/the-ultimate-guide-about-useMemo-and-useCallback)

