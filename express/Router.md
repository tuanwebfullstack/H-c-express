
# PACER - Router trong Express

## P - Problem (Vấn đề)

### Nếu không có Router

Giả sử bạn xây dựng website bán hàng.

Ban đầu chỉ có 2 trang:

```text
GET /
GET /about
```

Bạn có thể viết:

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
    res.send("Home");
});

app.get("/about", (req, res) => {
    res.send("About");
});
```

Không vấn đề gì.

---

Sau một thời gian...

Website có thêm

```text
/users
/users/:id
/users/login
/users/register

/products
/products/:id
/products/create

/orders
/orders/:id

/admin
/admin/users
/admin/products
```

File `app.js` lúc này sẽ thành:

```javascript
app.get(...)
app.post(...)
app.delete(...)
app.put(...)

app.get(...)
app.post(...)
...
```

500 dòng.

1000 dòng.

2000 dòng.

Mọi route đều nằm trong một file.

Muốn sửa route User phải kéo chuột rất lâu.

Muốn tìm route Product cũng rất khó.

👉 Đây chính là vấn đề.

---

## A - Analogy (Ví dụ đời thực)

Hãy tưởng tượng một bệnh viện.

Nếu mọi bệnh nhân đều xếp hàng ở một quầy:

```
Tim mạch
Da liễu
Tai mũi họng
Nhi khoa
Sản khoa
```

thì sẽ cực kỳ hỗn loạn.

Thay vào đó bệnh viện chia thành:

```
Khoa Tim

Khoa Da Liễu

Khoa Nhi

Khoa Sản
```

Mỗi khoa xử lý bệnh nhân của mình.

---

Express Router cũng vậy.

Thay vì:

```
app xử lý mọi URL
```

ta chia thành

```
User Router

Product Router

Order Router

Admin Router
```

Mỗi Router chỉ quản lý một nhóm URL.

---

## C - Concept (Khái niệm)

### Router là gì?

Router là một mini Express App dùng để quản lý một nhóm route.

Ví dụ:

```
User Router

/users
/users/login
/users/register
/users/:id
```

đều đặt trong một file.

---

Product Router

```
/products
/products/:id
/products/create
```

ở file khác.

---

Sau đó Express ghép tất cả Router lại.

```
App

├── User Router

├── Product Router

├── Order Router
```

---

### Cách tạo Router

```javascript
const express = require("express");

const router = express.Router();
```

Đây không phải App.

Mà là một Router.

---

Bạn có thể dùng:

```javascript
router.get()

router.post()

router.put()

router.delete()
```

giống hệt `app`.

Ví dụ

```javascript
router.get("/", (req, res) => {
    res.send("Danh sách user");
});

router.get("/:id", (req, res) => {
    res.send("Chi tiết user");
});
```

---

## E - Example (Ví dụ)

### Bước 1

Cấu trúc thư mục

```
project/

app.js

routes/

    user.js
```

---

### app.js

```javascript
const express = require("express");
const app = express();

const userRouter = require("./routes/user");

app.use("/users", userRouter);

app.listen(3000);
```

---

### routes/user.js

```javascript
const express = require("express");

const router = express.Router();

router.get("/", (req, res) => {
    res.send("Danh sách user");
});

router.get("/login", (req, res) => {
    res.send("Trang login");
});

router.get("/:id", (req, res) => {
    res.send(`User ${req.params.id}`);
});

module.exports = router;
```

---

Khi chạy:

```
GET /users
```

Express làm gì?

```
App

↓

app.use("/users", userRouter)

↓

chuyển request sang userRouter

↓

router.get("/")
```

---

Nếu:

```
GET /users/login
```

Express:

```
App

↓

/users

↓

User Router

↓

/login

↓

Trả về login
```

---

Nếu:

```
GET /users/10
```

Express:

```
App

↓

User Router

↓

/:id

↓

id = 10
```

---

### Router chỉ nhìn phần URL còn lại

Ví dụ

```javascript
app.use("/users", userRouter);
```

Request

```
GET /users/login
```

Router sẽ nhận:

```
/login
```

không phải

```
/users/login
```

Đây là điểm rất nhiều người mới học bị nhầm.

---

## R - Recap (Tóm tắt)

### Router giúp giải quyết vấn đề gì?

* Chia nhỏ các route theo chức năng.
* Giảm độ dài của `app.js`.
* Dễ bảo trì và mở rộng.
* Cho phép nhiều người cùng phát triển các phần khác nhau.

### Luồng xử lý của Router

```text
Client
    │
    ▼
app.js
    │
app.use("/users", userRouter)
    │
    ▼
userRouter
    │
router.get("/")
    │
    ▼
Controller (hoặc xử lý trực tiếp)
    │
    ▼
Response
```

---

# Bài tập thực hành

Tạo cấu trúc như sau:

```
project/

app.js

routes/
    user.js
    product.js
```

Trong `user.js`, tạo các route:

* `GET /` → "Danh sách người dùng"
* `GET /login` → "Đăng nhập"
* `GET /:id` → "Chi tiết người dùng"

Trong `product.js`, tạo các route:

* `GET /` → "Danh sách sản phẩm"
* `GET /create` → "Tạo sản phẩm"
* `GET /:id` → "Chi tiết sản phẩm"

Trong `app.js`, gắn:

```javascript
app.use("/users", userRouter);
app.use("/products", productRouter);
```
