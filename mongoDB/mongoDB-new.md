
# Hiểu MongoDB hoạt động như thế nào (Hệ thống PACER)

## P — Purpose (Mục đích)

### MongoDB là gì?

MongoDB là một **hệ quản trị cơ sở dữ liệu NoSQL** được thiết kế để lưu trữ dữ liệu theo dạng **Document** thay vì bảng như cơ sở dữ liệu quan hệ.

### Tại sao cần MongoDB?

Các website hiện đại thường có dữ liệu:

* thay đổi liên tục
* không cố định cấu trúc
* cần mở rộng dễ dàng
* làm việc tốt với JavaScript

MongoDB giải quyết các vấn đề đó bằng cách sử dụng **Document** có cấu trúc linh hoạt.

---

## A — Architecture (Kiến trúc)

MongoDB được tổ chức theo cấu trúc phân cấp sau:

```text
MongoDB Server
│
├── Database
│
├── Collection
│
├── Document
│
└── Field
```

### Thành phần

### 1. Database

Là nơi chứa nhiều Collection.

Ví dụ

```text
learning_ai
```

---

### 2. Collection

Collection tương đương Table trong MySQL.

Ví dụ

```text
users
courses
lessons
```

---

### 3. Document

Document là một bản ghi dữ liệu.

Ví dụ

```json
{
    "name": "Tuấn",
    "age": 20,
    "email": "tuan@gmail.com"
}
```

MongoDB lưu mỗi document thành một object độc lập.

---

### 4. Field

Là các thuộc tính của Document.

```json
{
    "name": "Tuấn",
    "age": 20
}
```

Field gồm

* name
* age

---

## C — Concept (Khái niệm)

### MongoDB là Database NoSQL

Khác với SQL Database.

SQL

```text
Database
    └── Table
            └── Row
```

MongoDB

```text
Database
    └── Collection
            └── Document
```

---

### Document

MongoDB lưu dữ liệu dưới dạng Document.

Ví dụ

```json
{
    "name": "Tuấn",
    "age": 20
}
```

Document gần giống Object trong JavaScript.

---

### JSON và BSON

Lập trình viên làm việc với JSON.

```json
{
    "name":"Tuấn",
    "age":20
}
```

MongoDB sẽ chuyển thành BSON để lưu.

```text
JSON
      ↓
MongoDB
      ↓
BSON
```

BSON giúp:

* lưu nhanh
* tìm kiếm nhanh
* hỗ trợ nhiều kiểu dữ liệu hơn JSON

---

### Collection thay cho Table

MySQL

```text
User Table
```

MongoDB

```text
users Collection
```

Collection chứa nhiều Document.

---

### Document thay cho Row

MySQL

| id | name |
| -- | ---- |
| 1  | Tuấn |

MongoDB

```json
{
    "_id": "...",
    "name": "Tuấn"
}
```

---

### Schema linh hoạt

Trong MySQL

```text
Row 1
id
name
age
```

```text
Row 2
id
name
age
```

Mọi Row phải giống nhau.

MongoDB

```json
{
    "name":"Tuấn",
    "age":20
}
```

```json
{
    "name":"Nam",
    "phone":"0123..."
}
```

Hai Document có thể khác nhau.

Đó gọi là **Flexible Schema**.

---

## E — Execution (Cách hoạt động)

Ví dụ người dùng đăng ký tài khoản.

### Bước 1

Người dùng nhập

* tên
* email
* mật khẩu

↓

### Bước 2

NodeJS nhận request.

↓

### Bước 3

Express gọi MongoDB.

```javascript
db.users.insertOne(user);
```

↓

### Bước 4

MongoDB chuyển JSON thành BSON.

↓

### Bước 5

MongoDB lưu Document.

```json
{
    "_id":"...",
    "name":"Tuấn",
    "email":"tuan@gmail.com"
}
```

---

Ví dụ đọc dữ liệu

```javascript
db.users.findOne({
    email:"tuan@gmail.com"
})
```

MongoDB

↓

Tìm Collection

↓

Tìm Document

↓

Trả về JSON cho NodeJS.

---

## R — Relationship (Mối quan hệ)

### So sánh MongoDB và MySQL

| MySQL          | MongoDB          |
| -------------- | ---------------- |
| Database       | Database         |
| Table          | Collection       |
| Row            | Document         |
| Column         | Field            |
| SQL            | Query API        |
| Schema cố định | Schema linh hoạt |

---

### Mối quan hệ giữa các thành phần

```text
MongoDB Server
        │
        ▼
    Database
        │
        ▼
    Collection
        │
        ▼
    Document
        │
        ▼
      Field
```

Ví dụ:

```text
MongoDB
└── learning_ai
    ├── users
    │   ├── {name, email, password}
    │   └── {name, email, role}
    │
    ├── courses
    │   ├── {title, level}
    │   └── {title, level}
    │
    └── lessons
        ├── {title, content}
        └── {title, content}
```

---


