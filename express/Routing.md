
# PACER Bài 1: Routing trong Express
## 🎯 Mục tiêu

Sau bài này bạn sẽ hiểu:

* Routing là gì.
* Cách Express chọn route.
* Các HTTP Method (`GET`, `POST`, `PUT`, `DELETE`).
* Route Parameters (`:id`).
* Query Parameters.
* Thứ tự route ảnh hưởng như thế nào.

---

# P - Phân rã kiến thức

## Bước 1: Routing là gì?

Khi trình duyệt gửi một request:

```text
GET /about
```

Express phải quyết định:

> "Request này nên được xử lý bởi đoạn code nào?"

Việc đó gọi là **Routing**.

---

## Luồng hoạt động

```text
Browser

↓

GET /about

↓

Express Router

↓

Tìm route phù hợp

↓

Callback

↓

Response
```

---

# A - Ví dụ thực tế

Hãy tưởng tượng một bưu điện.

Mỗi lá thư có địa chỉ:

```text
123 Nguyễn Trãi
```

↓

Nhân viên nhìn địa chỉ

↓

Chuyển đúng người nhận

Express cũng làm như vậy.

```text
/about

↓

Route "/about"

↓

Callback
```

---

# Bước 2: Route đầu tiên

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Home");
});

app.listen(3000);
```

Nếu truy cập:

```text
http://localhost:3000/
```

Kết quả:

```text
Home
```

---

# Bước 3: Nhiều route

```javascript
app.get("/", (req, res) => {
    res.send("Home");
});

app.get("/about", (req, res) => {
    res.send("About");
});

app.get("/contact", (req, res) => {
    res.send("Contact");
});
```

Khi truy cập:

```text
GET /

↓

Home

GET /about

↓

About

GET /contact

↓

Contact
```

---

# C - Minh họa trực quan

```text
                Request

                   │

                   ▼

             GET /about

                   │

         Express kiểm tra route

                   │

     ┌─────────────┴─────────────┐
     │                           │
 GET "/" ?                  Không
     │
     ▼
 GET "/about" ?              Có
     │
     ▼
 Chạy callback
```

Express **kiểm tra route từ trên xuống dưới**.

---

# Bước 4: HTTP Method

Không chỉ có GET.

| Method | Ý nghĩa           |
| ------ | ----------------- |
| GET    | Lấy dữ liệu       |
| POST   | Tạo dữ liệu       |
| PUT    | Cập nhật toàn bộ  |
| PATCH  | Cập nhật một phần |
| DELETE | Xóa dữ liệu       |

---

Ví dụ:

```javascript
app.post("/users", (req, res) => {
    res.send("Tạo user");
});
```

```javascript
app.put("/users/1", (req, res) => {
    res.send("Cập nhật user");
});
```

```javascript
app.delete("/users/1", (req, res) => {
    res.send("Xóa user");
});
```

---

# REST API

Một API quản lý người dùng thường có dạng:

```text
GET     /users
```

↓

Lấy danh sách

```text
GET     /users/1
```

↓

Lấy người dùng số 1

```text
POST    /users
```

↓

Tạo mới

```text
PUT     /users/1
```

↓

Cập nhật

```text
DELETE  /users/1
```

↓

Xóa

---

# Bước 5: Route Parameters

Giả sử có:

```text
/users/1

/users/2

/users/100
```

Không thể viết:

```javascript
app.get("/users/1"...)

app.get("/users/2"...)

app.get("/users/3"...)
```

Express hỗ trợ:

```javascript
app.get("/users/:id", (req, res) => {

    console.log(req.params);

    res.send("User");
});
```

Truy cập:

```text
/users/25
```

Kết quả:

```javascript
{
    id: "25"
}
```

Muốn lấy ID:

```javascript
const id = req.params.id;
```

---

# Minh họa

```text
/users/99

↓

:id

↓

req.params

↓

{
   id: "99"
}
```

---

# Bước 6: Nhiều Parameters

```javascript
app.get("/users/:id/books/:bookId", (req, res) => {

    res.send(req.params);

});
```

Truy cập:

```text
/users/15/books/300
```

Kết quả:

```javascript
{
    id: "15",
    bookId: "300"
}
```

---

# Bước 7: Query Parameters

Ví dụ:

```text
/products?page=2&limit=10
```

Đây **không phải Route Parameter**.

Express đọc bằng:

```javascript
app.get("/products", (req, res) => {

    console.log(req.query);

});
```

Kết quả:

```javascript
{
    page: "2",
    limit: "10"
}
```

Lấy:

```javascript
req.query.page

req.query.limit
```

---

## So sánh

### Route Parameters

```text
/users/15
```

↓

```javascript
req.params.id
```

---

### Query Parameters

```text
/users?page=2
```

↓

```javascript
req.query.page
```

---

# Bước 8: Route Matching

Đây là lỗi rất nhiều người mới gặp.

Sai:

```javascript
app.get("/users/:id", ...);

app.get("/users/new", ...);
```

Nếu truy cập:

```text
/users/new
```

Express sẽ hiểu:

```text
id = "new"
```

và route `/users/new` sẽ không bao giờ được chạy.

---

Đúng:

```javascript
app.get("/users/new", ...);

app.get("/users/:id", ...);
```

Express luôn **ưu tiên route xuất hiện trước**.

---

# Bước 9: Route Not Found

Cuối cùng nên thêm:

```javascript
app.use((req, res) => {

    res.status(404).send("Not Found");

});
```

Nếu không có route nào khớp:

```text
↓

404
```

---

# E - Bài tập thực hành

## Bài 1

Tạo các route:

```text
/

↓

Home

/about

↓

About

/contact

↓

Contact
```

---

## Bài 2

Tạo:

```text
GET /products
```

Trả:

```text
Danh sách sản phẩm
```

---

## Bài 3

Tạo:

```text
GET /products/:id
```

Trả:

```text
Sản phẩm số 5
```

Nếu truy cập:

```text
/products/5
```

---

## Bài 4

Đọc query:

```text
/products?page=2&limit=5
```

Trả về:

```javascript
{
    page: "2",
    limit: "5"
}
```

---

## Bài 5

Viết:

```text
/users/new

/users/:id
```

Đổi thứ tự hai route và quan sát kết quả khi truy cập:

```text
/users/new
```

---

# R - Tổng kết

## Sơ đồ tư duy

```text
Routing
│
├── app.get()
│
├── app.post()
│
├── app.put()
│
├── app.patch()
│
├── app.delete()
│
├── req.params
│
├── req.query
│
└── Route Matching
```

---

# Điều cần ghi nhớ

### HTTP Method

```text
GET

↓

Lấy dữ liệu

POST

↓

Tạo dữ liệu

PUT

↓

Cập nhật

DELETE

↓

Xóa dữ liệu
```

---

### Parameters

```text
/users/:id

↓

req.params.id
```

---

### Query

```text
/users?page=2

↓

req.query.page
```

---

### Route Matching

```text
Express

↓

Đọc từ trên xuống

↓

Route đầu tiên khớp sẽ được thực thi
```

---

# 🧠 Kiểm tra kiến thức

1. Sự khác nhau giữa `req.params` và `req.query` là gì?
2. Vì sao `GET /users/new` có thể bị route `GET /users/:id` "bắt mất"?
3. Khi nào nên dùng Route Parameter, khi nào nên dùng Query Parameter?
4. `POST /users` và `GET /users` khác nhau như thế nào?
5. Tại sao nên đặt middleware 404 ở cuối file?

---

