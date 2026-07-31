
# PACER Bài 10: Request & Response trong Express

## 🎯 Mục tiêu

Sau bài này bạn phải hiểu được:

```text
Client
   ↓
Request
   ↓
Express
   ↓
Response
   ↓
Client
```

Cụ thể:

* `req` là gì?
* `res` là gì?
* `req.params`
* `req.query`
* `req.body`
* `req.headers`
* `req.method`
* `req.url`
* `res.send()`
* `res.json()`
* `res.status()`
* `res.sendStatus()`
* `res.set()`

---

# P — Phân rã kiến thức

## 1. Request là gì?

**Request** là dữ liệu mà client gửi đến server.

Ví dụ bạn truy cập:

```text
GET /users/15?page=2
```

Client gửi cho server một request chứa rất nhiều thông tin:

```text
Request
│
├── Method
│      └── GET
│
├── URL
│      └── /users/15?page=2
│
├── Headers
│
├── Parameters
│      └── id = 15
│
├── Query
│      └── page = 2
│
└── Body
```

Trong Express, bạn truy cập request thông qua:

```javascript
req
```

---

# 2. Response là gì?

Sau khi server xử lý request, server cần trả kết quả cho client.

Đó là **Response**.

```text
Client

   │
   │ Request
   ▼

Express

   │
   │ Response
   ▼

Client
```

Trong Express:

```javascript
res
```

đại diện cho Response.

---

# A — Analogy

Hãy tưởng tượng bạn đi mua hàng.

Bạn nói với nhân viên:

> "Cho tôi một chai nước."

Đó là **Request**.

Nhân viên:

1. Nhận yêu cầu.
2. Tìm sản phẩm.
3. Đưa chai nước cho bạn.

Đó là **Response**.

Trong web:

```text
Bạn
 ↓
Request
 ↓
Server
 ↓
Xử lý
 ↓
Response
 ↓
Bạn
```

---

# C — Code

## 3. Request cơ bản

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {

    console.log(req);

    res.send("Hello");

});

app.listen(3000);
```

Nhưng `req` có rất nhiều thông tin.

Chúng ta sẽ tách từng phần.

---

# 4. `req.method`

Lấy HTTP Method.

```javascript
app.get("/", (req, res) => {

    console.log(req.method);

    res.send("Hello");

});
```

Kết quả:

```text
GET
```

Nếu:

```javascript
app.post("/users", (req, res) => {

    console.log(req.method);

    res.send("Created");

});
```

Kết quả:

```text
POST
```

---

# 5. `req.url`

Lấy URL mà client gửi.

```javascript
app.get("/users", (req, res) => {

    console.log(req.url);

    res.send("Users");

});
```

Nếu truy cập:

```text
/users
```

Kết quả:

```text
/users
```

Nếu:

```text
/users?page=2
```

thì:

```text
/users?page=2
```

---

# 6. `req.params`

Đây là phần bạn đã gặp trong Routing.

Ví dụ:

```javascript
app.get("/users/:id", (req, res) => {

    console.log(req.params);

    res.send("User");

});
```

Truy cập:

```text
/users/15
```

Kết quả:

```javascript
{
    id: "15"
}
```

Lấy ID:

```javascript
const id = req.params.id;
```

---

## ⚠️ Lưu ý

`req.params.id` là **string**.

```javascript
const id = req.params.id;

console.log(typeof id);
```

Kết quả:

```text
string
```

Nếu cần số:

```javascript
const id = Number(req.params.id);
```

---

# 7. `req.query`

Query nằm sau dấu `?`.

Ví dụ:

```text
/products?page=2&limit=10
```

Code:

```javascript
app.get("/products", (req, res) => {

    console.log(req.query);

    res.send("Products");

});
```

Kết quả:

```javascript
{
    page: "2",
    limit: "10"
}
```

Lấy riêng:

```javascript
const page = req.query.page;

const limit = req.query.limit;
```

---

# Params vs Query

Đây là phần bạn **phải phân biệt**.

### Params

```text
/users/15
```

```javascript
req.params.id
```

Kết quả:

```text
15
```

---

### Query

```text
/users?page=2
```

```javascript
req.query.page
```

Kết quả:

```text
2
```

Có thể nhớ:

```text
/users/:id
       ↑
     params
```

và:

```text
/users?page=2
       ↑
     query
```

---

# 8. `req.headers`

HTTP Request có Headers.

Ví dụ:

```javascript
app.get("/", (req, res) => {

    console.log(req.headers);

    res.send("Hello");

});
```

Bạn có thể thấy:

```text
host
user-agent
accept
accept-language
...
```

Lấy một header:

```javascript
const userAgent = req.headers["user-agent"];
```

---

# 9. Authorization Header

Sau này khi học JWT, bạn sẽ thường gặp:

```text
Authorization: Bearer abc123
```

Lấy bằng:

```javascript
const auth = req.headers.authorization;
```

Ví dụ:

```javascript
app.get("/profile", (req, res) => {

    const auth = req.headers.authorization;

    console.log(auth);

    res.send("Profile");

});
```

Đây là một trong những cách server nhận thông tin xác thực từ client.

---

# 10. `req.body`

Đây là phần **cực kỳ quan trọng** khi xây API.

Ví dụ client gửi:

```json
{
    "name": "Tuan",
    "age": 20
}
```

Server muốn lấy:

```javascript
req.body
```

Nhưng trước tiên Express phải được cấu hình để đọc JSON.

```javascript
app.use(express.json());
```

Sau đó:

```javascript
app.post("/users", (req, res) => {

    console.log(req.body);

    res.send("Created");

});
```

Client gửi:

```json
{
    "name": "Tuan",
    "age": 20
}
```

Server nhận:

```javascript
{
    name: "Tuan",
    age: 20
}
```

---

# 11. Tại sao phải `express.json()`?

Đây là chỗ bạn nên hiểu bản chất.

Request có thể chứa:

```text
Body

↓

JSON
```

Express cần middleware để phân tích JSON.

```javascript
app.use(express.json());
```

Luồng:

```text
Client

↓

JSON Request

↓

express.json()

↓

Phân tích JSON

↓

req.body

↓

Route
```

Đây cũng chính là lý do **Middleware** bạn sắp học rất quan trọng.

---

# 12. Response — `res.send()`

Đây là cách đơn giản nhất để trả dữ liệu.

```javascript
app.get("/", (req, res) => {

    res.send("Hello Express");

});
```

Có thể gửi HTML:

```javascript
res.send("<h1>Hello</h1>");
```

Hoặc object:

```javascript
res.send({
    name: "Tuan"
});
```

---

# 13. `res.json()`

Khi xây REST API, bạn sẽ dùng rất nhiều:

```javascript
app.get("/users", (req, res) => {

    res.json({
        name: "Tuan",
        age: 20
    });

});
```

Response:

```json
{
    "name": "Tuan",
    "age": 20
}
```

Thông thường:

```text
API
 ↓
JSON
```

nên:

```javascript
res.json(...)
```

rất phổ biến.

---

# 14. `res.status()`

Đặt HTTP Status Code.

Ví dụ:

```javascript
res.status(200).json({
    message: "Success"
});
```

Hoặc:

```javascript
res.status(404).json({
    message: "User not found"
});
```

Có thể chain:

```javascript
res
    .status(201)
    .json({
        message: "Created"
    });
```

---

# 15. Một số Status Code quan trọng

|  Code | Ý nghĩa                        |
| ----: | ------------------------------ |
| `200` | Thành công                     |
| `201` | Tạo thành công                 |
| `204` | Thành công nhưng không có body |
| `400` | Request không hợp lệ           |
| `401` | Chưa xác thực                  |
| `403` | Không có quyền                 |
| `404` | Không tìm thấy                 |
| `500` | Lỗi server                     |

Ví dụ:

```javascript
app.get("/users/:id", (req, res) => {

    const user = null;

    if (!user) {
        return res.status(404).json({
            message: "User not found"
        });
    }

});
```

---

# 16. `return res...`

Đây là một thói quen rất quan trọng.

Ví dụ:

```javascript
if (!user) {

    return res.status(404).json({
        message: "User not found"
    });

}
```

Tại sao có `return`?

Để kết thúc function ngay tại đó.

Nếu không:

```javascript
if (!user) {

    res.status(404).json({
        message: "Not found"
    });

}

res.json(user);
```

Bạn có thể gặp lỗi kiểu:

```text
Cannot set headers after they are sent
```

vì server đã gửi response rồi nhưng code vẫn cố gửi thêm response.

---

# 17. `res.sendStatus()`

Có thể viết:

```javascript
res.sendStatus(404);
```

Nó sẽ gửi:

```text
404 Not Found
```

Nhưng khi xây API thực tế, bạn thường muốn response có thông tin rõ ràng hơn:

```javascript
res.status(404).json({
    message: "User not found"
});
```

Cách này hữu ích hơn cho frontend.

---

# 18. `res.set()`

Thiết lập Response Header.

```javascript
res.set("X-App-Version", "1.0");

res.send("Hello");
```

Hoặc:

```javascript
res.set({
    "X-App-Version": "1.0",
    "X-Author": "Tuan"
});

res.send("Hello");
```

---

# 19. Kết hợp Request + Response

Bây giờ hãy xây một API đơn giản.

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.get("/users/:id", (req, res) => {

    const id = req.params.id;

    const page = req.query.page;

    res.json({
        id,
        page
    });

});

app.post("/users", (req, res) => {

    const user = req.body;

    res.status(201).json({
        message: "User created",
        user
    });

});

app.listen(3000);
```

---

## Request 1

```text
GET /users/15?page=2
```

Express lấy:

```javascript
req.params.id
```

→ `"15"`

và:

```javascript
req.query.page
```

→ `"2"`

Response:

```json
{
    "id": "15",
    "page": "2"
}
```

---

## Request 2

```text
POST /users
```

Body:

```json
{
    "name": "Tuan",
    "age": 20
}
```

Express lấy:

```javascript
req.body
```

Response:

```json
{
    "message": "User created",
    "user": {
        "name": "Tuan",
        "age": 20
    }
}
```

---

# E — Exercise

## Bài 1: Lấy Params

Tạo:

```text
GET /products/:id
```

Nếu client gọi:

```text
/products/100
```

Response:

```json
{
    "productId": "100"
}
```

---

## Bài 2: Lấy Query

Tạo:

```text
GET /products?page=2&limit=10
```

Response:

```json
{
    "page": "2",
    "limit": "10"
}
```

---

## Bài 3: Lấy Body

Tạo:

```text
POST /users
```

Body:

```json
{
    "name": "Tuan",
    "email": "tuan@gmail.com"
}
```

Response:

```json
{
    "message": "User created",
    "user": {
        "name": "Tuan",
        "email": "tuan@gmail.com"
    }
}
```

---

## Bài 4: Status Code

Nếu không tìm thấy user:

```json
{
    "message": "User not found"
}
```

và Status Code:

```text
404
```

---

# R — Tổng kết

Hãy ghi nhớ sơ đồ này:

```text
                    HTTP Request
                         │
        ┌────────────────┼────────────────┐
        │                │                │
      params           query            body
        │                │                │
        ▼                ▼                ▼
   req.params       req.query        req.body
                         │
                         ▼
                    Express
                         │
                         ▼
                      res
                         │
              ┌──────────┼──────────┐
              │          │          │
           status       json       send
              │          │          │
              └──────────┼──────────┘
                         ▼
                  HTTP Response
```

## 4 thứ bạn phải nhớ

### ① URL động

```text
/users/:id
```

→

```javascript
req.params.id
```

### ② Query

```text
/users?page=2
```

→

```javascript
req.query.page
```

### ③ Body

```json
{
    "name": "Tuan"
}
```

→

```javascript
req.body
```

với:

```javascript
app.use(express.json());
```

### ④ Response

```javascript
res.status(200).json(data);
```

---

# 🧠 Kiểm tra PACER

Không nhìn tài liệu, hãy thử trả lời:

**1.** Client gửi:

```text
GET /products/50?page=2
```

`50` nằm ở đâu?

`2` nằm ở đâu?

---

**2.** Client gửi:

```text
POST /users
```

Body:

```json
{
    "name": "An",
    "age": 20
}
```

Bạn lấy `name` bằng câu lệnh nào?

---

**3.** Viết một route:

```text
GET /users/:id
```

nếu không tìm thấy user thì trả:

```json
{
    "message": "User not found"
}
```

với status `404`.

---

**4.** Tại sao cần:

```javascript
app.use(express.json());
```

trước khi sử dụng:

```javascript
req.body
```

---
