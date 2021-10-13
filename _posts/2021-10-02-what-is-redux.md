---
layout: post
title: Redux - Bạn Đời Hoàn Hảo Của React.
date:   2021-10-02
excerpt: Làm thế nào để quản lý dữ liệu với redux trong react.
tags: [react, redux]
comments: true
---

Ở bài viết trước ta đã hiểu về cách quản lý state với Context trong React. Tuy nhiên cách làm đấy chỉ phù hợp với các ứng dụng vừa và nhỏ, trong trường hợp ta cần quản lý nhiều state hơn, có nhiều hàm logic phải thực hiện hơn và nhiều sự thay đổi hơn. Các chương trình của React sẽ trở nên vô cùng phức tạp, nhập nhằng và khó hiểu. Lúc này ta cần một kiến trúc mới, một kiến trúc có khả năng quản lý các state và thực thi mọi thứ ổn định hơn. Vì vậy ta không thể ngồi chờ từng bản cập nhật của React nữa, mà buộc phải tìm một sự hỗ trợ khác. Và đây là tất cả những lựa chọn mà ta có thể dùng :

- Redux
- RxJS
- Mobx
- Apollo Link State
- Unstated
- Flux

Có quá nhiều cái tên, nhưng phổ biến nhất và được dùng nhiều nhất cho các vấn đề với React đấy chính là **Redux**. Dĩ nhiên là ta cũng có thể sử dụng **Redux** cho Angular hay Vue, nhưng rất ít đa số mọi người vẫn dùng React với Redux vì vốn dĩ chúng nó sinh ra là để cho nhau mà. :)

Khi đã chọn đuọc Redux rồi thì ta cần đặt câu hỏi : *Vậy Redux quản lý state như thế nào ?*

# Ý tưởng & Hoạt động

![Ideal](https://miro.medium.com/max/1024/1*XHMeHrC4tdwTSHWRalm_lQ.png)

Ý tưởng cho quản lý state trong redux có thể hiểu qua hình bên trên. Thay vì truyền cho từng component, nó tổng hợp state vào một nơi gọi là store. Sau đó với mỗi thay đổi của component nó sẽ cập nhật vào store và trả về lại component. (nghe qua thì có vẻ giống context tuy nhiên nó khác rất nhiều) 

Về ý tưởng nó chỉ đơn giản như vậy, nhưng khi triển khai hoạt động redux sẽ đòi hỏi nhiều thành phần hơn. Ta có thể quan sát cách redux hoạt động qua hình bên dưới :

![Work](https://miro.medium.com/max/919/1*BcmtHcMHN6PT7IniIWniHg.png)

Từ đấy có thể thấy, redux bao gồm các thành phần : Store, Reducer và Action.
Bây giờ ta sẽ tìm hiểu tại sao phải cần các thành phần đấy.

# 3 Nguyên Tắc Của Redux
Trong redux có ba nguyên tắc bắt buộc phải thực hiện, để nó có thể vận hành bao gồm :
- ***Single source of truth***: State của toàn bộ ứng dụng được lưu tại một nơi duy nhất, một object trong mô hình tree.
- ***State is read-only***: Chỉ có một cách duy nhất để thay đổi state đó là tạo ra một object mô tả những gì xảy ra.
- ***Changes are made with pure functions***: Để chỉ rõ state tree được thay đổi bởi một hành động ta phải cần một pure function.

Với 3 yêu cầu trên đó, rõ ràng ta sẽ cần *store* để lưu trữ, *action* để thay đổi state và *reducer* để chỉ rõ thay đổi.

# Actions

Đây là nơi xử lý các event trong redux, hiểu đơn giản là khi người dùng thực hiện một thao tác sự kiện nào đó có thể dẫn đến thay đổi về state, nó sẽ gọi đến action để thực hiện thay đổi đấy, sau đó action sẽ trả về một dispatch rằng đã thực hiện. Hàm action nhận vào hai thuộc tính là type (kiểu) và payload (giá trị tham số truyền vào). Ví dụ như : 

```javascript
{ type: FACEBOOK_LOGIN, payload: token }
```

`type` thường có dạng string, mục đích nhằm đồng bộ với reducer rằng hành động nào ứng với state nào. Vì như đã nói ở trên, tất cả state sẽ nằm chung một chỗ, nên đế tránh việc trùng lặp cần phân định rõ, hành động nào sẽ thay đổi state nào. Một mẫu action :

```javascript
const fetchBlog = () => {
  return (dispatch) => {
    axios.get('https://api.blogs.com/s3ege314')
      .then(response => {
        console.log(response.data.blog);
          dispatch({
            type: 'FETCH_BLOG',
            payload: response.data.blog
          })
        })
      .catch((error) => {
         throw(error);
      });
  }
}
```

# Reducers

Đây là nơi chỉ định thay đổi của các state. Để tránh những trùng lặp hay nhầm lẫn không đáng có thì thực hiện thay đổi. Hàm reducer nhận vào hai tham số là `state` và `action`. 

```javascript
const reducer = (state = initialState, action)
```

Trong đó `state` là định dạng mặc định của `state` và `action` là các thay đổi diễn ra với state đó. Nếu có thay đổi nào nằm ngoài reducer hay state sau khi bị thay đổi khác định dạng thì nó sẽ báo lỗi. Mẫu reducer :

```javascript
const initialState = {
  isSeen: true,
  blogs: [],
}

const reducer = (state = initialState, action) => {
  switch(action.type) {
    case 'FETCH_BLOG':
      return {
        ...state,
        blogs: state.blogs,            
      }
    case 'IS_SEEN':
      return {
        ...state,
        isSeen: !state.isSeen,
      }
    default:
      return state;
  }
}
```

# Store

Store đóng vai trò lưu trữ trạng thái của các component, thường thực hiện 3 hành động sau :
- *getState()*: lấy state hiện tại
- *dispatch(action)*: kêu action thực hiện hành động
- *subscribe(listener)*: lắng nghe các thay đổi

Cú pháp tạo store :

```jsx
import { createStore } from 'redux';
const store = createStore(reducer);
```

Quay trở lại với mô hình hoạt động của Redux như sau : 

![Work](https://miro.medium.com/max/1838/1*QERgzuzphdQz4e0fNs1CFQ.gif)

Nếu nhìn trên hình, ta sẽ thấy còn một bộ phận trong redux nữa đấy là middleware. Vậy middleware là gì. Ví dụ ta thử viết hàm action như sau :

```javascript
const increase = () => {
  setTimeout(() => {
    return {type: 'INCREASE'}
  }, 1000)
}
```

Thì ngay lập tức chương trình báo lỗi : 

{: .box-error}
> **Error**: Actions must be plain objects, use custom middleware for async actions.

Điều đó có nghĩa là hàm action chỉ được viết dưới dạng plain object để trả về dispatch thôi. Nếu ta muốn thực hiện thao tác nào đó, ta phải sử dụng thêm một middleware như hình sau.

![middleware](https://xdevclass.com/wp-content/uploads/2020/08/middleware.png)

Với redux ta có nhiều loại middleware như : redux-thunk, redux-saga, redux-promise và redux-observable. Trong đấy redux-thunk là nhỏ gọn và phổ biến nhất. 

![Google Trend](https://miro.medium.com/max/1838/1*7xyflPw9uvxxrIKnfX5VnA.png)

Thế nên ở bài viết này ta sẽ giới thiệu sơ qua middleware thunk. Còn ba cái khác mọi người hãy tự tìm hiểu nhé.

# Redux Thunk

**Thunk** là một dạng function trả về một function khác và trong function con này nó sẽ trả về kết quả cần có. Nói đơn giản thunk trả về bằng cách dùng một hàm con thay vì trả trực tiếp.
Redux-thunk dựa trên nguyên lý này để trả về một function có 2 tham số là getState và dispatch (hai thuộc tính của store). Nhờ vào điều đó mà redux-thunk có thể thực hiên các side-effect rồi mới dispatch.

```javascript
const login = (email, password) => async (dispatch) => {
  try {
    dispatch({ type: types.USER_LOGIN_REQUEST });

    const config = {
      headers: {
        'Content-Type': 'application/json',
      },
    };

    const { data } = await axios.post(
      `/api/users/login`,
      { email, password },
      config,
    );

    dispatch({
      type: types.USER_LOGIN_SUCCESS,
      payload: data,
    });

    localStorage.setItem('userInfo', JSON.stringify(data));
  } catch (error) {
    dispatch({
      type: types.USER_LOGIN_FAIL,
      payload:
        error.response && error.response.data.message
          ? error.response.data.message
          : error.message,
    });
  };
};
```

Khai báo redux-thunk trong react :

```javascript
import {createStore, applyMiddleware} from 'redux'; 
import thunk from 'redux-thunk'; 
import reducers from '../reducers';  

const userInfoFromStorage = localStorage.getItem('userInfo')     
  ? JSON.parse(localStorage.getItem('userInfo'))     
  : null  

const initialState = {     
  userLogin: { userInfo: userInfoFromStorage }, 
};

const store = createStore(     
  reducers,     
  initialState,     
  applyMiddleware(thunk) 
);  

export default store;
```

Nhìn chung thì Redux-Thunk dễ code, dễ hiểu nhưng trong một số trường hợp đặc biệt thì Redux-Thunk tỏ ra chưa thực sự mạnh mẽ cho lắm. Điển hình như

- Tạm dừng 1 request hoặc hủy request khi đang gọi api
- Bài toán click vào button để fetch data, nếu click liên tục thì chỉ lấy những lần click sau cùng
- Tự động gọi lại request vài lần khi có sự cố mạng xảy ra

# Tổng kết

Trên đây là những khái niệm căn bản về redux và các thứ liên quan. Mong là nó sẽ có ích với những ai đang gặp rắc rối khi tìm hiểu về redux.

## Tham khảo

[**medium**](https://insights.innovatube.com/redux-thật-là-đơn-giản-phần-1-76a3fa2c31ab)

[**xdevclass**](https://xdevclass.com/so-sanh-redux-thunk-vs-redux-saga-uu-nhuoc-diem-cua-2-middleware-redux-react/)
