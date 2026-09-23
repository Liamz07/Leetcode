# Pascal's Triangle II — LeetCode

> **Bài toán:** [Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/)

## 1. Mô tả bài toán

Cho một số nguyên `rowIndex`, hãy trả về **hàng thứ `rowIndex`** của tam giác Pascal.

Quy ước:

- Hàng đầu tiên có chỉ số `0`.
- Hàng `0`: `[1]`
- Hàng `1`: `[1, 1]`
- Hàng `2`: `[1, 2, 1]`
- Hàng `3`: `[1, 3, 3, 1]`
- ...

Ví dụ:

```text
Input:  rowIndex = 3
Output: [1, 3, 3, 1]
```

---

# 2. Hiểu cấu trúc của Pascal's Triangle

Tam giác Pascal có dạng:

```text
                1
              1   1
            1   2   1
          1   3   3   1
        1   4   6   4   1
      1   5  10  10   5   1
```

Mỗi phần tử ở giữa được tạo bởi tổng của **hai phần tử ngay phía trên**:

```text
        1       3       3       1
          \     / \     / \     /
           1+3=4  3+3=6  3+1=4
```

Công thức:

```text
triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j]
```

với:

```text
0 < j < i
```

Hai đầu của mỗi hàng luôn là `1`:

```text
triangle[i][0] = 1
triangle[i][i] = 1
```

---

# 3. Điểm quan trọng của bài Pascal's Triangle II

Nếu bài toán là **Pascal's Triangle** thông thường, ta phải trả về toàn bộ tam giác từ hàng `0` đến hàng `rowIndex`.

Nhưng ở bài này, đề chỉ yêu cầu:

> Trả về **một hàng duy nhất**.

Ví dụ:

```text
rowIndex = 5
```

Ta chỉ cần:

```text
[1, 5, 10, 10, 5, 1]
```

Không cần lưu:

```text
[
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1],
    [1,4,6,4,1],
    [1,5,10,10,5,1]
]
```

Đây chính là điểm có thể tối ưu bộ nhớ.

---

# 4. Phân tích các hướng tiếp cận

Có nhiều cách giải. Ta nên hiểu từ cách trực tiếp đến cách tối ưu.

---

## 4.1. Cách 1 — Xây dựng toàn bộ tam giác Pascal

Ta có thể xây dựng từng hàng từ `0` đến `rowIndex`.

Ví dụ `rowIndex = 4`:

```text
Hàng 0: 1

Hàng 1: 1 1

Hàng 2: 1 2 1

Hàng 3: 1 3 3 1

Hàng 4: 1 4 6 4 1
```

### Ý tưởng

Ta lưu toàn bộ các hàng:

```text
vector<vector<int>> triangle;
```

Mỗi hàng mới có `i + 1` phần tử.

Phần tử:

```text
j = 0
```

và:

```text
j = i
```

luôn bằng `1`.

Các phần tử ở giữa:

```text
triangle[i][j]
    =
triangle[i-1][j-1] + triangle[i-1][j]
```

### Độ phức tạp

Có tổng cộng:

```text
1 + 2 + 3 + ... + (rowIndex + 1)
```

phần tử.

Do đó:

```text
Time:  O(rowIndex²)
Space: O(rowIndex²)
```

### Nhược điểm

Ta đang lưu rất nhiều dữ liệu không cần thiết.

Đề chỉ yêu cầu **hàng cuối cùng**, nên việc lưu toàn bộ tam giác là lãng phí bộ nhớ.

---

# 5. Cách 2 — Chỉ lưu hàng hiện tại

Ta có thể nhận thấy:

Để xây dựng hàng tiếp theo, ta chỉ cần biết **hàng ngay trước đó**.

Ví dụ:

```text
Hàng hiện tại:

1  3  3  1
```

Có thể tạo hàng tiếp theo:

```text
1  4  6  4  1
```

Ta không cần lưu những hàng cũ hơn.

Khi đó:

```text
Space = O(rowIndex)
```

thay vì:

```text
Space = O(rowIndex²)
```

Đây đã là một cải thiện đáng kể.

---

# 6. Cách 3 — Tối ưu bằng chính một vector

Ta có thể đi xa hơn.

Thay vì dùng hai vector:

```text
hang_cu
hang_moi
```

ta chỉ dùng **một vector**.

Ví dụ:

```text
[1, 3, 3, 1]
```

Khi xây dựng hàng tiếp theo, ta cập nhật trực tiếp vector.

Điểm quan trọng là:

> Phải cập nhật từ **phải sang trái**.

---

# 7. Tại sao phải cập nhật từ phải sang trái?

Đây là phần quan trọng nhất của lời giải tối ưu.

Giả sử đang có:

```text
[1, 3, 3, 1]
```

Ta muốn tạo:

```text
[1, 4, 6, 4, 1]
```

Công thức:

```text
new[j] = old[j-1] + old[j]
```

Nếu cập nhật từ trái sang phải:

```text
j = 1
```

ta có:

```text
a[1] = a[0] + a[1]
```

Vector trở thành:

```text
[1, 4, 3, 1]
```

Sau đó:

```text
j = 2
```

ta cần:

```text
a[2] = old[1] + old[2]
```

Nhưng `a[1]` đã bị thay đổi thành `4`.

Vì vậy:

```text
a[2] = 4 + 3 = 7
```

Kết quả sai.

---

## 7.1. Cập nhật từ phải sang trái

Thay vào đó, ta cập nhật:

```text
j = 3
j = 2
j = 1
```

Ban đầu:

```text
[1, 3, 3, 1]
```

### Bước 1

```text
a[3] = a[2] + a[3]
     = 3 + 1
     = 4
```

Kết quả:

```text
[1, 3, 3, 4]
```

---

### Bước 2

```text
a[2] = a[1] + a[2]
     = 3 + 3
     = 6
```

Kết quả:

```text
[1, 3, 6, 4]
```

---

### Bước 3

```text
a[1] = a[0] + a[1]
     = 1 + 3
     = 4
```

Kết quả:

```text
[1, 4, 6, 4]
```

Sau đó thêm `1` vào cuối:

```text
[1, 4, 6, 4, 1]
```

Đúng.

---

# 8. Tại sao cập nhật từ phải sang trái lại đúng?

Khi cập nhật:

```text
a[j] = a[j] + a[j-1]
```

nếu đi từ phải sang trái:

```text
j = i
i-1
i-2
...
1
```

thì:

- `a[j]` vẫn chưa bị thay đổi.
- `a[j-1]` cũng chưa bị thay đổi.

Do đó cả hai giá trị đều chính là giá trị của **hàng cũ**.

Đây là kỹ thuật rất quan trọng trong các bài toán:

- Dynamic Programming 1 chiều.
- Cập nhật mảng tại chỗ.
- 0/1 Knapsack.
- Một số bài toán tổ hợp.

Có thể ghi nhớ:

> Khi trạng thái mới phụ thuộc vào trạng thái cũ ở vị trí `j` và `j-1`, nếu dùng chung một mảng thì thường cần xét thứ tự cập nhật để tránh ghi đè dữ liệu cũ.

---

# 9. Công thức toán học của Pascal's Triangle

Ngoài cách xây dựng từ hàng trước, mỗi phần tử trong hàng `rowIndex` còn có thể tính trực tiếp bằng tổ hợp:

```text
C(n, k) = n! / (k! * (n-k)!)
```

Với:

```text
n = rowIndex
```

thì hàng cần tìm là:

```text
C(n, 0),
C(n, 1),
C(n, 2),
...
C(n, n)
```

Ví dụ:

```text
rowIndex = 5
```

ta có:

```text
C(5,0) = 1
C(5,1) = 5
C(5,2) = 10
C(5,3) = 10
C(5,4) = 5
C(5,5) = 1
```

Kết quả:

```text
[1, 5, 10, 10, 5, 1]
```

---

# 10. Có nên dùng công thức tổ hợp trực tiếp không?

Có thể dùng, nhưng với bài này cách xây dựng hàng bằng quy hoạch động đơn giản và trực quan hơn.

Nếu dùng công thức:

```text
C(n,k)
```

ta phải cẩn thận với:

- `factorial` có thể rất lớn.
- Overflow.
- Kiểu dữ liệu.
- Việc tính lại nhiều giá trị.

Có thể tối ưu công thức bằng quan hệ:

```text
C(n, k)
=
C(n, k-1) * (n-k+1) / k
```

Nhưng cách này vẫn cần xử lý vấn đề số nguyên và kiểu dữ liệu.

Vì vậy, với bài LeetCode này, cách cập nhật vector từ phải sang trái là một lựa chọn rất tốt để học kỹ thuật **in-place update**.

---

# 11. Phương hướng tối ưu nên chọn

Ta chọn:

```text
Một vector
+
Xây dựng từng hàng
+
Cập nhật từ phải sang trái
```

Ý tưởng tổng quát:

```text
Khởi tạo:

[1]

Mỗi lần tạo một hàng mới:

1. Thêm 1 vào cuối.
2. Duyệt từ phần tử kế cuối về phần tử thứ 2.
3. Cập nhật:

   a[j] = a[j] + a[j-1]

Lặp lại cho đến rowIndex.
```

---

# 12. Ví dụ chi tiết với rowIndex = 4

Ban đầu:

```text
[1]
```

---

## Tạo hàng 1

Thêm `1`:

```text
[1, 1]
```

---

## Tạo hàng 2

Thêm `1`:

```text
[1, 1, 1]
```

Cập nhật từ phải sang trái:

```text
a[1] = a[0] + a[1]
     = 1 + 1
     = 2
```

Kết quả:

```text
[1, 2, 1]
```

---

## Tạo hàng 3

Thêm `1`:

```text
[1, 2, 1, 1]
```

Cập nhật:

```text
a[2] = a[1] + a[2]
     = 2 + 1
     = 3
```

```text
[1, 2, 3, 1]
```

Tiếp:

```text
a[1] = a[0] + a[1]
     = 1 + 2
     = 3
```

Kết quả:

```text
[1, 3, 3, 1]
```

---

## Tạo hàng 4

Thêm `1`:

```text
[1, 3, 3, 1, 1]
```

Cập nhật từ phải sang trái:

```text
a[3] = a[2] + a[3]
     = 3 + 1
     = 4
```

```text
[1, 3, 3, 4, 1]
```

Tiếp:

```text
a[2] = a[1] + a[2]
     = 3 + 3
     = 6
```

```text
[1, 3, 6, 4, 1]
```

Tiếp:

```text
a[1] = a[0] + a[1]
     = 1 + 3
     = 4
```

Cuối cùng:

```text
[1, 4, 6, 4, 1]
```

---

# 13. Thuật toán

### Bước 1

Khởi tạo:

```text
vector<int> hang = {1};
```

### Bước 2

Lặp từ:

```text
i = 1
```

đến:

```text
rowIndex
```

### Bước 3

Mỗi vòng lặp, thêm một phần tử `1` vào cuối:

```text
hang.push_back(1);
```

### Bước 4

Duyệt từ:

```text
i - 1
```

về:

```text
1
```

### Bước 5

Cập nhật:

```text
hang[j] = hang[j] + hang[j - 1];
```

### Bước 6

Sau khi hoàn thành, `hang` chính là hàng `rowIndex`.

---

# 14. Code C++

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> hang = {1};

        for (int i = 1; i <= rowIndex; i++) {
            hang.push_back(1);

            for (int j = i - 1; j >= 1; j--) {
                hang[j] = hang[j] + hang[j - 1];
            }
        }

        return hang;
    }
};
```

---

# 15. Giải thích code từng phần

## Khởi tạo vector

```cpp
vector<int> hang = {1};
```

Hàng `0` của Pascal's Triangle là:

```text
[1]
```

Do đó ta bắt đầu bằng một vector chứa `1`.

---

## Vòng lặp xây dựng từng hàng

```cpp
for (int i = 1; i <= rowIndex; i++)
```

Ta bắt đầu từ hàng `1`.

Ví dụ:

```text
rowIndex = 4
```

thì `i` lần lượt là:

```text
1
2
3
4
```

Sau mỗi vòng lặp, `hang` trở thành hàng `i`.

---

## Thêm phần tử 1

```cpp
hang.push_back(1);
```

Mọi hàng Pascal đều kết thúc bằng `1`.

Ví dụ:

```text
[1, 3, 3, 1]
```

thêm `1`:

```text
[1, 3, 3, 1, 1]
```

Phần tử cuối cùng này sẽ trở thành phần tử biên của hàng mới.

---

## Duyệt từ phải sang trái

```cpp
for (int j = i - 1; j >= 1; j--)
```

Đây là phần quan trọng nhất.

Ta không duyệt:

```cpp
j = 1 → i - 1
```

mà duyệt:

```cpp
j = i - 1 → 1
```

Mục đích là giữ nguyên các giá trị cũ chưa được sử dụng.

---

## Công thức cập nhật

```cpp
hang[j] = hang[j] + hang[j - 1];
```

Tương đương:

```text
giá trị mới
=
giá trị cũ tại j
+
giá trị cũ tại j-1
```

Đây chính là công thức tạo Pascal's Triangle:

```text
new[j] = old[j-1] + old[j]
```

---

# 16. Ví dụ chạy code

Input:

```text
rowIndex = 3
```

Ban đầu:

```text
hang = [1]
```

### i = 1

```text
push_back(1)

[1, 1]
```

Không có phần tử ở giữa cần cập nhật.

---

### i = 2

```text
push_back(1)

[1, 1, 1]
```

Cập nhật:

```text
j = 1

hang[1] = hang[1] + hang[0]
        = 1 + 1
        = 2
```

Kết quả:

```text
[1, 2, 1]
```

---

### i = 3

```text
push_back(1)

[1, 2, 1, 1]
```

Cập nhật:

```text
j = 2

hang[2] = hang[2] + hang[1]
        = 1 + 2
        = 3
```

```text
[1, 2, 3, 1]
```

Tiếp:

```text
j = 1

hang[1] = hang[1] + hang[0]
        = 2 + 1
        = 3
```

Kết quả:

```text
[1, 3, 3, 1]
```

Trả về:

```text
[1, 3, 3, 1]
```

---

# 17. Độ phức tạp

## Time Complexity

Ở hàng `i`, ta thực hiện khoảng `i` phép cập nhật.

Tổng số phép cập nhật:

```text
1 + 2 + 3 + ... + rowIndex
```

Do đó:

```text
Time = O(rowIndex²)
```

---

## Space Complexity

Ta chỉ sử dụng một vector có tối đa:

```text
rowIndex + 1
```

phần tử.

Do đó:

```text
Space = O(rowIndex)
```

Đây là điểm tối ưu quan trọng so với việc lưu toàn bộ tam giác:

```text
O(rowIndex²)
```

---

# 18. So sánh các phương pháp

| Phương pháp | Time | Space | Ghi chú |
|---|---:|---:|---|
| Xây toàn bộ tam giác | O(n²) | O(n²) | Dễ hiểu nhưng dư bộ nhớ |
| Lưu hàng trước + hàng mới | O(n²) | O(n) | Đã tối ưu bộ nhớ |
| Một vector, cập nhật phải → trái | O(n²) | O(n) | In-place, gọn và hiệu quả |
| Tính trực tiếp bằng tổ hợp | O(n) nếu tính liên tiếp | O(n) | Cần cẩn thận overflow |

Trong bài này, lời giải bằng **một vector và cập nhật từ phải sang trái** là một cách rất tốt để luyện kỹ thuật tối ưu bộ nhớ và cập nhật mảng tại chỗ.

---

# 19. Những lỗi thường gặp

## Lỗi 1 — Duyệt từ trái sang phải

Sai:

```cpp
for (int j = 1; j < i; j++) {
    hang[j] = hang[j] + hang[j - 1];
}
```

Vấn đề:

```text
hang[j - 1]
```

có thể đã bị cập nhật.

Khi đó ta không còn sử dụng dữ liệu của hàng cũ nữa.

---

## Lỗi 2 — Quên thêm số 1 cuối hàng

Nếu không:

```cpp
hang.push_back(1);
```

thì kích thước vector không đủ để tạo hàng mới.

---

## Lỗi 3 — Cập nhật luôn vị trí 0

Không cần cập nhật:

```text
hang[0]
```

vì phần tử đầu tiên của mọi hàng luôn là:

```text
1
```

Do đó vòng lặp chỉ cần:

```cpp
j >= 1
```

---

## Lỗi 4 — Tạo toàn bộ tam giác khi không cần thiết

Cách này vẫn đúng:

```cpp
vector<vector<int>> triangle;
```

nhưng bài chỉ yêu cầu một hàng.

Đây là ví dụ điển hình của việc:

> Đúng về kết quả nhưng chưa tối ưu về bộ nhớ.

---

# 20. Cách tư duy cần ghi nhớ

Bài này không chỉ kiểm tra khả năng nhớ công thức Pascal's Triangle.

Điểm quan trọng hơn là nhận ra:

```text
Chỉ cần hàng trước
        ↓
Không cần lưu toàn bộ lịch sử
        ↓
Có thể dùng một vector
        ↓
Cập nhật tại chỗ
        ↓
Phải chọn đúng thứ tự cập nhật
        ↓
Duyệt từ phải sang trái
```

Đây là một pattern rất đáng nhớ khi học:

```text
Array
↓
Dynamic Programming
↓
Space Optimization
↓
In-place Update
```

---

# 21. Công thức tổng quát cần nhớ

Pascal's Triangle:

```text
a[j] = a[j] + a[j - 1]
```

Nếu cập nhật trên **cùng một mảng**, hãy chú ý thứ tự duyệt.

Trong bài này:

```text
right → left
```

là bắt buộc để đảm bảo `a[j]` và `a[j-1]` vẫn đại diện cho dữ liệu của hàng trước tại thời điểm cần tính.

---

# 22. Template tư duy cho các bài tương tự

Khi gặp bài yêu cầu:

- Xây dựng trạng thái mới từ trạng thái cũ.
- Trạng thái mới phụ thuộc vào các phần tử gần nhau.
- Muốn giảm từ hai mảng xuống một mảng.
- Có thể cập nhật trực tiếp trên mảng.

Hãy tự hỏi:

```text
1. Trạng thái mới phụ thuộc vào vị trí nào?
2. Nếu ghi đè, dữ liệu cũ nào sẽ bị mất?
3. Nên duyệt trái → phải hay phải → trái?
4. Có thể cập nhật in-place không?
5. Bộ nhớ có giảm từ O(n²) xuống O(n) được không?
```

Đây là kỹ thuật có thể áp dụng cho nhiều bài Dynamic Programming khác.

---

# 23. Kết luận

Lời giải tối ưu theo hướng xây dựng Pascal's Triangle là:

```text
vector<int> hang = {1};

for mỗi hàng:
    thêm 1 vào cuối

    duyệt từ phải sang trái:
        hang[j] += hang[j - 1]
```

Code hoàn chỉnh:

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> hang = {1};

        for (int i = 1; i <= rowIndex; i++) {
            hang.push_back(1);

            for (int j = i - 1; j >= 1; j--) {
                hang[j] = hang[j] + hang[j - 1];
            }
        }

        return hang;
    }
};
```

### Complexity

```text
Time Complexity:
O(rowIndex²)

Space Complexity:
O(rowIndex)
```

### Kiến thức quan trọng rút ra

```text
Pascal's Triangle
        ↓
DP / xây dựng trạng thái
        ↓
Chỉ giữ trạng thái cần thiết
        ↓
Space Optimization
        ↓
In-place Update
        ↓
Duyệt phải → trái để tránh ghi đè
```
