

# INDEX TRONG MONGODB (PACER)

# P - Purpose (Mục đích)

## Index là gì?

Index là **cấu trúc dữ liệu đặc biệt** giúp MongoDB tìm kiếm dữ liệu nhanh hơn mà không cần đọc toàn bộ Collection.

Có thể tưởng tượng:

* Collection = một cuốn sách
* Document = từng trang
* Index = mục lục của cuốn sách

Ví dụ:

Bạn có 1 triệu User.

```json
{
    "_id":1,
    "name":"Tuấn",
    "age":20
}
```

Bạn tìm

```js
db.users.find({name:"Tuấn"})
```

### Không có Index

MongoDB phải

```
Document 1
Document 2
Document 3
...
Document 1000000
```

Đọc từng document.

Đây gọi là

```
Collection Scan (COLLSCAN)
```

Độ phức tạp gần như

```
O(n)
```

---

### Có Index

MongoDB tra ngay mục lục

```
Tuấn
    ↓
Document 538291
```

Không cần đọc cả Collection.

Thường chỉ mất

```
O(log n)
```

hoặc gần như O(1) với một số trường hợp truy cập.

---

## Mục tiêu của Index

* Tăng tốc Find
* Tăng tốc Sort
* Tăng tốc Join (`$lookup` ở collection được tra cứu)
* Tăng tốc Aggregate
* Giảm số document phải đọc

---

# A - Architecture (Kiến trúc hoạt động)

Giả sử Collection

```json
[
 {name:"An", age:20},
 {name:"Nam", age:22},
 {name:"Tuấn", age:25},
 {name:"Bình", age:19}
]
```

Tạo Index

```js
db.users.createIndex({name:1})
```

MongoDB tạo một cấu trúc gần giống

```
An
 ↓
Document 1

Bình
 ↓
Document 4

Nam
 ↓
Document 2

Tuấn
 ↓
Document 3
```

Khi tìm

```js
find({name:"Nam"})
```

MongoDB

```
Query

↓

Index

↓

Document

↓

Return
```

Không cần đọc toàn bộ Collection.

---

## Index được lưu ở đâu?

MongoDB lưu Index riêng với Collection.

```
Database

    users Collection

        Documents

        Indexes
```

Mỗi Collection có thể có nhiều Index.

Ví dụ

```
_id

name

email

age

createdAt
```

---

## MongoDB dùng cấu trúc gì?

MongoDB sử dụng **B-Tree** (chính xác hơn là B-tree/B+tree trong storage engine).

Đặc điểm:

* dữ liệu luôn được sắp xếp
* tìm kiếm nhanh
* insert nhanh
* delete nhanh
* hỗ trợ range query

---

# C - Core (Kiến thức cốt lõi)

## 1. Tạo Index

```js
db.users.createIndex({
    name:1
})
```

1

=

```
Ascending
```

---

## 2. Descending

```js
db.users.createIndex({
    age:-1
})
```

-1

=

```
Descending
```

---

## 3. Xem Index

```js
db.users.getIndexes()
```

Ví dụ

```json
[
 {_id_},
 {name_1},
 {email_1}
]
```

---

## 4. Xóa Index

```js
db.users.dropIndex("name_1")
```

---

## 5. Xóa toàn bộ

```js
db.users.dropIndexes()
```

---

## 6. MongoDB tự tạo Index

Mọi Collection đều có

```json
_id
```

MongoDB tự tạo

```js
_id:1
```

Không cần tạo.

---

## 7. Unique Index

Không cho phép trùng.

```js
db.users.createIndex(
    {
        email:1
    },
    {
        unique:true
    }
)
```

Nếu

```
a@gmail.com
```

đã tồn tại

Insert tiếp

```
a@gmail.com
```

↓

```
Duplicate Key Error
```

---

## 8. Compound Index

Index nhiều field.

```js
db.users.createIndex({
    age:1,
    name:1
})
```

Thứ tự rất quan trọng.

Index

```
(age,name)
```

khác

```
(name,age)
```

---

## 9. Text Index

Cho phép tìm kiếm văn bản.

```js
db.posts.createIndex({
    content:"text"
})
```

Tìm

```js
db.posts.find({
    $text:{
        $search:"mongodb"
    }
})
```

---

## 10. TTL Index

Tự động xóa document.

Ví dụ OTP

```json
{
    code:"123456",
    expireAt:Date(...)
}
```

Tạo

```js
db.otp.createIndex(
{
    expireAt:1
},
{
    expireAfterSeconds:0
})
```

Hết hạn

↓

MongoDB tự xóa.

---

## 11. Sparse Index

Chỉ Index document có field.

Ví dụ

```json
{name:"A"}

{name:"B", phone:"0123"}
```

Sparse

```js
db.users.createIndex(
{
    phone:1
},
{
    sparse:true
})
```

Chỉ document có

```
phone
```

được index.

---

## 12. Explain

Muốn biết MongoDB dùng Index hay không.

```js
db.users.find({
    name:"Tuấn"
}).explain("executionStats")
```

Nếu thấy

```
IXSCAN
```

↓

Đã dùng Index.

Nếu

```
COLLSCAN
```

↓

Quét cả Collection.

---

# E - Example (Ví dụ thực tế)

Giả sử website học lập trình có Collection `users`.

```json
{
    "_id":1,
    "email":"abc@gmail.com",
    "username":"tuan",
    "role":"student",
    "createdAt":ISODate(...)
}
```

---

### Đăng nhập

```js
find({
    email:"abc@gmail.com"
})
```

Nên tạo

```js
createIndex({
    email:1
})
```

---

### Tìm User

```js
find({
    username:"tuan"
})
```

Tạo

```js
createIndex({
    username:1
})
```

---

### Danh sách học viên mới

```js
find()
.sort({
    createdAt:-1
})
```

Nên tạo

```js
createIndex({
    createdAt:-1
})
```

---

### Lọc theo vai trò

```js
find({
    role:"student"
})
```

Có thể tạo

```js
createIndex({
    role:1
})
```

---

### Tìm theo Role và sắp xếp theo ngày tạo

```js
find({
    role:"student"
})
.sort({
    createdAt:-1
})
```

Nên tạo **Compound Index**

```js
db.users.createIndex({
    role:1,
    createdAt:-1
})
```

MongoDB có thể vừa lọc vừa sắp xếp bằng cùng một index, tránh phải sắp xếp thêm trong bộ nhớ.

---

# R - Rules (Quy tắc cần nhớ)

## Khi nào nên tạo Index?

✅ Field thường xuyên:

* `find()`
* `sort()`
* `update()`
* `delete()`
* `$lookup`
* `aggregate`
* `$match`

---

## Khi nào không nên?

❌ Field ít được truy vấn.

❌ Field thay đổi liên tục nếu không cần thiết (vì mỗi lần cập nhật MongoDB cũng phải cập nhật Index).

❌ Tạo quá nhiều Index.

---

## Trade-off

Index không phải lúc nào cũng tốt.

**Ưu điểm**

* Query nhanh hơn.
* Sort nhanh hơn.
* Aggregate hiệu quả hơn.

**Nhược điểm**

* Tốn thêm dung lượng lưu trữ.
* Insert chậm hơn vì phải cập nhật Index.
* Update/Delete cũng có chi phí cao hơn.
* Quá nhiều Index làm tăng chi phí ghi và quản lý.

---

## Thứ tự trong Compound Index

Đây là quy tắc rất quan trọng.

Ví dụ:

```js
db.users.createIndex({
    role: 1,
    createdAt: -1
})
```

Index này hỗ trợ tốt:

```js
find({ role: "student" })
find({ role: "student" }).sort({ createdAt: -1 })
```

Nhưng **không tối ưu** cho:

```js
find({ createdAt: { $gte: someDate } })
```

vì truy vấn không bắt đầu từ trường `role`. Đây được gọi là **quy tắc tiền tố (Prefix Rule)** trong Compound Index.

---
