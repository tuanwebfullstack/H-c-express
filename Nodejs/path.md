Rất đúng lộ trình. Trước khi học **HTTP Module**, bạn nên học **Path Module**. Hầu hết các dự án Express đều sử dụng `path`, đặc biệt khi làm việc với file, upload, static files và template engine.

---

# PACER Bài 6: Path Module (`path`)

## 🎯 Mục tiêu

Sau bài học này, bạn sẽ hiểu:

* `path` là gì.
* Tại sao không nên tự nối đường dẫn bằng chuỗi.
* Các hàm quan trọng:

  * `join()`
  * `resolve()`
  * `basename()`
  * `dirname()`
  * `extname()`
  * `parse()`
* `__dirname` và `__filename`.

---

# P - Phân rã kiến thức

## Bước 1: Path là gì?

`path` là một **Core Module** của Node.js.

Không cần cài:

```javascript
const path = require("path");
```

Nó giúp bạn **làm việc với đường dẫn (file path)**.

Ví dụ:

```text
C:\Users\Tuan\Desktop\project\index.js
```

hoặc

```text
/home/tuan/project/index.js
```

---

## Path giải quyết vấn đề gì?

Giả sử bạn muốn đọc:

```text
project

│

├── index.js

└── data

    └── user.json
```

Bạn có thể viết:

```javascript
fs.readFileSync("./data/user.json");
```

Điều này hoạt động khi bạn chạy đúng thư mục.

Nhưng nếu chạy ở thư mục khác:

```bash
node src/index.js
```

thì rất dễ lỗi.

Đó là lý do cần `path`.

---

# A - Ví dụ thực tế

Hãy tưởng tượng bạn gửi thư.

Bạn không ghi:

```text
Nhà màu xanh
```

Mà phải ghi:

```text
Số 10

Đường ABC

Quận XYZ

Hà Nội
```

Đường dẫn file cũng vậy.

`path` giúp tạo ra địa chỉ chính xác.

---

# Bước 2: join()

Đây là hàm dùng nhiều nhất.

```javascript
const path = require("path");

const file = path.join(
    "data",
    "users",
    "admin.json"
);

console.log(file);
```

Windows:

```text
data\users\admin.json
```

Linux/macOS:

```text
data/users/admin.json
```

👉 Bạn không cần quan tâm hệ điều hành.

---

## Tại sao không nối chuỗi?

Sai:

```javascript
const file = "data/" + "users/" + "admin.json";
```

Hoặc:

```javascript
const file = "data\\users\\admin.json";
```

Hai cách này có thể gặp vấn đề giữa Windows và Linux.

`path.join()` sẽ tự dùng dấu phân cách phù hợp.

---

# C - Minh họa

```text
"data"

↓

"path.join"

↓

"users"

↓

"admin.json"

↓

data/users/admin.json
```

---

# Bước 3: __dirname

Đây là biến đặc biệt của Node.js (trong CommonJS).

Ví dụ:

```text
project

│

└── src

    └── index.js
```

Trong `index.js`:

```javascript
console.log(__dirname);
```

Kết quả:

```text
C:\project\src
```

Đây là **thư mục chứa file hiện tại**.

---

# Bước 4: __filename

```javascript
console.log(__filename);
```

Kết quả:

```text
C:\project\src\index.js
```

Khác nhau:

```text
__dirname

↓

C:\project\src

__filename

↓

C:\project\src\index.js
```

---

# Bước 5: Kết hợp path và __dirname

Đây là cách viết phổ biến nhất.

```javascript
const path = require("path");

const file = path.join(
    __dirname,
    "data",
    "users.json"
);

console.log(file);
```

Kết quả:

```text
C:\project\src\data\users.json
```

Dù bạn chạy chương trình từ đâu, đường dẫn vẫn chính xác.

---

# Bước 6: basename()

Lấy tên file.

```javascript
const path = require("path");

console.log(
    path.basename("/home/user/test.txt")
);
```

Kết quả:

```text
test.txt
```

---

# Bước 7: dirname()

Lấy thư mục.

```javascript
console.log(
    path.dirname("/home/user/test.txt")
);
```

Kết quả:

```text
/home/user
```

---

# Bước 8: extname()

Lấy phần mở rộng.

```javascript
console.log(
    path.extname("photo.png")
);
```

Kết quả:

```text
.png
```

Ví dụ:

```text
index.js

↓

.js

report.pdf

↓

.pdf

music.mp3

↓

.mp3
```

---

# Bước 9: parse()

Phân tích toàn bộ đường dẫn.

```javascript
const info = path.parse(
    "/home/user/index.js"
);

console.log(info);
```

Kết quả:

```javascript
{
  root: "/",
  dir: "/home/user",
  base: "index.js",
  ext: ".js",
  name: "index"
}
```

---

# Bước 10: resolve()

Đây là hàm nhiều người nhầm với `join()`.

Ví dụ:

```javascript
path.resolve("data", "user.json");
```

Nếu thư mục hiện tại là:

```text
/home/project
```

Kết quả:

```text
/home/project/data/user.json
```

Khác với `join()`:

```javascript
path.join("data", "user.json");
```

Kết quả:

```text
data/user.json
```

---

## join() và resolve()

| `join()`                          | `resolve()`                     |
| --------------------------------- | ------------------------------- |
| Ghép các đoạn đường dẫn           | Tạo đường dẫn tuyệt đối         |
| Có thể trả về đường dẫn tương đối | Luôn trả về đường dẫn tuyệt đối |

Một cách dễ nhớ:

* **join** = "nối các mảnh đường dẫn".
* **resolve** = "xác định địa chỉ đầy đủ từ gốc".

---

# E - Bài tập thực hành

## Bài 1

In ra:

```javascript
console.log(__dirname);

console.log(__filename);
```

Quan sát sự khác nhau.

---

## Bài 2

Tạo:

```javascript
const path = require("path");

const file = path.join(
    __dirname,
    "data",
    "student.json"
);

console.log(file);
```

---

## Bài 3

Thử:

```javascript
console.log(
    path.basename("image.png")
);

console.log(
    path.extname("image.png")
);
```

Dự đoán trước khi chạy.

---

## Bài 4

Thử:

```javascript
console.log(
    path.parse("hello.txt")
);
```

Xem từng thuộc tính:

* base
* ext
* name

---

## Bài 5

So sánh:

```javascript
console.log(
    path.join("a", "b", "c")
);

console.log(
    path.resolve("a", "b", "c")
);
```

Quan sát điểm khác nhau.

---

# R - Tổng kết

## Sơ đồ tư duy

```text
path
│
├── join()
│      └── Ghép đường dẫn
│
├── resolve()
│      └── Đường dẫn tuyệt đối
│
├── basename()
│      └── Tên file
│
├── dirname()
│      └── Thư mục
│
├── extname()
│      └── Phần mở rộng
│
├── parse()
│      └── Phân tích đường dẫn
│
├── __dirname
│      └── Thư mục của file hiện tại
│
└── __filename
       └── Đường dẫn đầy đủ của file
```

---

# Những điều cần ghi nhớ

* `path` là **Core Module**, không cần cài bằng npm.
* Hãy ưu tiên dùng `path.join()` thay vì tự nối chuỗi để tạo đường dẫn.
* `__dirname` là thư mục chứa file hiện tại, còn `__filename` là đường dẫn đầy đủ của file.
* `path.resolve()` thường được dùng khi cần đường dẫn tuyệt đối.
* `path.parse()` rất hữu ích khi cần tách tên file, phần mở rộng và thư mục.

---

# 🧠 Kiểm tra kiến thức

1. Tại sao nên dùng `path.join()` thay vì `"folder/" + "file.txt"`?
2. `__dirname` và `__filename` khác nhau ở điểm nào?
3. `path.basename("report.pdf")` trả về gì?
4. `path.extname("photo.jpeg")` trả về gì?
5. Khi nào nên dùng `path.resolve()` thay vì `path.join()`?

---
