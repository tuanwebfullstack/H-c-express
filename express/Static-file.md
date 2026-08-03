

#  Static File trong Express

## P - Problem (Vấn đề)

Bạn đã upload được ảnh vào thư mục `uploads`.

Cấu trúc dự án:

```text
project/
│
├── uploads/
│   ├── avatar.png
│   └── cat.jpg
│
├── app.js
```

Nếu mở trình duyệt và truy cập:

```text
http://localhost:3000/uploads/avatar.png
```

Bạn sẽ nhận được:

```text
Cannot GET /uploads/avatar.png
```

**Tại sao?**

Vì Express mặc định **không cho phép truy cập trực tiếp các file trên ổ đĩa**. Điều này giúp tránh việc người dùng truy cập nhầm các file nhạy cảm của server.

---

# A - Analyze (Phân tích)

Khi có request:

```text
GET /uploads/avatar.png
```

Express sẽ kiểm tra các route:

```javascript
app.get(...)
app.post(...)
app.put(...)
```

Nếu không có route nào khớp, Express trả về:

```text
404 Not Found
```

Mặc dù file `uploads/avatar.png` tồn tại trên ổ cứng, Express **không tự động gửi file đó về cho client**.

---

# C - Concept (Kiến thức)

## Static File là gì?

**Static file** là những file được gửi nguyên vẹn từ server đến client, không cần xử lý thêm.

Ví dụ:

* HTML
* CSS
* JavaScript
* Hình ảnh (`.png`, `.jpg`)
* Video
* Font

Ví dụ:

```text
Browser
    │
GET /logo.png
    │
    ▼
Express
    │
Đọc logo.png
    │
    ▼
Trả file về Browser
```

---

## Middleware `express.static()`

Express cung cấp middleware:

```javascript
express.static()
```

để chia sẻ một thư mục chứa các static file.

Ví dụ:

```javascript
app.use(express.static("public"));
```

Khi đó:

```text
public/
    logo.png
```

Có thể truy cập bằng:

```text
http://localhost:3000/logo.png
```

---

# E - Example (Ví dụ)

## Ví dụ 1: Chia sẻ thư mục `public`

Cấu trúc:

```text
project/
│
├── public/
│   ├── logo.png
│   ├── style.css
│   └── app.js
```

Code:

```javascript
const express = require("express");

const app = express();

app.use(express.static("public"));

app.listen(3000);
```

Truy cập:

```text
http://localhost:3000/logo.png
```

Express sẽ:

```text
Request
    │
    ▼
public/logo.png
    │
    ▼
Gửi file về trình duyệt
```

---

## Ví dụ 2: Chia sẻ thư mục `uploads`

Đây là trường hợp rất phổ biến sau khi upload file.

```javascript
app.use(express.static("uploads"));
```

Thư mục:

```text
uploads/
    avatar.png
```

Truy cập:

```text
http://localhost:3000/avatar.png
```

---

## Ví dụ 3: Gắn đường dẫn ảo (Virtual Path)

Thay vì:

```text
/avatar.png
```

bạn có thể dùng:

```javascript
app.use("/images", express.static("uploads"));
```

Khi đó:

```text
uploads/
    avatar.png
```

sẽ được truy cập bằng:

```text
/images/avatar.png
```

Sơ đồ:

```text
Browser

/images/avatar.png

        │

        ▼

uploads/avatar.png
```

Đây là cách được khuyến khích vì không làm lộ tên thật của thư mục trên server.

---

# R - Recap

Sau bài này bạn đã hiểu:

* ✅ Static file là gì.
* ✅ Tại sao Express không tự cho truy cập file.
* ✅ Vai trò của `express.static()`.
* ✅ Cách chia sẻ thư mục `public`.
* ✅ Cách hiển thị ảnh trong `uploads`.
* ✅ Cách tạo đường dẫn ảo như `/images`.

---

## Bài tập

1. Tạo thư mục:

```text
public/
```

2. Thêm:

```text
style.css
logo.png
```

3. Cấu hình:

```javascript
app.use(express.static("public"));
```

4. Kiểm tra:

* `http://localhost:3000/style.css`
* `http://localhost:3000/logo.png`

5. **Nâng cao:** Đổi sang:

```javascript
app.use("/assets", express.static("public"));
```

và thử truy cập:

```text
http://localhost:3000/assets/logo.png
```

### Câu hỏi kiểm tra

**Nếu viết:**

```javascript
app.use("/images", express.static("uploads"));
```

và trong thư mục `uploads` có file `cat.png`, thì URL nào sẽ hiển thị đúng ảnh?

A. `http://localhost:3000/uploads/cat.png`

B. `http://localhost:3000/images/cat.png`

C. `http://localhost:3000/cat.png`

Hãy trả lời trước khi sang bài tiếp theo về **Router trong Express**.
