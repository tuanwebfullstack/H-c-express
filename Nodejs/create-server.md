
# PACER Bài 8: Tạo Server với Express

## 🎯 Mục tiêu

Sau bài này bạn sẽ hiểu:

* Express là gì.
* Tại sao Express ra đời.
* Cách tạo server.
* `app.listen()`
* Route đầu tiên.
* Chu trình Request → Response trong Express.

---

# P - Phân rã kiến thức

## Bước 1: Express là gì?

Express là một **framework** được xây dựng trên HTTP Module của Node.js.

Mối quan hệ:

```text
Application

↓

Express

↓

HTTP Module

↓

TCP

↓

Internet
```

Nghĩa là:

Express **không thay thế HTTP**.

Nó chỉ giúp bạn viết code dễ hơn.

---

## Node thuần

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
    res.end("Hello");
});

server.listen(3000);
```

---

## Express

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Hello");
});

app.listen(3000);
```

Dễ đọc hơn rất nhiều.

---

# A - Ví dụ thực tế

Hãy tưởng tượng:

Node.js giống như bạn phải **tự xây một chiếc xe**.

Express giống như **mua chiếc xe đã lắp sẵn**.

Bạn vẫn lái xe.

Nhưng không cần tự chế tạo động cơ.

---

# Bước 2: Cài Express

Khởi tạo project:

```bash
npm init -y
```

Cài:

```bash
npm install express
```

Kiểm tra:

```text
project

│

├── node_modules

├── package.json

├── package-lock.json

└── index.js
```

---

# Bước 3: Import Express

```javascript
const express = require("express");
```

Điều gì xảy ra?

```text
node_modules

↓

express

↓

module.exports

↓

require()

↓

Biến express
```

---

# Bước 4: Tạo ứng dụng

```javascript
const app = express();
```

Lúc này:

```text
express()

↓

Tạo Express Application

↓

Biến app
```

`app` là đối tượng chính của Express.

Hầu hết mọi thao tác đều thông qua `app`.

---

# Bước 5: Route đầu tiên

```javascript
app.get("/", (req, res) => {

    res.send("Xin chào Express");

});
```

Ý nghĩa:

```text
Nếu

Method = GET

URL = /

↓

Chạy callback
```

---

# C - Minh họa

```text
Browser

↓

GET /

↓

Express

↓

app.get("/")

↓

Callback

↓

res.send()

↓

Browser
```

---

# Bước 6: Khởi động Server

```javascript
app.listen(3000);
```

Hoặc:

```javascript
app.listen(3000, () => {

    console.log("Server chạy");

});
```

Mở:

```text
http://localhost:3000
```

Kết quả:

```text
Xin chào Express
```

---

# Bước 7: Code hoàn chỉnh

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Xin chào Express");
});

app.listen(3000, () => {
    console.log("Server đang chạy tại http://localhost:3000");
});
```

Chạy:

```bash
node index.js
```

---

# Điều gì xảy ra?

```text
node index.js

↓

Express tạo HTTP Server

↓

listen(3000)

↓

Chờ Request

↓

Có Request

↓

So khớp Route

↓

Callback

↓

Response
```

---

# Bước 8: Thêm nhiều Route

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

Kết quả:

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

# Bước 9: req và res

Bạn đã gặp chúng ở HTTP Module.

Express chỉ **mở rộng** chúng.

Ví dụ:

```javascript
app.get("/about", (req, res) => {

    console.log(req.method);

    console.log(req.url);

    res.send("About");

});
```

Kết quả:

```text
GET

/about
```

---

# Bước 10: res.send()

HTTP Module:

```javascript
res.end("Hello");
```

Express:

```javascript
res.send("Hello");
```

`send()` thông minh hơn.

Ví dụ:

```javascript
res.send("<h1>Hello</h1>");
```

Express tự đặt:

```text
Content-Type

↓

text/html
```

---

Hoặc:

```javascript
res.send({
    name: "Tuan"
});
```

Express tự chuyển thành JSON.

---

# So sánh

Node:

```javascript
res.setHeader(
    "Content-Type",
    "application/json"
);

res.end(
    JSON.stringify({
        name: "Node"
    })
);
```

---

Express:

```javascript
res.send({
    name: "Node"
});
```

---

# E - Bài tập thực hành

## Bài 1

Tạo project mới.

```bash
mkdir express-demo

cd express-demo

npm init -y

npm install express
```

---

## Bài 2

Tạo:

```javascript
const express = require("express");

const app = express();

app.listen(3000);
```

Chạy thử.

---

## Bài 3

Thêm route:

```javascript
app.get("/", (req, res) => {

    res.send("Home");

});
```

Kiểm tra trên trình duyệt.

---

## Bài 4

Thêm:

```javascript
/about

↓

About

/contact

↓

Contact
```

---

## Bài 5

Thử:

```javascript
res.send({
    framework: "Express",
    version: "5"
});
```

Quan sát trình duyệt.

---

# R - Tổng kết

## Sơ đồ tư duy

```text
Express
│
├── express()
│      │
│      ▼
│     app
│
├── app.get()
│
├── app.post()
│
├── app.listen()
│
├── req
│
└── res.send()
```

---

# Mối quan hệ giữa Node và Express

```text
Client

↓

Express

↓

HTTP Module

↓

Node.js

↓

Operating System
```

Express **không thay thế** Node.js.

Express sử dụng HTTP Module của Node.js để xử lý request và response, đồng thời cung cấp API dễ dùng hơn.

---

# 🧠 Kiểm tra kiến thức

1. Tại sao vẫn phải cài Node.js dù đã dùng Express?
2. `express()` trả về đối tượng gì? Đối tượng đó dùng để làm gì?
3. `app.get("/", ...)` có ý nghĩa gì?
4. `app.listen(3000)` thực hiện nhiệm vụ gì?
5. `res.send()` tiện lợi hơn `res.end()` ở những điểm nào?

---
