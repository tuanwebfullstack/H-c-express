

# PACER Bài 1: Node.js hoạt động như thế nào?

## Mục tiêu

Sau bài này bạn sẽ hiểu:

* Node.js thực chất là gì
* Tại sao JavaScript chạy được ngoài trình duyệt
* Event Loop hoạt động như thế nào
* Tại sao Node.js lại xử lý được hàng nghìn request

---

# Bước 1: Node.js không phải là ngôn ngữ

Nhiều người mới học nghĩ:

> Node.js = JavaScript

Điều này **không đúng**.

Thực tế:

```
JavaScript
     ↓
V8 Engine (Google Chrome)
     ↓
Node.js Runtime
```

Trong đó:

* **JavaScript** là ngôn ngữ.
* **V8** là engine dùng để dịch JavaScript thành mã máy.
* **Node.js** là một runtime bao quanh V8 và bổ sung rất nhiều API.

Ví dụ:

```js
console.log("Hello");
```

V8 hiểu được.

Nhưng:

```js
const fs = require("fs");
```

`fs` không phải của JavaScript.

Đó là API do **Node.js cung cấp**.

---

# Bước 2: Node.js gồm những gì?

Hãy tưởng tượng Node.js giống một chiếc xe.

```
             Node.js
+----------------------------------+
| JavaScript Runtime               |
|                                  |
|  +----------------------------+  |
|  | V8 Engine                  |  |
|  +----------------------------+  |
|                                  |
|  File System API                |
|  HTTP API                       |
|  Buffer                         |
|  Stream                         |
|  Event Loop                     |
|  Thread Pool                    |
|  Module System                  |
+----------------------------------+
```

Nghĩa là:

Node.js =

* V8
* Event Loop
* Thread Pool
* File System
* HTTP
* DNS
* TCP
* Stream
* Buffer
* ...

---

# Bước 3: Tại sao cần Event Loop?

Giả sử có chương trình:

```js
console.log("A");

đọc file 5GB

console.log("B");
```

Nếu chạy tuần tự:

```
A

(chờ 20 giây)

B
```

CPU bị đứng.

Điều này rất tệ với server.

---

Node.js chọn cách khác.

```
A

→ giao việc đọc file

B

đợi file đọc xong mới xử lý
```

Kết quả:

```
A
B
(file xong)
```

Đó chính là **Non-blocking I/O**.

---

# Bước 4: Event Loop là gì?

Đây là trái tim của Node.js.

Nó lặp liên tục:

```
while(true){

    Có callback nào hoàn thành chưa?

    Có → chạy callback

    Không → tiếp tục chờ

}
```

Đó gọi là **Event Loop**.

---

Ví dụ:

```js
console.log("1");

setTimeout(() => {
    console.log("2");
}, 0);

console.log("3");
```

Kết quả:

```
1
3
2
```

Tại sao?

Event Loop hoạt động như sau:

```
Call Stack

console.log(1)
↓

setTimeout()

↓

Timer được giao cho hệ thống

↓

console.log(3)

↓

Call Stack rỗng

↓

Event Loop lấy callback

↓

console.log(2)
```

---

# Bước 5: Call Stack

Node chỉ chạy **một đoạn JavaScript tại một thời điểm**.

Ví dụ:

```js
function A(){
    B();
}

function B(){
    C();
}

function C(){
    console.log("Hi");
}

A();
```

Call Stack:

```
A

↓

B

↓

C

↓

console.log
```

Sau khi chạy xong:

```
console.log

↓

C

↓

B

↓

A
```

Giống như một chồng đĩa.

---

# Bước 6: Nếu chỉ có một luồng thì sao đọc file được?

Đây là điều khiến nhiều người nhầm.

Node.js:

> JavaScript chạy **một luồng (single thread)**.

Nhưng:

Việc đọc file KHÔNG chạy trên luồng JavaScript.

Nó được giao cho:

```
libuv Thread Pool
```

Ví dụ:

```js
fs.readFile(...)
```

Quy trình:

```
JS Thread

↓

Event Loop

↓

libuv

↓

Worker Thread

↓

Đọc file

↓

Xong

↓

Callback

↓

Event Loop

↓

JS Thread
```

Do đó, JavaScript không bị "đứng" trong lúc đọc file.

---

# Bước 7: HTTP Server hoạt động

Ví dụ:

```js
const http = require("http");

http.createServer((req, res) => {

    res.end("Hello");

});
```

Quy trình:

```
Client

↓

TCP

↓

Node

↓

Event Loop

↓

Callback

↓

res.end()
```

Khi 1000 người truy cập:

Node KHÔNG tạo 1000 thread.

Nó:

```
Request 1

↓

Queue

↓

Request 2

↓

Queue

↓

Request 3

↓

Queue

↓

Event Loop xử lý từng callback rất nhanh
```

Đó là lý do Node phù hợp với ứng dụng có nhiều kết nối đồng thời (I/O-bound).

---

# Bước 8: Minh họa toàn bộ

```
                 Client

                    │

                    ▼

           HTTP Request đến Node

                    │

                    ▼

             Event Loop nhận event

                    │

        ┌───────────┴───────────┐
        │                       │
        ▼                       ▼
 Không cần I/O            Cần I/O (đọc file, DB...)
        │                       │
        ▼                       ▼
 Xử lý ngay              Giao cho libuv/OS
        │                       │
        │                Thực hiện ở nền
        │                       │
        └───────────┬───────────┘
                    ▼
          Callback được đưa vào hàng đợi
                    ▼
         Event Loop lấy callback khi Call Stack rỗng
                    ▼
              Trả Response cho Client
```

---

# Những điều quan trọng cần ghi nhớ

* **Node.js không phải là ngôn ngữ**, mà là runtime để chạy JavaScript ngoài trình duyệt.
* **V8** thực thi mã JavaScript.
* **JavaScript trong Node chạy trên một luồng chính (main thread)**.
* **Event Loop** giúp xử lý các tác vụ bất đồng bộ mà không chặn luồng JavaScript.
* Các tác vụ I/O như đọc file, truy vấn mạng... thường được hệ điều hành hoặc **libuv** xử lý, sau đó callback mới được đưa trở lại Event Loop.
* Node.js đạt hiệu quả cao với các ứng dụng nhiều I/O vì không cần tạo một thread cho mỗi request.

---

# Bài tập kiểm tra

1. Tại sao `fs.readFile()` không làm Node.js bị "đứng" trong lúc đọc file?

2. Vì sao đoạn mã sau in ra `1 3 2` thay vì `1 2 3`?

   ```js
   console.log(1);

   setTimeout(() => {
     console.log(2);
   }, 0);

   console.log(3);
   ```

3. Phân biệt vai trò của **V8**, **Node.js**, **Event Loop** và **libuv**.
4. Tại sao Node.js không cần tạo một thread cho mỗi HTTP request như nhiều mô hình server truyền thống?
