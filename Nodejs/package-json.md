
# PACER Bài 3: package.json

## 🎯 Mục tiêu

Sau bài học này, bạn sẽ hiểu:

* `package.json` là gì.
* Vì sao nó bắt buộc trong hầu hết các dự án Node.js.
* Ý nghĩa của từng trường quan trọng.
* npm sử dụng file này như thế nào.
* Cách thêm package và script.

---

# P - Phân rã kiến thức

## Bước 1: package.json là gì?

Hãy tưởng tượng bạn xây một ngôi nhà.

Bạn cần một bản thiết kế:

* Nhà tên gì?
* Có bao nhiêu tầng?
* Dùng loại gạch nào?
* Dùng loại cửa nào?

Trong Node.js cũng vậy.

Mỗi project đều có một **bản mô tả**.

Đó chính là:

```text
package.json
```

Ví dụ:

```json
{
  "name": "my-app",
  "version": "1.0.0"
}
```

File này **không chứa mã nguồn**, mà chứa **thông tin về dự án**.

---

## package.json giống như "CMND" của dự án

```text
Project

│

├── index.js

├── routes/

├── controllers/

└── package.json
```

Trong đó `package.json` trả lời các câu hỏi:

* Dự án tên gì?
* Phiên bản?
* Chạy file nào?
* Dùng thư viện nào?
* Có những lệnh gì?

---

# A - Ví dụ thực tế

Giả sử bạn clone một project từ GitHub.

Bạn chỉ thấy:

```text
my-app/

│

├── package.json

└── src/
```

Không có `node_modules`.

Làm sao npm biết cần tải những package nào?

👉 Nó đọc:

```text
package.json
```

Ví dụ:

```json
{
  "dependencies": {
    "express": "^5.1.0",
    "dotenv": "^17.0.0"
  }
}
```

Sau đó:

```bash
npm install
```

npm sẽ:

```text
Đọc package.json

↓

Biết cần Express

↓

Biết cần dotenv

↓

Tải về

↓

Tạo node_modules
```

---

# Bước 2: Cấu trúc package.json

Một file đầy đủ có thể như sau:

```json
{
  "name": "blog-api",
  "version": "1.0.0",
  "description": "REST API",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "author": "Tuan",
  "license": "ISC",
  "dependencies": {
    "express": "^5.1.0"
  },
  "devDependencies": {
    "nodemon": "^3.1.0"
  }
}
```

Nhìn có vẻ nhiều, nhưng mỗi trường có một vai trò rõ ràng.

---

# Bước 3: name

```json
"name": "blog-api"
```

Tên của project.

Quy tắc:

* Viết thường.
* Không có khoảng trắng.
* Có thể dùng dấu `-`.

Ví dụ:

```text
blog-api

shopping-cart

express-demo
```

Không nên:

```text
My Project

Blog API
```

---

# Bước 4: version

```json
"version": "1.0.0"
```

Đây là phiên bản của project.

Theo quy ước **Semantic Versioning**:

```text
MAJOR.MINOR.PATCH
```

Ví dụ:

```text
1.0.0
```

Trong đó:

```text
1

↓

Major

↓

Thay đổi lớn

0

↓

Minor

↓

Thêm tính năng

0

↓

Patch

↓

Sửa lỗi
```

Ví dụ:

```text
1.0.0

↓

1.0.1

↓

Sửa bug

↓

1.1.0

↓

Thêm API mới

↓

2.0.0

↓

Thay đổi lớn
```

---

# Bước 5: main

```json
"main": "index.js"
```

Đây là file chính của project nếu package của bạn được project khác sử dụng.

Ví dụ:

```text
Project

│

├── index.js

├── app.js

└── package.json
```

Khi người khác cài package của bạn, Node sẽ mặc định đọc file được chỉ định ở `main`.

> Với các ứng dụng Express thông thường, trường này ít ảnh hưởng, nhưng vẫn nên khai báo rõ ràng.

---

# Bước 6: scripts

Đây là phần được dùng hằng ngày.

Ví dụ:

```json
"scripts": {
  "start": "node index.js"
}
```

Thay vì:

```bash
node index.js
```

Bạn chỉ cần:

```bash
npm start
```

Hoặc:

```json
"scripts": {
  "dev": "nodemon index.js"
}
```

Chạy:

```bash
npm run dev
```

npm sẽ thực hiện lệnh:

```text
nodemon index.js
```

---

# C - Code minh họa

Ví dụ:

```json
{
  "scripts": {
    "hello": "node hello.js"
  }
}
```

Tạo:

```javascript
// hello.js

console.log("Xin chào!");
```

Chạy:

```bash
npm run hello
```

Kết quả:

```text
Xin chào!
```

Điều này giúp bạn đặt tên ngắn gọn cho các lệnh dài hoặc phức tạp.

---

# Bước 7: dependencies

Ví dụ:

```json
"dependencies": {
    "express": "^5.1.0",
    "mongoose": "^8.0.0"
}
```

Nghĩa là:

Project **bắt buộc** cần:

* Express
* Mongoose

Khi chạy:

```bash
npm install
```

npm sẽ cài các package này.

---

# Bước 8: devDependencies

Ví dụ:

```json
"devDependencies": {
    "nodemon": "^3.1.0",
    "eslint": "^9.0.0"
}
```

Đây là các công cụ hỗ trợ **lập trình**, không phải để ứng dụng hoạt động khi triển khai.

Ví dụ:

* `nodemon`: tự khởi động lại server khi sửa code.
* `eslint`: kiểm tra lỗi và quy tắc viết mã.

---

# Bước 9: package.json và package-lock.json

Đây là điểm nhiều người mới dễ nhầm.

```text
package.json

↓

"Tôi cần Express phiên bản phù hợp"

package-lock.json

↓

"Tôi đã cài đúng phiên bản Express này
và toàn bộ các package phụ thuộc cụ thể"
```

Nói ngắn gọn:

| File              | Vai trò                           |
| ----------------- | --------------------------------- |
| package.json      | Khai báo những package cần dùng   |
| package-lock.json | Ghi lại chính xác những gì đã cài |

---

# E - Bài tập thực hành

### Bài 1

Tạo project:

```bash
mkdir demo-package
cd demo-package
npm init -y
```

Mở `package.json`.

Đọc từng trường.

---

### Bài 2

Thêm script:

```json
"scripts": {
    "hello": "node hello.js"
}
```

Tạo:

```javascript
console.log("Hello PACER");
```

Chạy:

```bash
npm run hello
```

---

### Bài 3

Cài Express:

```bash
npm install express
```

Quan sát:

* `dependencies` thay đổi như thế nào?
* Có xuất hiện `package-lock.json` không?
* Có thư mục `node_modules` không?

---

### Bài 4

Cài Nodemon:

```bash
npm install -D nodemon
```

Quan sát:

* `devDependencies` được thêm vào.
* `dependencies` không thay đổi.

---

# R - Tổng kết

## Sơ đồ tư duy

```text
package.json
│
├── name
│   └── Tên dự án
│
├── version
│   └── Phiên bản
│
├── description
│   └── Mô tả
│
├── main
│   └── File chính
│
├── scripts
│   └── Các lệnh npm
│
├── dependencies
│   └── Thư viện cần khi chạy
│
├── devDependencies
│   └── Công cụ hỗ trợ phát triển
│
├── author
│   └── Tác giả
│
└── license
    └── Giấy phép
```

## Những điều cần ghi nhớ

* `package.json` là **bản mô tả của dự án**, không phải nơi chứa mã nguồn.
* npm dựa vào `package.json` để biết **cần cài những package nào**.
* `scripts` giúp bạn tạo các lệnh ngắn gọn như `npm start` hay `npm run dev`.
* `dependencies` chứa các package cần để ứng dụng hoạt động, còn `devDependencies` chứa công cụ phục vụ phát triển.
* `package.json` và `package-lock.json` bổ sung cho nhau: một file mô tả nhu cầu, file còn lại ghi lại chính xác trạng thái đã cài.

---

## Câu hỏi tự kiểm tra

1. Vì sao chỉ cần có `package.json`, người khác vẫn có thể cài lại toàn bộ thư viện của dự án?
2. Sự khác nhau giữa `dependencies` và `devDependencies` là gì?
3. `scripts` giúp giải quyết vấn đề gì? Hãy lấy một ví dụ.
4. Khi chạy `npm install express`, những file hoặc thư mục nào trong dự án sẽ thay đổi?
5. Trong `package.json`, trường `main` có ý nghĩa gì và khi nào nó quan trọng?

Nếu bạn trả lời được 5 câu hỏi này mà không cần nhìn tài liệu, bạn đã nắm vững kiến thức nền tảng về `package.json` và sẵn sàng học **Module System (CommonJS & ES Modules)** – chủ đề rất quan trọng trước khi đi vào ExpressJS.
