---
layout: post
title: Quản Lý Dữ Liệu Đơn Giản Hơn Với Redux Toolkit
date:   2021-10-04
excerpt: Tìm hiểu về @reduxjs/toolkit trong react.
tags: [react, redux]
comments: true
---

**Redux** là một công cụ quản lý state phổ biến trong React, tuy nhiên với nhiều người thì nó khá lằng nhằng vì phải xử lý từ **action** sau đấy chạy sang thiết lập **reducer** rồi chạy về **store**. Chưa kể còn phải quan tâm đến **middleware**, **thunk** hay **saga**,...chưa kể nó cũng không tiêu chuẩn về cách vài viết login nào cả và điều này rất phiền phức khi xây dựng các ứng dụng lớn. Như vậy redux toolkit ra đời để đem đến sự gọn gàng, và linh động hơn.

Điều đầu tiên phải hiểu redux vẫn có 3 phần là **action**, **reducer** và **store**.

# Action, Reducer và Store

## Store

Về **store** thì redux toolkit có sẵn các package hỗ trợ việc triển khai các **store** dễ dàng hơn, tiêu biểu là nó có sẵn *redux-thunk*, ví dụ như thông thường để triển khai **store** với redux-thunk ta phải làm như sau:

```javascript
import { createStore, applyMiddleware, compose } from 'redux';
import thunkMiddleware from 'redux-thunk';
import rootReducer from './reducers';

// Enable to use redux dev tool in development mode
const composeEnhancers = 'development' === process.env.NODE_ENV
    ? (window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose)
    : compose;
// Use redux-thunk as a redux middleware
const enhancer = composeEnhancers(applyMiddleware(thunkMiddleware));

const store = createStore(rootReducer, {}, enhancer);
export default store;
```

Với redux toolkit ta có thể làm đơn giản hơn:

```javascript
import { configureStore } from '@reduxjs/toolkit';
import rootReducer from './reducers';
const store = configureStore({ reducer: rootReducer });
```

## Reducers

Điều tương tự **actions** và **reducer** cũng đơn giản hơn nhiều với `createAction` và `createReducer`. Ví dụ thông thường với **reducer** ta phải viết:

```javascript
function todosReducer(state = [], action) {
    switch (action.type) {
        case 'ADD_TODO': {
            return state.concat(action.payload);
        }
        case 'TOGGLE_TODO': {
            const { index } = action.payload;
            return state.map((todo, i) => {
                if (i !== index) return todo;
                return {
                    ...todo,
                    completed: !todo.completed,
                };
            })
        }
        case 'REMOVE_TODO': {
            return state.filter((todo, i) => i !== action.payload.index);
        }
        default:return state;
    }
}
```

Khá là dài dòng, nhưng với `createReducer` thì mọi thứ lại đơn giản hơn.

```javascript
const todosReducer = createReducer([], (builder) => {
    builder
        .addCase('ADD_TODO', (state, action) => {
            // "mutate" the array by calling push()
            state.push(action.payload)
        })
        .addCase('TOGGLE_TODO', (state, action) => {
            const todo = state[action.payload.index]
            // "mutate" the object by overwriting a field
            todo.completed = !todo.completed
        })
        .addCase('REMOVE_TODO', (state, action) => {
            // Can still return an immutably-updated value if we want to
            return state.filter((todo, i) => i !== action.payload.index)
        })
})
```

## Actions

Còn với action, nếu thông thường ta viết rằng:

```javascript
function addTodo(text) {
    return {
        type: 'ADD_TODO',
        payload: { text },
    }
}
```

Thì giờ chỉ cần ngắn gọn như thế này:

```javascript
const addTodo = createAction('ADD_TODO');
addTodo({ text: 'Buy milk' });
```

Song như đã nói ở trên tuy redux toolkit vẫn có 3 thành phần riêng rẽ như vậy, nhưng để linh hoạt cho việc chỉnh sửa dữ liệu chúng ta sẽ gộp **actions** và **reducers** lại cùng nhau gọi là **slice**.

# Slices = Actions + Reducers

Để hiểu rõ slices trước tiên ta đi đến ví dụ sử dụng **action** và **reducer** trong cùng một file:

```javascript
const CREATE_POST = 'CREATE_POST';
const UPDATE_POST = 'UPDATE_POST';
const DELETE_POST = 'DELETE_POST';

export function addPost(id, title) {
    return {
        type: CREATE_POST,
        payload: { id, title },
    }
};

const initialState = [];
export default function postsReducer(state = initialState, action) {
    switch (action.type) {
        case CREATE_POST: {
            // Omit actual codebreak
        };
        default:
            return state;
    }
}
```

Vậy slices sẽ kết hợp như thế này:

```javascript
const postsSlice = createSlice({
    name: 'posts',
    initialState: [],
    reducers: {
        createPost(state, action) {},
        updatePost(state, action) {},
        deletePost(state, action) {},
    },
});

// Extract the action creators object and the reducer
const { actions, reducer } = postsSlice;
// Extract and export each action creator by name
export const { createPost, updatePost, deletePost } = actions;
// Export the reducer, either as a default or named export
export default reducer;
```

Có thể thấy hàm `createSlice` nhận vào 3 tham số :

- **`name`**: Đây sẽ coi như là tiền tố để phân chia các slice để sau này **dispatch** các **action** dễ dàng hơn.
- **`initialState`** : Phần này tương tự như bên **reducer**, là phần các **state** khởi tạo của redux.
- **`reducers`**: Một object mà mỗi key giống như là một **action**, các **action** này sẽ kích hoạt khi chúng được **dispatch type** tương ứng (Nhìn ban đầu sẽ giống như là các `switch/case` trong **reducers**).

Như vậy `createSlice` giúp chúng ta giải quyết các vấn đề khi tạo redux module, **reducer** và **action** được nhóm lại trong một **object**, và chúng ta chỉ cần export ra để sử dụng trong ứng dụng, đồng thời **action types** cũng được sinh ra với tiền tố mặc định là trường `name` khi tạo **slice**.

## extraReducers builder callback

Ở các ví dụ trên các **action** sẽ tự động sinh ra tương ứng là các thuộc tính trong trường **reducers**, vậy nếu trường hợp **reducer** của slice muốn lắng nghe các **action** khác thì sao, lúc này ta sử dụng một option khác khi tạo slice, tên là `extraReducers`.

```javascript
import { createSlice, createAction } from '@reduxjs/toolkit';

const otherAction = createAction('updateCounterByValue');

const counterSlice = createSlice({
    name: 'counter',
    initialState: { count: 0 },
    reducers: {
        increment: state => state.count += 1,
        decrement: state => state.count -= 1
    },
    extraReducers: {
        // có thể viết là [otherAction] hoặc otherAction.type
        [otherAction]: (state, action) => {
            state.count += action.payload
        }
    }
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

Bây giờ chúng ta sẽ tìm hiểu cách xử lý bất đồng bộ trong redux toolkit.

# createAsyncThunk()

Thông thường một hàm bất đồng bộ sẽ được triển khai trong redux-thunk như sau:

```javascript
function fetchPostDetail(postId) {
    return async (dispatch) {
        try {
            const response = await services.fetchPostDetail(postId);
            dispatch(fetchPostSuccess(response.data));
        } catch (error) {
            dispatch(fetchPostFail(error));
        }
    }
}
```

Với redux toolkit ta có thể làm với `createAsyncThunk` như sau:

```javascript
export const fetchPostDetail= createAsyncThunk(
    'posts/fetch_post_detail',
    async (postId, thunkParams) => {
        const response = services.fetchPostDetail(postId);
        return response.data;
    }
);
```

Ví dụ trên cho ta thấy `createAsyncThunk` là một function nhận vào hai tham số, một **string** là `type` và một hàm **callback** là `payloadCreator`.

Hàm `createAsyncThunk` này sẽ thực hiện thi luồng bất đồng bộ và có thể **dispatch** ra ba **action** khác nhau tương ứng với ba trạng thái của **Promise** được truyền vào, giúp ta xử lý một luồng bất đồng bộ tương tự nhưng ngắn gọn hơn so với ví dụ trước đó. Giá trị **type** được truyền vào hàm `createAsyncThunk` ban đầu sẽ là tiền tố tương ứng với **type** của ba **action** được tạo ra như sau:

```javascript
// fetchPostDetail.pending : 'posts/fetch_post_detail/pending'
// fetchPostDetail.fulfilled : 'posts/fetch_post_detail/fulfilled'
// fetchPostDetail.rejected : 'posts/fetch_post_detail/rejected'
```

Ở đây có một lưu ý là `createAsyncThunk` chỉ giúp tạo ra một luồng hoạt động bất đồng bộ và **dispatch action** theo tham số được truyền vào mà không tự mình thực thi logic xử lý **reducer** nào cả, chúng ta sẽ phải tự viết logic **reducer** để nhận và xử lý các **action** được `createAsyncThunk` tạo ra và **dispatch** như ở trên.

Trong hàm **callback** `payloadCreateor` được truyền vào `createAsyncThunk` ví dụ ở trên có 2 tham số, trong đó, tham số thứ nhất là là một giá trị đơn (`arg`) mà ta sẽ truyền vào luồng bất đồng bộ, trong ví dụ thì khi gọi **api** để lấy về `post_detail` thì ta cần truyền vào id chẳng hạn. Nếu muốn truyền nhiều tham số hơn thì sẽ cần nhóm chúng lại bằng một **object**, như sau:

```javascript
dispatch(fetchPostDetail({ id: 1, key1: "value1", key2: "value2" }))
```

Tham số thứ hai là một **object** chứa các giá trị mặc định được truyền vào hàm **thunk**(`thunkAPI`), có thể được sử dụng khi cần thiết trong logic để tính toán kết quả của luồng bất đồng bộ:

- **`dispatch`**: phương thức **dispatch** của **store**, cho phép **dispatch action** bên trong hàm **callback**.
- **`getState`**: phương thức cho phép tham chiếu giá trị của **state** hiện tại.
- **`extra`**: các tham số được truyền vào trong lúc thiết lập **redux-thunk middleware**.
- **`requestId`**: giá trị `id` duy nhất được sinh ra tự động khi hàm **thunk** được gọi.
- **`signal`**: một **object** là `AbortController.signal` được sử dụng để xem liệu một phần khác trong logic của ứng dụng có đánh dấu yêu cầu là cần hủy hay không.
- **`rejectWithValue`**: đây là một hàm tiện ích cho phép ta muốn bắt buộc luồng xử lý phải trả về trạng thái **rejected** với một giá trị **payload** được chỉ định chính là tham số được truyền hàm đó.

Cơ chế sử dụng `createAsyncThunk` giúp chúng ta giải quyết bài toán bất đồng bộ một cách ngắn gọn hơn, giúp giảm thiểu những đoạn code lặp lại.

# Tổng kết

Trên đây là những khái niệm và hoạt động của **redux toolkit**. Mong là nó sẽ có ích với những ai đang bắt đầu tìm hiểu về nó.
