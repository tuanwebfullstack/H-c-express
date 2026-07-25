Rất đúng thứ tự. Sau khi hiểu **Node.js**, **npm** và **package.json**, bước tiếp theo là **Module**. Đây là nền tảng để hiểu cách ExpressJS được tổ chức thành nhiều file thay vì viết tất cả trong `index.js`.

Mình sẽ dạy theo **PACER**.

---

# PACER Bài 4: Module trong Node.js

## 🎯 Mục tiêu

Sau bài này bạn sẽ hiểu:

* Module là gì.
* Tại sao cần module.
* `require()` hoạt động như thế nào.
* `module.exports` và `exports`.
* Cách chia code thành nhiều file.

---

# P - Phân rã kiến thức

## Bước 1: Module là gì?

Giả sử bạn viết chương trình như sau:

```javascript
console.log("Đăng nhập");

console.log("Đăng ký");

console.log("Giỏ hàng");

console.log("Thanh toán");

console.log("Quản lý người dùng");

// ... thêm vài nghìn dòng nữa
```

Sau vài tháng:

```
index.js

↓

5000 dòng code
```

Lúc này:

* Khó tìm lỗi.
* Khó sửa.
* Khó làm việc nhóm.

---

Node.js giải quyết bằng **Module**.

Mỗi chức năng đặt trong một file riêng.

```
project

│

├── auth.js

├── cart.js

├── payment.js

├── user.js

└── index.js
```

Mỗi file chính là **một module**.

---

# A - Ví dụ thực tế

Hãy tưởng tượng bạn đang viết một cuốn sách.

❌ Không tốt:

```
1 file

↓

1000 trang
```

✅ Tốt hơn:

```
Chương 1

Chương 2

Chương 3

...
```

Module cũng giống như các chương của cuốn sách.

---

# Bước 2: Tạo module đầu tiên

Tạo file:

```
math.js
```

```javascript
function add(a, b) {
    return a + b;
}
```

Nếu chạy:

```bash
node math.js
```

Không có gì xảy ra vì bạn chưa gọi hàm.

Quan trọng hơn, file này **chưa chia sẻ** hàm `add` cho file khác.

---

# Bước 3: module.exports

Muốn file khác sử dụng được, bạn phải **export**.

```javascript
function add(a, b) {
    return a + b;
}

module.exports = add;
```

Ở đây:

```
add

↓

module.exports

↓

Cho phép file khác sử dụng
```

---

# Bước 4: require()

Tạo:

```
index.js
```

```javascript
const add = require("./math");

console.log(add(2, 3));
```

Kết quả:

```
5
```

Điều gì xảy ra?

```
index.js

↓

require("./math")

↓

Node đọc file math.js

↓

Lấy module.exports

↓

Trả về biến add
```

---

# C - Minh họa trực quan

```
math.js

↓

module.exports

↓

Node

↓

require()

↓

index.js
```

Hay chi tiết hơn:

```
math.js
┌─────────────────────┐
│ function add(){}    │
│                     │
│ module.exports=add  │
└──────────┬──────────┘
           │
           ▼
      require("./math")
           │
           ▼
index.js nhận được hàm add
```

---

# Bước 5: Export nhiều hàm

Ví dụ:

```javascript
function add(a, b) {
    return a + b;
}

function sub(a, b) {
    return a - b;
}

module.exports = {
    add,
    sub
};
```

Import:

```javascript
const math = require("./math");

console.log(math.add(5, 2));

console.log(math.sub(5, 2));
```

Kết quả:

```
7

3
```

Hoặc dùng destructuring:

```javascript
const { add, sub } = require("./math");

console.log(add(1, 2));

console.log(sub(5, 1));
```

---

# Bước 6: exports là gì?

Bạn sẽ thấy:

```javascript
exports.add = add;

exports.sub = sub;
```

Hoặc:

```javascript
module.exports = {
    add,
    sub
};
```

Hai cách này thường cho kết quả giống nhau.

Vì ban đầu Node tạo:

```javascript
exports = module.exports;
```

Nên:

```javascript
exports.add = add;
```

tương đương:

```javascript
module.exports.add = add;
```

---

⚠️ Nhưng hãy cẩn thận.

Sai:

```javascript
exports = add;
```

Điều này **không hoạt động**.

Vì bạn chỉ đổi biến `exports`, còn `module.exports` vẫn giữ giá trị cũ.

Hãy ưu tiên dùng:

```javascript
module.exports = ...
```

để tránh nhầm lẫn.

---

# Bước 7: Module Cache

Đây là kiến thức rất quan trọng.

Tạo:

```javascript
console.log("Math loaded");

module.exports = {};
```

`index.js`

```javascript
require("./math");

require("./math");

require("./math");
```

Kết quả:

```
Math loaded
```

chỉ in **một lần**.

---

Tại sao?

Node lưu module vào bộ nhớ.

```
Lần đầu

↓

Đọc file

↓

Cache

↓

Lần sau

↓

Lấy từ RAM
```

Không cần đọc lại file.

Điều này giúp Node chạy nhanh hơn.

---

# Bước 8: Module tích hợp sẵn

Không phải module nào cũng cần cài bằng npm.

Ví dụ:

```javascript
const fs = require("fs");
```

```
fs

↓

Node Core Module
```

Hay:

```javascript
const http = require("http");
```

```
http

↓

Node Core Module
```

Các module này đã đi kèm Node.js.

---

# Bước 9: Module bên thứ ba

Ví dụ:

```bash
npm install express
```

Sau đó:

```javascript
const express = require("express");
```

Lúc này Node tìm:

```
node_modules

↓

express

↓

module.exports

↓

Trả về Express
```

---

# E - Bài tập thực hành

## Bài 1

Tạo:

```
calculator.js
```

```javascript
function multiply(a, b) {
    return a * b;
}

module.exports = multiply;
```

`index.js`

```javascript
const multiply = require("./calculator");

console.log(multiply(5, 6));
```

Kết quả mong đợi:

```
30
```

---

## Bài 2

Export nhiều hàm:

```javascript
module.exports = {
    add,
    sub,
    multiply,
    divide
};
```

Import bằng destructuring:

```javascript
const { add, divide } = require("./calculator");
```

---

## Bài 3

Thêm:

```javascript
console.log("Loaded");
```

vào đầu `calculator.js`.

Trong `index.js`:

```javascript
require("./calculator");

require("./calculator");

require("./calculator");
```

Quan sát:

```
Loaded
```

chỉ xuất hiện **một lần** để hiểu cơ chế **Module Cache**.

---

# R - Tổng kết

## Sơ đồ tư duy

```text
Module
│
├── Mỗi file là một module
│
├── module.exports
│      │
│      └── Xuất dữ liệu
│
├── require()
│      │
│      └── Nhập dữ liệu
│
├── exports
│
├── Core Module
│      ├── fs
│      ├── http
│      └── path
│
├── Third-party Module
│      └── express
│
└── Module Cache
```

---

# Những điều cần nhớ

* Mỗi file `.js` trong Node.js là một **module**.
* Dùng `module.exports` để **xuất** hàm, đối tượng hoặc giá trị.
* Dùng `require()` để **nhập** module từ file khác hoặc từ `node_modules`.
* `exports` chỉ là một tham chiếu đến `module.exports`; khi mới học, nên ưu tiên dùng `module.exports` để tránh lỗi.
* Node.js chỉ tải một module **một lần** rồi lưu vào **Module Cache**, nên các lần `require()` sau sẽ dùng lại phiên bản đã nạp.

---
