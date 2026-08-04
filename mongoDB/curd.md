# CRUD trong MongoDB 


# P — Purpose (Mục đích)

## CRUD là gì?

**CRUD** là 4 thao tác cơ bản để làm việc với dữ liệu trong cơ sở dữ liệu.

CRUD là viết tắt của:

| Chữ viết tắt | Ý nghĩa | Chức năng        |
| ------------ | ------- | ---------------- |
| **C**        | Create  | Thêm dữ liệu     |
| **R**        | Read    | Đọc/Lấy dữ liệu  |
| **U**        | Update  | Cập nhật dữ liệu |
| **D**        | Delete  | Xóa dữ liệu      |

Mọi ứng dụng web đều sử dụng CRUD.

Ví dụ website học lập trình:

* Đăng ký tài khoản → **Create**
* Xem thông tin cá nhân → **Read**
* Đổi mật khẩu → **Update**
* Xóa tài khoản → **Delete**

---

# A — Architecture (Kiến trúc)

CRUD tác động trực tiếp lên **Collection** và **Document**.

```text
Application (Node.js)
        │
        ▼
 MongoDB Driver / Mongoose
        │
        ▼
     Database
        │
        ▼
    Collection
        │
        ▼
    Document
```

Ví dụ Collection `users`:

```json
[
    {
        "_id": 1,
        "name": "Tuấn",
        "age": 20
    },
    {
        "_id": 2,
        "name": "Nam",
        "age": 22
    }
]
```

CRUD sẽ thêm, đọc, sửa hoặc xóa các **Document** trong Collection này.

---

# C — Concept (Khái niệm)

## 1. Create (Thêm dữ liệu)

Thêm một hoặc nhiều Document vào Collection.

Ví dụ:

```javascript
db.users.insertOne({
    name: "Tuấn",
    age: 20
});
```

Sau khi thực hiện:

```json
{
    "_id": ObjectId("..."),
    "name": "Tuấn",
    "age": 20
}
```

MongoDB tự sinh `_id` nếu bạn không cung cấp.

---

## 2. Read (Đọc dữ liệu)

Lấy dữ liệu từ Collection.

Lấy tất cả:

```javascript
db.users.find();
```

Lấy một Document:

```javascript
db.users.findOne({
    name: "Tuấn"
});
```

---

## 3. Update (Cập nhật dữ liệu)

Thay đổi dữ liệu của Document.

```javascript
db.users.updateOne(
    { name: "Tuấn" },
    {
        $set: {
            age: 21
        }
    }
);
```

Kết quả:

Trước

```json
{
    "name": "Tuấn",
    "age": 20
}
```

↓

Sau

```json
{
    "name": "Tuấn",
    "age": 21
}
```

---

## 4. Delete (Xóa dữ liệu)

Xóa Document khỏi Collection.

```javascript
db.users.deleteOne({
    name: "Tuấn"
});
```

Document sẽ bị xóa khỏi Collection.

---

# E — Execution (Cách hoạt động)

Ví dụ người dùng đăng ký tài khoản.

## Create

```
Người dùng nhập thông tin
        │
        ▼
Node.js nhận Request
        │
        ▼
insertOne()
        │
        ▼
MongoDB lưu Document
```

---

Ví dụ người dùng xem hồ sơ.

## Read

```
Người dùng mở trang Profile
        │
        ▼
findOne()
        │
        ▼
MongoDB tìm Document
        │
        ▼
Trả dữ liệu về Server
```

---

Ví dụ đổi tên.

## Update

```
Người dùng sửa tên
        │
        ▼
updateOne()
        │
        ▼
MongoDB cập nhật Document
```

---

Ví dụ xóa tài khoản.

## Delete

```
Người dùng bấm Xóa
        │
        ▼
deleteOne()
        │
        ▼
MongoDB xóa Document
```

---

# R — Relationship (Mối quan hệ)

## CRUD và các phương thức MongoDB

| CRUD   | MongoDB                                       |
| ------ | --------------------------------------------- |
| Create | `insertOne()`, `insertMany()`                 |
| Read   | `find()`, `findOne()`                         |
| Update | `updateOne()`, `updateMany()`, `replaceOne()` |
| Delete | `deleteOne()`, `deleteMany()`                 |

---

## CRUD và HTTP Method trong REST API

Khi xây dựng API với ExpressJS, các thao tác CRUD thường tương ứng với các HTTP Method:

| CRUD   | HTTP Method        | Ví dụ API                      |
| ------ | ------------------ | ------------------------------ |
| Create | `POST`             | `POST /users`                  |
| Read   | `GET`              | `GET /users`, `GET /users/:id` |
| Update | `PUT` hoặc `PATCH` | `PATCH /users/:id`             |
| Delete | `DELETE`           | `DELETE /users/:id`            |

Ví dụ luồng hoạt động:

```
Client
    │
    ├── POST /users      → Create
    ├── GET /users       → Read
    ├── PATCH /users/1   → Update
    └── DELETE /users/1  → Delete
            │
            ▼
       ExpressJS
            │
            ▼
         MongoDB
```

---
