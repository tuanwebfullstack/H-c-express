
# PACER Bài 5: File System (fs)

## 🎯 Mục tiêu

Sau bài học này, bạn sẽ hiểu:

* `fs` là gì.
* Sự khác nhau giữa Sync và Async.
* Cách đọc, ghi, tạo, xóa file.
* Cách tạo và quản lý thư mục.
* Khi nào nên dùng Sync và Async.

---

# P - Phân rã kiến thức

## Bước 1: fs là gì?

`fs` là viết tắt của **File System**.

Nó là **Core Module** của Node.js.

Nghĩa là **không cần cài npm**.

Chỉ cần:

```javascript
const fs = require("fs");
```

là có thể sử dụng.

---

## fs giúp làm gì?

Hãy tưởng tượng ổ cứng của bạn.

```text
📁 Project

│

├── index.js

├── data.txt

├── users.json

└── images/
```

Node có thể:

* Đọc file
* Ghi file
* Xóa file
* Đổi tên file
* Tạo thư mục
* Xóa thư mục
* Kiểm tra file có tồn tại không

---

# A - Ví dụ thực tế

Giả sử bạn xây ứng dụng ghi chú.

Người dùng nhập:

```text
Học Node.js
```

Bạn muốn lưu vào:

```text
notes.txt
```

Node sẽ:

```text
Người dùng

↓

Node

↓

fs

↓

Ổ cứng

↓

notes.txt
```

---

# Bước 2: Đọc file (Sync)

Tạo:

```text
hello.txt
```

Nội dung:

```text
Xin chào Node.js
```

Đọc:

```javascript
const fs = require("fs");

const data = fs.readFileSync("hello.txt", "utf8");

console.log(data);
```

Kết quả:

```text
Xin chào Node.js
```

---

## Điều gì xảy ra?

```text
readFileSync()

↓

Đọc file

↓

Đợi đọc xong

↓

Trả dữ liệu

↓

Chạy tiếp
```

Từ **Sync** nghĩa là **đồng bộ**.

Nó sẽ **chặn chương trình**.

---

Ví dụ:

```javascript
console.log("A");

const data = fs.readFileSync("hello.txt", "utf8");

console.log(data);

console.log("B");
```

Kết quả:

```text
A

Xin chào Node.js

B
```

---

# Bước 3: Đọc file (Async)

```javascript
const fs = require("fs");

fs.readFile("hello.txt", "utf8", (err, data) => {
    console.log(data);
});

console.log("Done");
```

Kết quả:

```text
Done

Xin chào Node.js
```

---

## Điều gì xảy ra?

```text
readFile()

↓

Giao việc đọc file

↓

Tiếp tục chạy

↓

Đọc xong

↓

Callback
```

Đây là **Non-blocking I/O** mà bạn đã học ở bài Event Loop.

---

# C - Minh họa trực quan

## Sync

```text
JS

↓

Đọc file

↓

Chờ

↓

Đọc xong

↓

Chạy tiếp
```

---

## Async

```text
JS

↓

Giao đọc file

↓

Chạy tiếp

↓

File xong

↓

Callback
```

---

# Bước 4: Ghi file

```javascript
const fs = require("fs");

fs.writeFileSync(
    "hello.txt",
    "Đây là nội dung mới"
);
```

Sau khi chạy:

```text
hello.txt

↓

Đây là nội dung mới
```

⚠️ `writeFileSync()` sẽ **ghi đè** nội dung cũ.

---

## Ghi bất đồng bộ

```javascript
fs.writeFile(
    "hello.txt",
    "Hello",
    (err) => {
        console.log("Đã ghi");
    }
);
```

---

# Bước 5: Thêm nội dung

Muốn thêm cuối file:

```javascript
fs.appendFileSync(
    "hello.txt",
    "\nNode.js"
);
```

Kết quả:

```text
Hello

Node.js
```

---

# Bước 6: Xóa file

```javascript
fs.unlinkSync("hello.txt");
```

Sau khi chạy:

```text
hello.txt

↓

Đã bị xóa
```

---

# Bước 7: Đổi tên file

```javascript
fs.renameSync(
    "hello.txt",
    "note.txt"
);
```

Kết quả:

```text
hello.txt

↓

note.txt
```

---

# Bước 8: Tạo thư mục

```javascript
fs.mkdirSync("uploads");
```

Kết quả:

```text
Project

│

├── uploads
```

---

# Bước 9: Đọc thư mục

```javascript
const files = fs.readdirSync(".");

console.log(files);
```

Kết quả:

```text
[
  'index.js',
  'package.json',
  'uploads'
]
```

---

# Bước 10: Kiểm tra file tồn tại

```javascript
const exists = fs.existsSync("hello.txt");

console.log(exists);
```

Kết quả:

```text
true
```

hoặc:

```text
false
```

---

# Bước 11: Sync vs Async

Đây là phần quan trọng nhất.

## Sync

```javascript
const data = fs.readFileSync("big.txt");
```

```text
↓

Đợi

↓

Đợi

↓

Đợi

↓

Xong
```

Không làm việc khác được.

---

## Async

```javascript
fs.readFile("big.txt", callback);
```

```text
↓

Giao việc

↓

Làm việc khác

↓

Đọc xong

↓

Callback
```

---

## Khi nào dùng?

### Sync

* Script nhỏ.
* Tool chạy một lần.
* Bài tập.
* Khởi tạo cấu hình lúc ứng dụng vừa khởi động.

---

### Async

* Web Server.
* Express.
* API.
* Upload file.
* Đọc ảnh.
* Đọc video.

Trong Express, **ưu tiên Async** để tránh làm chậm các request khác.

---

# E - Bài tập thực hành

## Bài 1

Tạo:

```text
student.txt
```

Nội dung:

```text
Nguyễn Văn A
```

Đọc bằng:

```javascript
fs.readFileSync(...)
```

---

## Bài 2

Đổi sang:

```javascript
fs.readFile(...)
```

Quan sát thứ tự:

```javascript
console.log("Start");

fs.readFile(...);

console.log("End");
```

Dự đoán kết quả trước khi chạy.

---

## Bài 3

Tạo:

```javascript
fs.writeFileSync(
    "hello.txt",
    "Hello PACER"
);
```

Mở file xem nội dung.

---

## Bài 4

Thêm:

```javascript
fs.appendFileSync(
    "hello.txt",
    "\nNode.js"
);
```

Kiểm tra file.

---

## Bài 5

Tạo thư mục:

```javascript
fs.mkdirSync("images");
```

Sau đó dùng:

```javascript
fs.readdirSync(".");
```

để xem danh sách file và thư mục trong project.

---

# R - Tổng kết

## Sơ đồ tư duy

```text
fs
│
├── Đọc file
│      ├── readFileSync()
│      └── readFile()
│
├── Ghi file
│      ├── writeFileSync()
│      └── writeFile()
│
├── Thêm nội dung
│      └── appendFile()
│
├── Xóa file
│      └── unlink()
│
├── Đổi tên
│      └── rename()
│
├── Tạo thư mục
│      └── mkdir()
│
├── Đọc thư mục
│      └── readdir()
│
└── Kiểm tra tồn tại
       └── existsSync()
```

---

# Những điều cần ghi nhớ

* `fs` là **Core Module**, không cần cài bằng npm.
* Mỗi API thường có hai phiên bản:

  * **Sync** (`readFileSync`, `writeFileSync`...): dễ hiểu nhưng chặn luồng JavaScript.
  * **Async** (`readFile`, `writeFile`...): không chặn luồng, phù hợp cho server và ứng dụng thực tế.
* Trong Express hoặc các ứng dụng xử lý nhiều request, hãy ưu tiên dùng **API bất đồng bộ**.

---

# 🧠 Kiểm tra kiến thức

1. Vì sao `fs` không cần cài bằng `npm install`?
2. `readFileSync()` khác `readFile()` ở điểm nào?
3. Điều gì xảy ra nếu `writeFileSync()` ghi vào một file đã có nội dung?
4. Tại sao dùng `readFileSync()` trong một route Express có thể làm giảm hiệu năng?
5. Hãy viết quy trình xử lý của `fs.readFile()` theo thứ tự từ lúc gọi hàm đến khi callback được thực thi.

---
