
# PACER - Body Parser trong Express

# P - Problem (Vấn đề)

Giả sử bạn xây dựng API đăng ký tài khoản.

Frontend gửi:

```http
POST /register
Content-Type: application/json

{
    "name": "Tuan",
    "email": "tuan@gmail.com"
}
```

Express nhận được request.

Bạn thử:

```javascript
app.post("/register", (req, res) => {
    console.log(req.body);

    res.send("OK");
});
```

Kết quả:

```text
undefined
```

Tại sao?

Bởi vì Express **nhận được dữ liệu**, nhưng **chưa biết cách đọc phần Body**.

Giống như bạn nhận được một bưu kiện nhưng chưa mở hộp.

---

# A - Analogy (Ví dụ đời thực)

Hãy tưởng tượng bạn làm nhân viên bưu điện.

Khách hàng gửi một chiếc hộp.

Bên ngoài hộp ghi:

```text
Người gửi

Người nhận

Địa chỉ
```

Bạn nhìn được ngay.

Đó giống:

```text
Headers
```

---

Nhưng bên trong hộp mới là:

```text
Đơn hàng

Tiền

Hóa đơn
```

Muốn biết bên trong có gì bạn phải:

```text
Mở hộp
```

Body Parser chính là người mở chiếc hộp.

Sau khi mở:

```text
Tên

Email

Password
```

được lấy ra.

Express đặt chúng vào

```javascript
req.body
```

---

# C - Concept (Khái niệm)

## Request gồm những phần gì?

```text
HTTP Request

├── URL
├── Method
├── Headers
└── Body
```

Ví dụ:

```http
POST /login

Headers:
Content-Type: application/json

Body:
{
    "email":"abc@gmail.com",
    "password":"123456"
}
```

---

## Body Parser làm gì?

Nó đọc dữ liệu trong Body.

Ví dụ:

Body nhận được:

```json
{
    "name":"Tuan",
    "age":20
}
```

Sau khi parse:

```javascript
req.body
```

sẽ là

```javascript
{
    name: "Tuan",
    age: 20
}
```

Bạn có thể dùng:

```javascript
console.log(req.body.name);
```

Kết quả

```text
Tuan
```

---

## Nếu không có Body Parser

```javascript
req.body
```

=

```text
undefined
```

---

## express.json()

Đây là middleware dùng để đọc JSON.

```javascript
app.use(express.json());
```

Nó sẽ chạy trước mọi route.

Luồng xử lý:

```text
Request

↓

express.json()

↓

Đọc Body

↓

Biến thành Object

↓

req.body

↓

Route
```

---

## express.urlencoded()

Không phải dữ liệu nào cũng là JSON.

Form HTML thường gửi:

```text
application/x-www-form-urlencoded
```

Ví dụ

```html
<input name="username">

<input name="password">
```

Khi submit:

```text
username=tuan&password=123456
```

Middleware:

```javascript
app.use(express.urlencoded({ extended: true }));
```

sẽ chuyển thành

```javascript
req.body
```

=

```javascript
{
    username: "tuan",
    password: "123456"
}
```

---

# E - Example (Ví dụ)

## Ví dụ 1 - Không dùng Body Parser

```javascript
const express = require("express");

const app = express();

app.post("/login", (req, res) => {

    console.log(req.body);

    res.send("OK");

});

app.listen(3000);
```

Frontend gửi

```json
{
    "email":"abc@gmail.com"
}
```

Kết quả

```text
undefined
```

---

## Ví dụ 2 - Dùng express.json()

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.post("/login", (req, res) => {

    console.log(req.body);

    res.send("OK");

});

app.listen(3000);
```

Gửi

```json
{
    "email":"abc@gmail.com"
}
```

Kết quả

```javascript
{
    email: "abc@gmail.com"
}
```

---

Bạn có thể lấy

```javascript
app.post("/login", (req, res) => {

    console.log(req.body.email);

});
```

Kết quả

```text
abc@gmail.com
```

---

## Ví dụ 3 - Form HTML

HTML

```html
<form action="/login" method="POST">

<input name="username">

<input name="password">

<button>Login</button>

</form>
```

Express

```javascript
app.use(express.urlencoded({ extended: true }));

app.post("/login", (req, res) => {

    console.log(req.body);

});
```

Kết quả

```javascript
{
    username: "tuan",
    password: "123456"
}
```

---

## express.json() và express.urlencoded() có thể dùng cùng nhau

Đây là cấu hình phổ biến:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.use(express.urlencoded({ extended: true }));
```

Điều này cho phép Express xử lý cả:

* JSON từ API.
* Form HTML truyền thống.

---

# Luồng hoạt động

```text
Client

↓

POST /login

↓

Body
{
    email: "abc@gmail.com"
}

↓

express.json()

↓

Phân tích JSON

↓

req.body

↓

Route Handler

↓

console.log(req.body.email)

↓

abc@gmail.com
```

---

# R - Recap (Tóm tắt)

### Body Parser giải quyết vấn đề gì?

* Đọc dữ liệu trong phần **Body** của HTTP Request.
* Chuyển dữ liệu thành đối tượng JavaScript để dễ sử dụng.
* Gắn kết quả vào `req.body`.

### Hai middleware quan trọng

| Middleware                               | Dùng để đọc                                             |
| ---------------------------------------- | ------------------------------------------------------- |
| `express.json()`                         | Dữ liệu JSON (`application/json`)                       |
| `express.urlencoded({ extended: true })` | Dữ liệu form HTML (`application/x-www-form-urlencoded`) |

### Khi nào dùng?

* API nhận dữ liệu JSON → `express.json()`
* Form HTML gửi dữ liệu → `express.urlencoded()`
* Nhiều ứng dụng dùng cả hai để hỗ trợ cả API và form.

---

# Bài tập thực hành

Tạo một ứng dụng Express có hai route:

1. `POST /register`

   * Nhận JSON gồm `name`, `email`, `password`.
   * Trả về:

     ```json
     {
       "message": "Đăng ký thành công",
       "user": {
         "name": "...",
         "email": "..."
       }
     }
     ```

2. `POST /login`

   * Nhận dữ liệu từ một form HTML với `email` và `password`.
   * In `req.body` ra console và trả về `"Đăng nhập thành công"`.

Sau khi hoàn thành, hãy tự trả lời:

* Tại sao `req.body` là `undefined` khi chưa dùng middleware?
* Tại sao `express.json()` không đọc được dữ liệu từ form HTML?
* Vì sao `express.urlencoded()` không nên dùng để thay thế `express.json()` cho API JSON?

Nếu trả lời được ba câu hỏi này, bạn đã nắm được bản chất của Body Parser trong Express.
