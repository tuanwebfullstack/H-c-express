# AGGREGATION FRAMEWORK TRONG MONGODB (PACER)

Aggregation Framework là **một trong những kiến thức mạnh nhất của MongoDB**. Nếu `find()` chỉ giúp tìm dữ liệu, thì Aggregation giúp **phân tích, thống kê, biến đổi và kết hợp dữ liệu** tương tự như `GROUP BY`, `JOIN`, `SUM`, `AVG` trong SQL.

---

# P - Purpose (Mục đích)

## Aggregation Framework là gì?

Aggregation Framework là **hệ thống xử lý dữ liệu theo từng giai đoạn (pipeline)**.

Bạn đưa dữ liệu vào đầu pipeline, mỗi giai đoạn xử lý một phần và truyền kết quả cho giai đoạn tiếp theo.

```
Collection

↓

Stage 1

↓

Stage 2

↓

Stage 3

↓

Result
```

Ví dụ:

Bạn có Collection `submissions`

```
[
    { user:"A", score:90 },
    { user:"A", score:80 },
    { user:"B", score:70 }
]
```

Muốn tính điểm trung bình của từng người.

Không thể dùng `find()`.

Phải dùng Aggregation.

---

## Mục tiêu

Aggregation giúp:

* Thống kê dữ liệu
* Gom nhóm
* Tính toán
* Kết hợp nhiều Collection
* Chuyển đổi dữ liệu
* Phân tích dữ liệu

---

# A - Architecture (Kiến trúc)

Aggregation hoạt động theo **Pipeline**.

Ví dụ:

```
Users Collection

↓

$match

↓

$group

↓

$sort

↓

Result
```

Mỗi Stage nhận dữ liệu từ Stage trước.

Ví dụ

```
Collection

↓

Lọc học viên

↓

Tính điểm trung bình

↓

Sắp xếp

↓

Trả kết quả
```

Pipeline giống dây chuyền sản xuất.

---

# C - Core (Kiến thức cốt lõi)

## 1. aggregate()

Cú pháp

```javascript
db.collection.aggregate([
    { Stage1 },
    { Stage2 },
    { Stage3 }
])
```

---

## 2. $match

Lọc dữ liệu.

Giống

```javascript
find()
```

Ví dụ

```javascript
db.submissions.aggregate([
    {
        $match:{
            score:{
                $gte:80
            }
        }
    }
])
```

Chỉ lấy điểm ≥ 80.

---

## 3. $project

Chọn field.

Giống

```javascript
SELECT field
```

Ví dụ

```javascript
{
    $project:{
        user:1,
        score:1
    }
}
```

---

Có thể tạo field mới

```javascript
{
    $project:{
        user:1,
        passed:{
            $gte:["$score",50]
        }
    }
}
```

Kết quả

```
{
    user:"A",
    passed:true
}
```

---

## 4. $group

Quan trọng nhất.

Giống

```
GROUP BY
```

Ví dụ

```
A

90

A

80

B

70
```

Tính tổng

```javascript
{
    $group:{
        _id:"$user",
        total:{
            $sum:"$score"
        }
    }
}
```

Kết quả

```
A

170

B

70
```

---

## 5. Các toán tử trong $group

### Tổng

```javascript
$sum
```

---

### Trung bình

```javascript
$avg
```

---

### Lớn nhất

```javascript
$max
```

---

### Nhỏ nhất

```javascript
$min
```

---

### Đếm

```javascript
$count
```

hoặc

```javascript
{
    $sum:1
}
```

---

## 6. $sort

Sắp xếp.

```javascript
{
    $sort:{
        score:-1
    }
}
```

---

## 7. $limit

Lấy N document.

```javascript
{
    $limit:5
}
```

---

## 8. $skip

Bỏ qua.

```javascript
{
    $skip:20
}
```

Dùng cho phân trang.

---

## 9. $lookup

JOIN giữa hai Collection.

Ví dụ

```
Users

↓

Submissions
```

```javascript
{
    $lookup:{
        from:"users",
        localField:"userId",
        foreignField:"_id",
        as:"user"
    }
}
```

Kết quả

```
Submission

+

User
```

---

## 10. $unwind

Tách Array.

Ví dụ

```
[
    "JS",
    "React",
    "Node"
]
```

↓

```
JS

React

Node
```

```javascript
{
    $unwind:"$skills"
}
```

---

## 11. $count

Đếm document.

```javascript
{
    $count:"totalUsers"
}
```

---

## 12. $addFields

Thêm field.

```javascript
{
    $addFields:{
        passed:true
    }
}
```

---

## 13. $unset

Xóa field.

```javascript
{
    $unset:"password"
}
```

---

# E - Example (Website học lập trình)

## Ví dụ 1

Đếm số học viên.

```javascript
db.users.aggregate([
    {
        $count:"totalUsers"
    }
])
```

---

## Ví dụ 2

Điểm trung bình mỗi học viên.

```javascript
db.submissions.aggregate([
    {
        $group:{
            _id:"$userId",
            avgScore:{
                $avg:"$score"
            }
        }
    }
])
```

---

## Ví dụ 3

Top 5 học viên.

```javascript
db.submissions.aggregate([
    {
        $group:{
            _id:"$userId",
            totalScore:{
                $sum:"$score"
            }
        }
    },
    {
        $sort:{
            totalScore:-1
        }
    },
    {
        $limit:5
    }
])
```

---

## Ví dụ 4

Danh sách bài nộp kèm tên học viên.

```javascript
db.submissions.aggregate([
    {
        $lookup:{
            from:"users",
            localField:"userId",
            foreignField:"_id",
            as:"user"
        }
    }
])
```

---

## Ví dụ 5

Lấy Lesson của một Course.

```
Course

↓

Lesson
```

```javascript
db.courses.aggregate([
    {
        $lookup:{
            from:"lessons",
            localField:"_id",
            foreignField:"courseId",
            as:"lessons"
        }
    }
])
```

---

## Ví dụ 6

Thống kê số bài nộp theo bài tập.

```javascript
db.submissions.aggregate([
    {
        $group:{
            _id:"$exerciseId",
            totalSubmissions:{
                $sum:1
            }
        }
    }
])
```

---

# R - Rules (Quy tắc)

## 1. Luôn đặt `$match` càng sớm càng tốt

❌ Không tốt

```javascript
[
    { $group:{...} },
    { $match:{ score:{ $gte:80 } } }
]
```

✔ Tốt hơn

```javascript
[
    { $match:{ score:{ $gte:80 } } },
    { $group:{...} }
]
```

Lọc trước giúp giảm số document phải xử lý.

---

## 2. Tạo Index cho các trường dùng trong `$match` và `$sort`

Ví dụ

```javascript
{
    $match:{
        userId:ObjectId(...)
    }
}
```

Nên có Index

```javascript
{
    userId:1
}
```

---

## 3. Hạn chế `$lookup` trên dữ liệu lớn

MongoDB không tối ưu cho việc Join quá nhiều như SQL.

Nếu thường xuyên cần dữ liệu của nhiều Collection cùng lúc, hãy cân nhắc **Denormalization** (lưu lặp lại một số thông tin ít thay đổi) để giảm số lần `$lookup`.

---

## 4. Chỉ `$project` những field cần thiết

Giảm lượng dữ liệu truyền qua các Stage.

---

## 5. Thứ tự Pipeline rất quan trọng

Một Pipeline tối ưu thường có dạng:

```
$match

↓

$project

↓

$group

↓

$sort

↓

$limit
```

Điều này giúp MongoDB xử lý ít dữ liệu nhất ở các bước tốn kém.

---
