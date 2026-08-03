
Upload File trong Express

## P - Problem (Vấn đề)

Giả sử bạn đang làm website học lập trình.

Người dùng muốn:

* Upload avatar
* Upload ảnh bài viết
* Upload CV
* Upload project zip

Frontend gửi một file lên server.

Câu hỏi là:

> Express nhận file đó bằng cách nào?

Ví dụ:

```
Browser
    │
    │ gửi file
    ▼
Express
    │
    ▼
Lưu file vào server
```

---

# A - Analyze (Phân tích)

Thông thường Express chỉ đọc được

```json
{
   "name":"Tuan",
   "age":20
}
```

với

```javascript
express.json()
```

Nhưng file thì không phải JSON.

Khi upload file, trình duyệt gửi

```
multipart/form-data
```

Ví dụ request:

```
POST /upload

Tên:
Tuan

Avatar:
avatar.png
```

Express **không hiểu multipart/form-data** nếu không có middleware hỗ trợ. Vì vậy cần dùng **Multer**, middleware phổ biến để xử lý upload file trong Express. ([Express.js][1])

---

# C - Concept (Kiến thức)

## Multer là gì?

Multer là middleware giúp Express:

* đọc file
* lưu file
* đặt tên file
* giới hạn kích thước
* kiểm tra loại file

Sau khi Multer xử lý:

```
req.file
```

hoặc

```
req.files
```

sẽ xuất hiện.

Ví dụ

```
Client
    │
    ▼
Multer
    │
    ▼
req.file
```

---

## Các kiểu upload

### Upload 1 file

```
upload.single("avatar")
```

Kết quả

```
req.file
```

---

### Upload nhiều file

```
upload.array("images")
```

Kết quả

```
req.files
```

---

### Upload nhiều field

```
avatar
gallery
document
```

Dùng

```
upload.fields(...)
```

---

# E - Example (Ví dụ)

## Bước 1

Cài Multer

```bash
npm install multer
```

---

## Bước 2

Tạo thư mục

```
project

uploads/

app.js
```

---

## Bước 3

Cấu hình

```javascript
const express = require("express");
const multer = require("multer");

const app = express();

const upload = multer({
    dest: "uploads/"
});
```

Ý nghĩa:

```
dest

↓

uploads/

↓

mọi file sẽ lưu ở đây
```

---

## Bước 4

Tạo API

```javascript
app.post(
    "/upload",
    upload.single("avatar"),
    (req, res) => {

        console.log(req.file);

        res.send("Upload thành công");
    }
);
```

Lưu ý: `"avatar"` phải trùng với tên field file mà frontend gửi lên, nếu không Multer sẽ không lấy được file. ([Express.js][1])

---

## Bước 5

Gửi request bằng Postman

Body

```
form-data
```

Thêm

| KEY    | TYPE | VALUE     |
| ------ | ---- | --------- |
| avatar | File | image.png |

Sau khi gửi

```
uploads/

↓

image được lưu
```

---

## req.file có gì?

Ví dụ

```javascript
console.log(req.file);
```

Kết quả

```javascript
{
  fieldname: 'avatar',
  originalname: 'cat.png',
  encoding: '7bit',
  mimetype: 'image/png',
  destination: 'uploads/',
  filename: '8f4b6e...',
  path: 'uploads/8f4b6e...',
  size: 18321
}
```

Một số thuộc tính quan trọng:

* `originalname`: tên file gốc.
* `mimetype`: loại file (`image/png`, `application/pdf`, ...).
* `destination`: thư mục lưu.
* `filename`: tên Multer đặt sau khi lưu.
* `path`: đường dẫn đến file.
* `size`: kích thước file. ([GitHub][2])

---

# R - Recap

Sau bài này bạn đã hiểu:

✅ Tại sao Express không upload được file mặc định.

✅ `multipart/form-data` là gì.

✅ Vì sao cần Multer.

✅ `upload.single()` hoạt động như thế nào.

✅ File được lưu vào thư mục `uploads`.



[1]: https://expressjs.com/en/resources/middleware/multer/?utm_source=chatgpt.com "multer middleware · Express.js"
[2]: https://github.com/expressjs/multer?utm_source=chatgpt.com "GitHub - expressjs/multer: Node.js middleware for handling `multipart/form-data`. · GitHub"
