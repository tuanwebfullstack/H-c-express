
# P - Purpose (Mục tiêu)

Sau phần Query, bạn cần làm được:

* Tìm dữ liệu theo nhiều điều kiện.
* Kết hợp nhiều điều kiện lọc.
* Lấy đúng dữ liệu cần thiết.
* Sắp xếp và phân trang dữ liệu.
* Thực hiện truy vấn trên mảng và object.
* Hiểu cách MongoDB sử dụng Index để tối ưu Query.

---

# A - Anatomy (Cấu trúc kiến thức)

## 1. Tìm toàn bộ dữ liệu

```javascript
db.users.find()
```

Kết quả

```json
[
    {
        "_id": 1,
        "name": "An",
        "age": 20
    },
    {
        "_id": 2,
        "name": "Bình",
        "age": 25
    }
]
```

---

## 2. Tìm một document

```javascript
db.users.findOne()
```

Hoặc

```javascript
db.users.findOne({
    name: "An"
})
```

---

## 3. Query theo điều kiện

```javascript
db.users.find({
    age: 20
})
```

SQL tương đương

```sql
SELECT * FROM users
WHERE age = 20;
```

---

## 4. So sánh

### Lớn hơn

```javascript
db.users.find({
    age: {
        $gt: 18
    }
})
```

---

### Nhỏ hơn

```javascript
db.users.find({
    age: {
        $lt: 30
    }
})
```

---

### Lớn hơn hoặc bằng

```javascript
{
    age:{
        $gte:18
    }
}
```

---

### Nhỏ hơn hoặc bằng

```javascript
{
    age:{
        $lte:30
    }
}
```

---

### Khác

```javascript
{
    age:{
        $ne:20
    }
}
```

---

## 5. Query nhiều điều kiện

MongoDB mặc định là AND

```javascript
db.users.find({
    age:20,
    city:"HCM"
})
```

SQL

```sql
WHERE age=20
AND city='HCM'
```

---

## 6. Toán tử OR

```javascript
db.users.find({
    $or:[
        {age:20},
        {city:"HN"}
    ]
})
```

---

## 7. Toán tử AND

```javascript
db.users.find({
    $and:[
        {age:20},
        {city:"HN"}
    ]
})
```

Thực tế thường không cần dùng vì MongoDB tự hiểu nhiều điều kiện là AND.

---

## 8. IN

```javascript
db.users.find({
    city:{
        $in:["HN","HCM"]
    }
})
```

SQL

```sql
WHERE city IN (...)
```

---

## 9. NOT IN

```javascript
{
    city:{
        $nin:["HN","HCM"]
    }
}
```

---

## 10. EXISTS

Kiểm tra field có tồn tại.

```javascript
db.users.find({
    phone:{
        $exists:true
    }
})
```

---

## 11. REGEX

```javascript
db.users.find({
    name:/An/
})
```

Hoặc

```javascript
{
    name:{
        $regex:"^A"
    }
}
```

---

## 12. Query Object

Document

```json
{
    name:"An",
    address:{
        city:"HCM",
        district:"1"
    }
}
```

Query

```javascript
db.users.find({
    "address.city":"HCM"
})
```

---

## 13. Query Array

Document

```json
{
    skills:[
        "HTML",
        "CSS",
        "JS"
    ]
}
```

Query

```javascript
db.users.find({
    skills:"JS"
})
```

---

## 14. $all

```javascript
db.users.find({
    skills:{
        $all:["HTML","CSS"]
    }
})
```

---

## 15. $size

```javascript
db.users.find({
    skills:{
        $size:3
    }
})
```

---

## 16. Projection

Chỉ lấy field cần thiết.

```javascript
db.users.find(
    {},
    {
        name:1,
        age:1
    }
)
```

Không lấy `_id`

```javascript
{
    name:1,
    age:1,
    _id:0
}
```

---

## 17. Sort

```javascript
db.users.find().sort({
    age:1
})
```

Tăng dần

```javascript
1
```

Giảm dần

```javascript
-1
```

---

## 18. Limit

```javascript
db.users.find().limit(5)
```

---

## 19. Skip

```javascript
db.users.find()
.skip(10)
.limit(5)
```

Dùng để phân trang.

---

## 20. Count

```javascript
db.users.countDocuments()
```

Hoặc

```javascript
db.users.countDocuments({
    age:20
})
```

---

## 21. Distinct

```javascript
db.users.distinct("city")
```

---

## 22. Query theo nhiều điều kiện

```javascript
db.users.find({
    age:{
        $gte:18,
        $lte:30
    }
})
```

---

# C - Connection (Liên kết kiến thức)

Trong MERN Stack, các thao tác Query trong MongoDB thường được sử dụng thông qua Mongoose:

| MongoDB            | Mongoose            | SQL            |
| ------------------ | ------------------- | -------------- |
| `find()`           | `User.find()`       | SELECT         |
| `findOne()`        | `User.findOne()`    | SELECT LIMIT 1 |
| `sort()`           | `.sort()`           | ORDER BY       |
| `limit()`          | `.limit()`          | LIMIT          |
| `skip()`           | `.skip()`           | OFFSET         |
| `countDocuments()` | `.countDocuments()` | COUNT          |
| `distinct()`       | `.distinct()`       | DISTINCT       |

Ví dụ:

```javascript
const users = await User.find({
    age: {
        $gte: 18
    }
})
.sort({ age: 1 })
.limit(10)
.skip(20);
```

---

# E - Example (Ví dụ thực tế)

Giả sử collection `users`:

```json
[
    {
        "name":"An",
        "age":20,
        "city":"HCM",
        "skills":["HTML","CSS","JS"]
    },
    {
        "name":"Bình",
        "age":25,
        "city":"HN",
        "skills":["JS","React"]
    },
    {
        "name":"Cường",
        "age":30,
        "city":"HCM",
        "skills":["NodeJS","MongoDB"]
    }
]
```

### Ví dụ 1: Người từ 18–25 tuổi

```javascript
db.users.find({
    age:{
        $gte:18,
        $lte:25
    }
})
```

### Ví dụ 2: Người ở HCM

```javascript
db.users.find({
    city:"HCM"
})
```

### Ví dụ 3: Biết JavaScript

```javascript
db.users.find({
    skills:"JS"
})
```

### Ví dụ 4: Sắp xếp theo tuổi giảm dần

```javascript
db.users.find()
.sort({
    age:-1
})
```

### Ví dụ 5: Phân trang

```javascript
db.users.find()
.skip(20)
.limit(10)
```

---

# R - Review (Tổng kết)

## Các phương thức quan trọng

| Nhóm            | Kiến thức                           |
| --------------- | ----------------------------------- |
| Truy vấn cơ bản | `find()`, `findOne()`               |
| So sánh         | `$gt`, `$gte`, `$lt`, `$lte`, `$ne` |
| Điều kiện       | `$and`, `$or`, `$in`, `$nin`        |
| Chuỗi           | `$regex`                            |
| Object          | `address.city`                      |
| Array           | `$all`, `$size`                     |
| Projection      | Chọn hoặc loại bỏ field             |
| Sắp xếp         | `sort()`                            |
| Phân trang      | `skip()`, `limit()`                 |
| Thống kê        | `countDocuments()`, `distinct()`    |



Đây là lộ trình phù hợp để phục vụ phát triển ứng dụng **MERN Stack**, đặc biệt khi bạn xây dựng website học lập trình có AI mentor, nơi cần truy vấn người dùng, bài học, tiến độ học tập và lịch sử tương tác một cách hiệu quả.
