
# PACER Bài 2: npm (Node Package Manager)

## Mục tiêu

Sau bài này, bạn sẽ hiểu:

* npm là gì.
* Package là gì.
* `package.json` dùng để làm gì.
* Cách cài, gỡ và cập nhật package.
* Ý nghĩa của `dependencies`, `devDependencies`.
* Vai trò của `package-lock.json`.
* Tại sao không nên đẩy `node_modules` lên Git.

---

# P - Phân rã kiến thức

## Bước 1: npm là gì?

Rất nhiều người nhầm:

> npm = Node.js

Thực tế:

```text
Node.js
│
├── V8
├── Event Loop
├── fs
├── http
└── npm
```

**npm** là trình quản lý package đi kèm khi cài Node.js.

Nó giúp bạn:

* Cài thư viện.
* Gỡ thư viện.
* Cập nhật thư viện.
* Quản lý phiên bản.
* Chạy các script của dự án.

---

## Package là gì?

Package là một thư viện chứa mã nguồn để giải quyết một vấn đề cụ thể.

Ví dụ:

| Package  | Chức năng            |
| -------- | -------------------- |
| express  | Tạo web server       |
| axios    | Gửi HTTP request     |
| mongoose | Làm việc với MongoDB |
| bcrypt   | Mã hóa mật khẩu      |
| dotenv   | Đọc biến môi trường  |

Thay vì tự viết hàng nghìn dòng mã, bạn chỉ cần cài package phù hợp.

---

# A - Ví dụ thực tế

Hãy tưởng tượng bạn xây một ngôi nhà.

Bạn không tự làm:

* gạch,
* xi măng,
* cửa,
* mái ngói.

Bạn mua chúng từ nhà cung cấp.

Lập trình cũng vậy.

```text
Dự án của bạn

↓

Cần Express

↓

npm tải Express

↓

Đưa vào project
```

npm giống như một "siêu thị" chứa hàng triệu package.

---

# Bước 2: Khởi tạo dự án

Tạo thư mục:

```bash
mkdir my-app
```

Di chuyển vào:

```bash
cd my-app
```

Khởi tạo:

```bash
npm init
```

npm sẽ hỏi:

```text
Project name:

Version:

Description:

Author:
```

Hoặc nhanh hơn:

```bash
npm init -y
```

Lúc này sẽ xuất hiện:

```text
package.json
```

---

# Bước 3: package.json

Đây là "chứng minh nhân dân" của dự án.

Ví dụ:

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "Demo project",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  }
}
```

Ý nghĩa:

| Thuộc tính      | Chức năng                   |
| --------------- | --------------------------- |
| name            | Tên dự án                   |
| version         | Phiên bản                   |
| main            | File chạy chính             |
| scripts         | Các lệnh tắt                |
| dependencies    | Thư viện cần khi chạy       |
| devDependencies | Thư viện phục vụ phát triển |

---

# Bước 4: Cài package

Ví dụ cài Express:

```bash
npm install express
```

Hoặc:

```bash
npm i express
```

Sau khi cài sẽ có:

```text
project

│

├── node_modules

├── package.json

└── package-lock.json
```

---

# C - Code minh họa

Sau khi cài Express:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("Hello");
});

app.listen(3000);
```

Nếu chưa cài:

```bash
npm install express
```

thì chương trình sẽ báo lỗi:

```text
Cannot find module 'express'
```

---

# Bước 5: node_modules

Đây là nơi npm lưu toàn bộ package.

Ví dụ:

```text
node_modules/

express/

body-parser/

accepts/

cookie/

...
```

Có thể lên tới hàng chục nghìn file.

Bạn **không nên chỉnh sửa trực tiếp** các file trong `node_modules`, vì chúng sẽ bị ghi đè khi cài đặt lại.

---

# Bước 6: package-lock.json

Nhiều người nghĩ file này không quan trọng.

Thực tế đây là file cực kỳ quan trọng.

Ví dụ:

Express phụ thuộc:

```text
express

↓

body-parser

↓

bytes

↓

debug

↓

ms
```

Nếu không khóa phiên bản:

```text
Máy A

↓

debug 4.3.1

Máy B

↓

debug 4.4.0
```

Hai máy có thể chạy khác nhau.

`package-lock.json` ghi lại chính xác phiên bản của toàn bộ cây phụ thuộc để mọi người cài đặt giống nhau.

---

# Bước 7: dependencies và devDependencies

Có hai loại package phổ biến.

### dependencies

Là package cần khi ứng dụng chạy.

Ví dụ:

```bash
npm install express
```

Trong `package.json`:

```json
"dependencies": {
    "express": "^5.1.0"
}
```

---

### devDependencies

Chỉ phục vụ quá trình phát triển.

Ví dụ:

* nodemon
* eslint
* prettier

Cài:

```bash
npm install -D nodemon
```

Hoặc:

```bash
npm install --save-dev nodemon
```

Trong `package.json`:

```json
"devDependencies": {
    "nodemon": "^3.0.0"
}
```

---

# Bước 8: Scripts

Thay vì gõ:

```bash
node index.js
```

Bạn có thể thêm:

```json
"scripts": {
    "start": "node index.js"
}
```

Sau đó chạy:

```bash
npm start
```

Hoặc với `nodemon`:

```json
"scripts": {
    "dev": "nodemon index.js"
}
```

Chạy:

```bash
npm run dev
```

---

# Bước 9: Gỡ package

```bash
npm uninstall express
```

Hoặc:

```bash
npm remove express
```

---

# Bước 10: Cài lại project

Giả sử bạn clone project từ GitHub.

Bạn chỉ thấy:

```text
package.json

package-lock.json
```

Không có:

```text
node_modules
```

Chỉ cần:

```bash
npm install
```

npm sẽ đọc:

```text
package.json

↓

package-lock.json

↓

tải đúng các package

↓

tạo node_modules
```

---

# E - Bài tập thực hành

### Bài 1

Tạo thư mục mới:

```bash
mkdir npm-demo
cd npm-demo
```

Khởi tạo:

```bash
npm init -y
```

Quan sát file `package.json`.

---

### Bài 2

Cài Express:

```bash
npm install express
```

Kiểm tra:

* Có thư mục `node_modules`.
* Có file `package-lock.json`.
* `package.json` xuất hiện mục `dependencies`.

---

### Bài 3

Cài `nodemon`:

```bash
npm install -D nodemon
```

Quan sát sự khác biệt giữa:

* `dependencies`
* `devDependencies`

---

### Bài 4

Thêm script:

```json
"scripts": {
  "dev": "nodemon index.js"
}
```

Chạy:

```bash
npm run dev
```

Nếu sửa `index.js`, chương trình sẽ tự khởi động lại.

---

# R - Tổng kết

Hãy ghi nhớ sơ đồ sau:

```text
npm
│
├── Quản lý package
│
├── npm init
│      │
│      ▼
│  package.json
│
├── npm install
│      │
│      ├── node_modules
│      └── package-lock.json
│
├── dependencies
│
├── devDependencies
│
└── scripts
```

## Những điều cần nhớ

* **npm** là trình quản lý package đi kèm Node.js.
* **Package** là thư viện giúp bạn tái sử dụng mã nguồn thay vì tự viết mọi thứ.
* **`package.json`** mô tả thông tin dự án, các package và các script.
* **`package-lock.json`** khóa phiên bản của toàn bộ cây phụ thuộc để đảm bảo mọi môi trường cài đặt giống nhau.
* **`node_modules`** chứa toàn bộ package đã cài và thường không được đưa lên Git.
* **`dependencies`** dành cho package cần khi ứng dụng chạy, còn **`devDependencies`** dành cho công cụ phát triển.
