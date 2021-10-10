---
layout: post
title: Kết Nối React với Redux Dùng (Hoặc Không Dùng) Hook
date:   2021-10-03
excerpt: So sánh connect vs useSelector & useDispatch.
tags: [react, redux, hooks]
comments: true
---

Ta đã từng nói về thư viện quản lý dữ liệu cho React là Redux, ở bài viết này ta sẽ đi vào chi tiết sự kết hợp giữa React và Redux hay cụ thể hơn chính là các phương thức `connect()`, `useSelector()` và `useDispatch()`.

Trước khi bắt đầu ta cần một mẫu redux để tiện trình bày:

**Action**

```javascript
export const addToCart = (food) => {
  return {
    type: 'ADD_TO_CART',
    payload: food
  };
};
```

**Reducer**

```javascript
const initialState = {
  foods: [
    {id: '1a', name: 'Bear', quantity: 3},
    {id: '2b', name: 'Chicken', quantity: 6},
    {id: '3c', name: 'Fish', quantity: 4}
  ]
};

export const rootReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ADD_TO_CARD':
      return {
        ...state,
        foods: state.foods.map((food) =>
          food.id === action.payload.id
          ? { ...food, quantity: food.quantity - 1 }
          : food
        )
      };
    default:
      return state;
  }
}
```

**Store**

```javascript
import { createStore } from 'redux';
import { rootReducer } from './reducer';
export const store = createStore(rootReducer);
```

# connect()

Nói sơ qua về connect thì đây là một HOC được dùng để kết nối react với redux. Có cú pháp như sau 

```javascript
connect(mapStateToProps, mapDispatchToProps)(Component);
```

Ta sẽ thấy hai hàm là `mapStateToProps` và `mapDispatchToProps`, đây là hai hàm thuần tuý trả về các *object* và những *key của object* này sẽ được gửi đi như là props của component mà chúng đã **connect**.

- **mapStateToProps**: hoạt động như một bộ lọc để lấy các *state* mà nó muốn lấy, sau khi *reducer* trong redux phân loại các state, lúc này ở mỗi component chúng chỉ cần một hay vài *state* chứ không cần tất cả. Như vậy hàm này sẽ giúp component có được dữ liệu cần thiết không thừa không thiếu.

- **mapDispatchToProps**: hàm này cũng đóng vai trò như bộ lọc nhưng thay vì là reducer thì nó lại có liên kết với *actions* của redux, nó sẽ chỉ nhận về kết quả từ một hay vài *action* mà nó mong muốn thay vì tất cả.

Component ta viết như sau :

```javascript
import React from 'react';
import FoodItem from './FoodItem';
import { connect } from 'react-redux';
import { addToCart } from '../store/actions';

function FoodList({ foodList, addToCart }) {
  return (
    <>
      <h2 className="title">FoodList use connect Redux</h2>
      <div className='food-list'>{foodList.map((foodItem) => 
        (<FoodItem 
          key={foodItem.id}
          foodItem={foodItem}
          addToCart ={addToCart }
        />))}
      </div>
    </>
  )
}

const mapStateToProps = (state) => ({ foodList: state.foods})

const mapDispatchToProps = { addToCart }

export default connect(mapStateToProps, mapDispatchToProps)(FoodList);
```

Mô hình hoạt động của connect như sau :

![connect1](/assets/img/redux/connect.png)

# useSelector() và useDispatch()

Trong trường hợp ta thay thế connect bằng các hooks ta được đoạn code như sau:

```javascript
import React from 'react';
import FoodItem from './FoodItem';
import { useSelector, useDispatch } from 'react-redux';
import { addToCart } from '../store/actions';

export default function FoodListHook() {
  const foodList= useSelector((state) => state.foods);
  const dispatch = useDispatch();
  const dispatchAddToCart = (food) => dispatch(addToCart (food));
    
  return (
    <>
      <h2 className="title">FoodList use hook Redux</h2>
      <div className='food-list'>{foodList.map((foodItem) => 
          (<FoodItem 
            key={foodItem.id}
            foodItem={foodItem}
            addToStomatch={dispatchAddToCart }
          />)
        )}
      </div>
    </>
  );
};
```

Trong đơn giản hơn rất nhiều, vậy các hooks này hoạt động như sau.

## useSelector()

Hook này cho phép chúng ta lấy state từ Redux store bằng cách sử dụng một selector function làm tham số đầu vào.

Trong đoạn code phía trên ta trả về mảng food từ store. Mặc dù nó thực hiện công việc như `mapStateToProps` nhưng nó vẫn có một số khác biệt mà bạn cần phải quan tâm.

- `mapStateToProps` chỉ trả về 1 object, còn `useSelector` có thể trả về bất cứ giá trị nào.
- Khi dispatch một *action*, `useSelector` sẽ thực hiện so sánh tham chiếu giữa giá trị được trả về trước đó và giá trị hiện tại. Nếu chúng khác nhau, component sẽ bị **re-render**. Nếu chúng giống nhau, component sẽ không **re-render**.

Như đã nói, `mapStateToProps` là một function sẽ luôn được chạy lại mỗi khi store có một sự thay đổi bất kì nào trong đó. Với `mapStateToProps`, tất cả các trường được trả về lại thành một dạng object kết hợp. Vậy nên mỗi khi mapState chạy thì nó sẽ trả về một object với tham chiếu mới. Hàm `connect()` sẽ thực hiện so sánh nông với object mà `mapStateToProps` trả về, nếu khác nhau thì sẽ **re-render** lại component. Tức hiểu cặn kẽ hơn là so sánh tham chiếu (so sánh ===) các trường bên trong object mà `mapStateToProps` trả về, chỉ cần 1 trường khác nhau là sẽ bị coi là khác nhau.

Hiểu đơn giản là `conenct()` sẽ hạn chế số lần bị **re-render** hơn so với `useSelector()`.

## useDispatch()

Hook này đơn giản chỉ là trả về một tham chiếu đến **dispatch** function từ Redux store và được sử dụng để **dispatch** các action.

Một điều tương tự với `useSelector()` là `useDispatch()` dễ rơi vào **re-render** ngay cả trong tình trạng state không có gì thay đổi.

# Ưu nhược của việc sử dụng Redux Hooks

## Ưu điểm

Không còn `connect()` HOC => ít node trong hệ thống component hơn.

## Nhược điểm

Bạn sẽ mất tính năng tự động memo mà `connect()` cung cấp.

# Tổng kết

Trên đây là những so sánh về **connect** và **react-redux hook**. Mong là nó sẽ có ích với những ai đang cần nó.

# Tham khảo

[xdevclass](https://xdevclass.com/so-sanh-connect-vs-useselector-usedispatch-redux-connect-voi-redux-hooks-moi/)
