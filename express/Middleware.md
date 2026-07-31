
## P — Preview: Bạn cần hiểu gì?

Sau bài này, bạn cần trả lời được 5 câu hỏi:

1. Middleware là gì?
2. Nó chạy **ở đâu và khi nào**?
3. `req`, `res`, `next` dùng để làm gì?
4. Vì sao phải gọi `next()`?
5. Middleware được dùng thế nào trong authentication, logging, validation và error handling?

Hãy hình dung Express như một **dây chuyền xử lý request**:

```text
Client
  │
  ▼
Middleware 1
  │
  ▼
Middleware 2
  │
  ▼
Middleware 3
  │
  ▼
Route Handler
  │
  ▼
Response
```

Ví dụ người dùng gửi:

```http
GET /users
```

Request có thể đi qua:

```text
Request
   ↓
Logger
   ↓
Check authentication
   ↓
Check permission
   ↓
/users controller
   ↓
Response
```

**Middleware chính là những "trạm kiểm tra/xử lý" nằm giữa request và response.**

---

# A — Acquire: Học kiến thức cốt lõi

## 1. Middleware là gì?

Middleware trong Express là một function có quyền truy cập vào:

```js
(req, res, next)
```

Ví dụ:

```js
const logger = (req, res, next) => {
    console.log("Có request!");
    next();
};
```

Đăng ký:

```js
app.use(logger);
```

Khi client gửi request:

```text
Client
   ↓
logger()
   ↓
Route
   ↓
Response
```

---

## 2. `req`, `res`, `next` là gì?

Đây là phần **quan trọng nhất**.

### `req`

Là request mà client gửi lên server.

Ví dụ:

```js
app.use((req, res, next) => {
    console.log(req.method);
    console.log(req.url);

    next();
});
```

Nếu client gửi:

```http
GET /users
```

thì:

```js
req.method // GET
req.url    // /users
```

---

### `res`

Là response mà server sẽ trả về client.

Ví dụ:

```js
app.use((req, res, next) => {
    res.json({
        message: "Hello"
    });
});
```

Middleware này **không gọi `next()`**, bởi vì nó đã kết thúc request bằng response.

---

### `next`

Đây là thứ bạn cần đặc biệt hiểu.

`next()` có nghĩa gần giống:

> "Middleware này xử lý xong rồi, chuyển request cho middleware tiếp theo."

Ví dụ:

```js
app.use((req, res, next) => {
    console.log("Middleware 1");

    next();
});

app.use((req, res, next) => {
    console.log("Middleware 2");

    next();
});
```

Request đi:

```text
Request
   ↓
Middleware 1
   ↓ next()
Middleware 2
   ↓ next()
Route
```

Nếu middleware 1 không gọi:

```js
next();
```

thì request sẽ **dừng tại đó**.

---

# C — Connect: Kết nối các khái niệm

Bây giờ hãy nhìn ví dụ thực tế.

```js
const express = require("express");

const app = express();

app.use((req, res, next) => {
    console.log("Middleware 1");

    next();
});

app.use((req, res, next) => {
    console.log("Middleware 2");

    next();
});

app.get("/", (req, res) => {
    res.send("Hello Express");
});

app.listen(3000);
```

Khi truy cập:

```text
http://localhost:3000/
```

Console:

```text
Middleware 1
Middleware 2
```

Sau đó:

```text
Hello Express
```

Luồng thực tế:

```text
GET /
 │
 ▼
app.use(Middleware 1)
 │
 │ next()
 ▼
app.use(Middleware 2)
 │
 │ next()
 ▼
app.get("/")
 │
 ▼
res.send()
```

Đây chính là **middleware pipeline**.

---

# E — Elaborate: Đi sâu hơn

## 1. Middleware có thể thay đổi `req`

Đây là một trong những ứng dụng cực kỳ quan trọng.

Ví dụ:

```js
app.use((req, res, next) => {
    req.user = {
        id: 1,
        name: "Tuan"
    };

    next();
});
```

Sau đó route có thể lấy:

```js
app.get("/profile", (req, res) => {
    res.json(req.user);
});
```

Kết quả:

```json
{
    "id": 1,
    "name": "Tuan"
}
```

Bạn có thể hiểu:

```text
Middleware
    │
    ├── xử lý request
    │
    └── thêm dữ liệu vào req
             │
             ▼
          Controller
```

Đây là nền tảng của **authentication**.

---

# 2. Middleware có thể kiểm tra request

Ví dụ kiểm tra đăng nhập:

```js
const authMiddleware = (req, res, next) => {

    const token = req.headers.authorization;

    if (!token) {
        return res.status(401).json({
            message: "Bạn chưa đăng nhập"
        });
    }

    next();
};
```

Sau đó:

```js
app.get(
    "/profile",
    authMiddleware,
    (req, res) => {
        res.json({
            message: "Thông tin cá nhân"
        });
    }
);
```

Luồng:

```text
GET /profile
      │
      ▼
authMiddleware
      │
      ├── Không có token
      │       ↓
      │    401 Response
      │
      └── Có token
              ↓
            next()
              ↓
          /profile
```

Đây chính là lý do middleware cực kỳ quan trọng trong backend.

---

# 3. Middleware có thể dùng cho toàn bộ app

```js
app.use(logger);
```

Middleware này áp dụng cho các request phía sau nó.

Ví dụ:

```js
app.use(logger);

app.get("/users", ...);

app.get("/products", ...);

app.get("/orders", ...);
```

Tất cả đều đi qua:

```text
logger
```

---

# 4. Middleware có thể dùng riêng cho một route

Ví dụ:

```js
app.get(
    "/admin",
    authMiddleware,
    adminMiddleware,
    (req, res) => {
        res.send("Admin page");
    }
);
```

Request phải đi qua:

```text
authMiddleware
      ↓
adminMiddleware
      ↓
Controller
```

Đây là cách bạn xây dựng:

* Authentication
* Authorization
* Validation
* Logging
* Rate limiting
* Permission checking

---

# 5. Middleware có thể có nhiều tầng

Ví dụ một API:

```js
app.post(
    "/users",
    authMiddleware,
    validateUser,
    createUser
);
```

Có thể hiểu:

```text
POST /users
     │
     ▼
Authentication
     │
     ▼
Validation
     │
     ▼
Controller
     │
     ▼
Database
```

Đây chính là kiến trúc mà bạn sẽ gặp rất nhiều khi làm Express thực tế.

---

# R — Recall: Không nhìn tài liệu, hãy tự trả lời

Hãy thử trả lời 5 câu này:

### Câu 1

Middleware này làm gì?

```js
app.use((req, res, next) => {
    console.log(req.method);
    next();
});
```

---

### Câu 2

Middleware này có vấn đề gì?

```js
app.use((req, res, next) => {
    console.log("Hello");
});
```

---

### Câu 3

Điều gì xảy ra ở đây?

```js
app.use((req, res, next) => {
    req.user = {
        id: 10
    };

    next();
});

app.get("/profile", (req, res) => {
    console.log(req.user);
    res.send("OK");
});
```

---

### Câu 4

Luồng xử lý của đoạn code này là gì?

```js
app.get(
    "/admin",
    authMiddleware,
    permissionMiddleware,
    (req, res) => {
        res.send("Admin");
    }
);
```

---

### Câu 5 — quan trọng nhất

Tại sao đoạn này:

```js
app.use((req, res, next) => {
    console.log("A");
});
```

có thể khiến request bị "treo"?

---

# Bài tập PACER

Đừng học middleware bằng cách đọc tiếp quá nhiều. Hãy làm bài này.

Viết một middleware:

```js
logger
```

sao cho khi gửi:

```http
GET /users
```

console xuất hiện:

```text
GET /users
```

Gợi ý:

```js
const logger = (req, res, next) => {
    // code của bạn
};
```

Sau đó dùng:

```js
app.use(logger);
```

và tạo:

```js
app.get("/users", (req, res) => {
    res.send("Users");
});
```

