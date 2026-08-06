# DATA MODELING TRONG MONGODB (PACER)

Data Modeling là **kiến thức quan trọng nhất của MongoDB**. Nếu Index giúp truy vấn nhanh thì Data Modeling quyết định database của bạn có dễ mở rộng, dễ bảo trì và hiệu năng tốt hay không.

---

# P - Purpose (Mục đích)

## Data Modeling là gì?

Data Modeling là quá trình **thiết kế cách lưu trữ dữ liệu trong MongoDB**.

Bạn phải trả lời được các câu hỏi:

* Nên tạo bao nhiêu Collection?
* Collection nào chứa dữ liệu gì?
* Có nên nhúng (Embed) dữ liệu không?
* Khi nào nên tách Collection?
* Các Collection liên kết với nhau như thế nào?

Ví dụ một website học lập trình.

Có các đối tượng:

* User
* Course
* Lesson
* Exercise
* Submission

Data Modeling sẽ quyết định:

```
users

courses

lessons

exercises

submissions
```

hay

```
courses

{
    title,
    lessons:[
        ...
    ]
}
```

Đây chính là Data Modeling.

---

## Mục tiêu

Thiết kế database sao cho:

* Query nhanh
* Ít phải Join
* Dễ mở rộng
* Ít dữ liệu trùng lặp
* Dễ bảo trì

---

# A - Architecture (Kiến trúc)

Trong MongoDB có hai cách thiết kế chính.

## 1. Embedding (Nhúng)

Lưu document con bên trong document cha.

Ví dụ

```
Course

{
    title:"JavaScript",

    lessons:[
        {
            title:"Variables"
        },
        {
            title:"Function"
        }
    ]
}
```

Collection

```
courses
```

chỉ có một document.

---

Ưu điểm

* Query rất nhanh
* Không cần Join
* Chỉ đọc một document

---

Nhược điểm

* Document lớn dần
* Khó cập nhật từng phần
* Không phù hợp dữ liệu rất lớn

---

## 2. Referencing (Tham chiếu)

Giống SQL.

```
courses

{
    _id:1,
    title:"JavaScript"
}
```

```
lessons

{
    _id:5,
    courseId:1,
    title:"Variables"
}
```

Liên kết bằng

```
courseId
```

---

Ưu điểm

* Dễ mở rộng
* Dễ cập nhật
* Ít trùng dữ liệu

---

Nhược điểm

* Phải query nhiều Collection
* Có thể dùng `$lookup` nếu cần lấy dữ liệu liên quan

---

# C - Core (Kiến thức cốt lõi)

## 1. One-to-One

Ví dụ

Một User

↓

Một Profile

```
users

{
    _id,
    username
}
```

```
profiles

{
    userId,
    avatar,
    birthday
}
```

Hoặc

```
users

{
    username,

    profile:{
        avatar,
        birthday
    }
}
```

Nếu Profile nhỏ và luôn đi cùng User → Embed.

---

## 2. One-to-Many

Ví dụ

Một khóa học

↓

Nhiều bài học

```
Course

↓

Lesson

↓

Lesson

↓

Lesson
```

Có hai cách.

### Embed

```
Course

{
    title,

    lessons:[
        ...
    ]
}
```

---

### Reference

```
Course

{
    _id
}
```

```
Lesson

{
    courseId
}
```

---

## 3. Many-to-Many

Ví dụ

```
Student

↔

Course
```

Một học viên học nhiều khóa.

Một khóa có nhiều học viên.

```
Enrollments

{
    userId,
    courseId
}
```

Giống bảng trung gian trong SQL.

---

## 4. Denormalization

MongoDB thích lưu dữ liệu lặp lại nếu điều đó giúp đọc nhanh hơn.

Ví dụ

```
Course

{
    title:"JS"
}
```

Lesson

```
{
    courseId,

    courseTitle:"JS"
}
```

Mặc dù `courseTitle` đã có trong Course, vẫn có thể lưu lại trong Lesson để giảm truy vấn.

Đây gọi là **Denormalization**.

---

## 5. Normalization

Giống SQL.

```
Lesson

{
    courseId
}
```

Muốn tên Course

↓

Query Course.

---

MongoDB thường **ưu tiên Denormalization hơn SQL**, nhưng chỉ lặp lại những dữ liệu ít thay đổi.

---

## 6. Atomic Operation

MongoDB đảm bảo tính **Atomic** trên **một document**.

Ví dụ

```
Course

{

title,

lessons:[

...

]

}
```

Update

```
lesson[3]
```

vẫn là một transaction trên đúng một document.

---

Nếu Lesson ở Collection khác

↓

Có thể phải Transaction nhiều document.

---

## 7. Document Size Limit

Một document tối đa

```
16 MB
```

Đây là lý do không nên Embed vô hạn.

Ví dụ

```
Course

↓

1 triệu lessons
```

Không được.

---

# E - Example (Website học lập trình)

Giả sử bạn xây dựng website học lập trình cùng AI.

## User

```
{
    _id,
    username,
    email,
    avatar
}
```

---

## Course

```
{
    _id,
    title,
    description,
    difficulty
}
```

---

## Lesson

```
{
    _id,
    courseId,
    title,
    order
}
```

---

## Exercise

```
{
    _id,
    lessonId,
    title,
    starterCode,
    expectedOutput
}
```

---

## Submission

```
{
    _id,
    userId,
    exerciseId,
    code,
    score,
    submittedAt
}
```

---

### Tại sao không Embed toàn bộ?

Nếu lưu như:

```
Course

↓

Lessons

↓

Exercises

↓

Submissions
```

Mỗi khi một học viên nộp bài, document Course sẽ phải cập nhật.

Nếu có hàng nghìn học viên:

* Document rất lớn.
* Dễ vượt giới hạn 16 MB.
* Hiệu năng ghi giảm.

Vì vậy:

* Course → Reference Lesson.
* Lesson → Reference Exercise.
* Submission → Collection riêng.

Đây là thiết kế phù hợp hơn cho hệ thống học trực tuyến.

---

# R - Rules (Quy tắc thiết kế)

## Khi nào nên Embed?

✔ Dữ liệu nhỏ.

✔ Luôn được đọc cùng nhau.

✔ Ít thay đổi.

Ví dụ:

* Địa chỉ người dùng.
* Thông tin hồ sơ.
* Danh sách tag ngắn.

---

## Khi nào nên Reference?

✔ Dữ liệu lớn.

✔ Một document có rất nhiều document con.

✔ Quan hệ Many-to-Many.

✔ Dữ liệu được cập nhật thường xuyên.

Ví dụ:

* User ↔ Submission.
* Course ↔ Lesson (khi số bài học có thể tăng nhiều).
* Lesson ↔ Exercise.

---

## Tránh Join nếu có thể

MongoDB không được thiết kế để phụ thuộc nhiều vào Join như SQL. Hãy thiết kế schema để phần lớn truy vấn chỉ cần đọc từ một hoặc rất ít Collection.

---

## Thiết kế theo truy vấn

Trong MongoDB, hãy bắt đầu từ câu hỏi:

> **Ứng dụng sẽ truy vấn dữ liệu như thế nào?**

Ví dụ:

```
Trang Lesson

↓

Cần:

Lesson

+

Exercise

+

Progress
```

Schema nên được thiết kế để truy vấn này đơn giản và nhanh, thay vì cố gắng chuẩn hóa tối đa như trong cơ sở dữ liệu quan hệ.

---
