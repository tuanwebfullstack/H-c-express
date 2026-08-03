

#  REST API trong Express là gì?

## P - Preview

**Mục tiêu sau bài này**

Bạn sẽ hiểu:

* REST API là gì.
* API hoạt động như thế nào.
* Client và Server giao tiếp ra sao.
* Vì sao Express thường dùng để viết REST API.

Chưa cần code nhiều.

---

# A - Analogy (Ví dụ thực tế)

Hãy tưởng tượng bạn đi ăn ở nhà hàng.

Có 4 thành phần.

```
Bạn
↓

Nhân viên phục vụ

↓

Nhà bếp

↓

Món ăn
```

Trong lập trình:

```
Frontend
↓

API

↓

Server

↓

Database
```

Frontend không tự vào database.

Frontend chỉ nói với API.

Ví dụ:

Bạn bấm

```
Đăng nhập
```

Frontend gửi

```
POST /login
```

Server nhận.

Kiểm tra.

Nếu đúng

```
{
    token: "abc123"
}
```

Nếu sai

```
{
    message:"Sai mật khẩu"
}
```

---

# REST API cũng giống như menu của nhà hàng

Menu có nhiều món.

API cũng có nhiều đường dẫn.

Ví dụ

```
GET /users

POST /users

PUT /users/5

DELETE /users/5
```

Mỗi đường dẫn giống một món ăn.

---

# C - Concept

## REST là gì?

REST là một quy tắc thiết kế API.

REST quy định:

* URL nên đại diện cho tài nguyên.
* Mỗi thao tác dùng đúng HTTP Method.
* Server trả dữ liệu dạng JSON.

Ví dụ.

Có tài nguyên

```
Users
```

REST sẽ thiết kế:

```
GET /users
```

Lấy danh sách.

```
GET /users/10
```

Lấy user số 10.

```
POST /users
```

Tạo user mới.

```
PUT /users/10
```

Sửa user.

```
DELETE /users/10
```

Xóa user.

Đây chính là REST API.

---

## Express tạo REST API như thế nào?

Ví dụ:

```javascript
const express = require("express");

const app = express();

app.get("/users", (req, res) => {
    res.json([
        {
            id:1,
            name:"An"
        }
    ]);
});

app.listen(3000);
```

Khi truy cập

```
GET localhost:3000/users
```

Server trả

```json
[
    {
        "id":1,
        "name":"An"
    }
]
```

---

## REST API chủ yếu xoay quanh 4 HTTP Method

| Method | Ý nghĩa          |
| ------ | ---------------- |
| GET    | Lấy dữ liệu      |
| POST   | Tạo dữ liệu      |
| PUT    | Cập nhật toàn bộ |
| DELETE | Xóa dữ liệu      |

Sau này sẽ học thêm:

```
PATCH
```

để cập nhật một phần.

---

## URL đại diện cho tài nguyên

Sai:

```
/getUsers

/createUser

/deleteUser
```

Đúng theo REST:

```
GET /users

POST /users

DELETE /users/5
```

REST để Method thể hiện hành động.

URL chỉ là tài nguyên.

---

## Luồng hoạt động

```
Frontend

↓

GET /users

↓

Express

↓

Controller

↓

Database

↓

Express

↓

JSON

↓

Frontend
```

Đây là luồng bạn sẽ gặp trong mọi dự án Express.

---

# E - Exercise

### Câu 1

Muốn lấy danh sách sản phẩm.

Method nào?

A. GET

B. POST

C. DELETE

D. PUT

---

### Câu 2

Muốn tạo user mới.

Method nào?

A. POST

B. GET

C. DELETE

D. PUT

---

### Câu 3

URL nào đúng chuẩn REST?

A.

```
/createUser
```

B.

```
/users
```

---

### Thử tự suy nghĩ

Nếu website TodoList của bạn có API:

```
GET /todos
```

Theo bạn API này làm gì?

---

# R - Review

### Hôm nay cần nhớ 5 ý

✅ API là cầu nối giữa Frontend và Backend.

✅ REST là quy tắc thiết kế API.

✅ URL đại diện cho **tài nguyên** (users, todos, products...), không phải hành động.

✅ HTTP Method thể hiện **hành động**:

* GET → lấy
* POST → tạo
* PUT → cập nhật
* DELETE → xóa

✅ Express tạo REST API bằng các hàm như:

```javascript
app.get(...)
app.post(...)
app.put(...)
app.delete(...)
```
