# Pascal's Triangle — LeetCode

## 1. Tóm tắt bài toán

Bài toán **Pascal's Triangle** yêu cầu xây dựng `numRows` hàng đầu tiên của tam giác Pascal.

Ví dụ với:

```text
numRows = 5
```

kết quả là:

```text
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

Mỗi hàng là một `vector<int>`.

---

# 2. Quan sát quan trọng nhất

Tam giác Pascal có cấu trúc rất đặc biệt.

Hàng đầu tiên:

```text
[1]
```

Hàng thứ hai:

```text
[1, 1]
```

Từ hàng thứ ba trở đi:

- Phần tử đầu tiên luôn là `1`.
- Phần tử cuối cùng luôn là `1`.
- Mỗi phần tử ở giữa được tính bằng **tổng của hai phần tử ngay phía trên nó**.

Ví dụ:

```text
        1
       / \
      1   1

        1
       / \
      1   1
     / \
    1   2
```

Để tạo hàng:

```text
[1, 2, 1]
```

ta lấy:

```text
1 = 1
2 = 1 + 1
1 = 1
```

---

# 3. Công thức tổng quát

Gọi:

```text
triangle[i][j]
```

là phần tử ở hàng `i`, cột `j`.

Với:

```text
i >= 1
```

và:

```text
1 <= j < i
```

ta có:

```text
triangle[i][j]
=
triangle[i-1][j-1]
+
triangle[i-1][j]
```

Ví dụ:

```text
           1
         1   1
       1   2   1
     1   3   3   1
```

Để tính số `3` ở hàng thứ tư:

```text
3 = 1 + 2
```

Và số `3` tiếp theo:

```text
3 = 2 + 1
```

Đây chính là recurrence relation của bài toán.

---

# 4. Phương hướng tiếp cận

Ta xây dựng từng hàng từ trên xuống dưới.

Giả sử đã có:

```text
previous = [1, 3, 3, 1]
```

Ta muốn tạo hàng tiếp theo:

```text
[1, 4, 6, 4, 1]
```

Ta làm như sau:

### Bước 1: Thêm `1` ở đầu

```text
[1]
```

### Bước 2: Tính các phần tử ở giữa

```text
1 + 3 = 4
3 + 3 = 6
3 + 1 = 4
```

Kết quả:

```text
[1, 4, 6, 4]
```

### Bước 3: Thêm `1` ở cuối

```text
[1, 4, 6, 4, 1]
```

Sau đó dùng hàng vừa tạo làm cơ sở để xây hàng tiếp theo.

---

# 5. Ví dụ chạy từng bước

Giả sử:

```text
numRows = 5
```

## Hàng 1

Khởi tạo:

```text
[1]
```

Triangle:

```text
[
    [1]
]
```

---

## Hàng 2

Từ:

```text
[1]
```

tạo:

```text
[1, 1]
```

Triangle:

```text
[
    [1],
    [1,1]
]
```

---

## Hàng 3

Từ:

```text
[1,1]
```

ta có:

```text
1
1 + 1 = 2
1
```

Kết quả:

```text
[1,2,1]
```

---

## Hàng 4

Từ:

```text
[1,2,1]
```

ta có:

```text
1
1 + 2 = 3
2 + 1 = 3
1
```

Kết quả:

```text
[1,3,3,1]
```

---

## Hàng 5

Từ:

```text
[1,3,3,1]
```

ta có:

```text
1
1 + 3 = 4
3 + 3 = 6
3 + 1 = 4
1
```

Kết quả:

```text
[1,4,6,4,1]
```

Cuối cùng:

```text
[
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1],
    [1,4,6,4,1]
]
```

---

# 6. Cách cài đặt trực tiếp

Một cách rất rõ ràng là lưu toàn bộ tam giác:

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> triangle;

        for (int i = 0; i < numRows; ++i) {
            vector<int> row(i + 1, 1);

            for (int j = 1; j < i; ++j) {
                row[j] = triangle[i - 1][j - 1]
                       + triangle[i - 1][j];
            }

            triangle.push_back(row);
        }

        return triangle;
    }
};
```

Đây là lời giải nên dùng cho đúng bài **Pascal's Triangle**, bởi vì đề bài yêu cầu trả về **toàn bộ tam giác**.

---

# 7. Giải thích code từng bước

## Khởi tạo kết quả

```cpp
vector<vector<int>> triangle;
```

`triangle` sẽ chứa toàn bộ các hàng.

Ví dụ sau khi chạy một phần:

```text
triangle =
[
    [1],
    [1,1],
    [1,2,1]
]
```

---

## Duyệt từng hàng

```cpp
for (int i = 0; i < numRows; ++i)
```

Ta dùng `i` là index của hàng.

Hàng `i` có chính xác:

```text
i + 1
```

phần tử.

Ví dụ:

```text
i = 0 -> [1]             -> 1 phần tử
i = 1 -> [1,1]           -> 2 phần tử
i = 2 -> [1,2,1]         -> 3 phần tử
i = 3 -> [1,3,3,1]       -> 4 phần tử
```

---

# 8. Tại sao tạo row bằng `i + 1` phần tử?

Ta viết:

```cpp
vector<int> row(i + 1, 1);
```

Điều này có nghĩa:

> Tạo vector có `i + 1` phần tử và tất cả ban đầu đều bằng `1`.

Ví dụ:

```cpp
i = 0
```

thì:

```cpp
vector<int> row(1, 1);
```

cho:

```text
[1]
```

---

Với:

```cpp
i = 3
```

thì:

```cpp
vector<int> row(4, 1);
```

cho:

```text
[1,1,1,1]
```

Sau đó ta chỉ cần tính lại các phần tử ở giữa.

Đây là một cách viết rất tiện vì:

> Hai đầu của mọi hàng luôn là `1`.

---

# 9. Tính các phần tử ở giữa

Code:

```cpp
for (int j = 1; j < i; ++j) {
    row[j] = triangle[i - 1][j - 1]
           + triangle[i - 1][j];
}
```

Tại sao:

```text
j = 1
```

?

Vì:

```text
row[0] = 1
```

đã đúng.

Tại sao:

```text
j < i
```

?

Vì hàng `i` có phần tử cuối cùng ở:

```text
index = i
```

và:

```text
row[i] = 1
```

đã đúng.

Do đó ta chỉ cần tính:

```text
1 <= j <= i - 1
```

hay:

```cpp
1 <= j < i
```

---

# 10. Công thức trong code

Dòng:

```cpp
row[j] = triangle[i - 1][j - 1]
       + triangle[i - 1][j];
```

chính là công thức Pascal:

```text
current[i][j]
=
previous[i-1][j-1]
+
previous[i-1][j]
```

Ví dụ muốn tính:

```text
[1, 4, 6, 4, 1]
```

phần tử `6` có index `2`.

Ta lấy:

```text
triangle[3][1] + triangle[3][2]
```

tức:

```text
3 + 3 = 6
```

---

# 11. Thêm hàng vào kết quả

Sau khi tạo xong:

```cpp
triangle.push_back(row);
```

Ví dụ:

```text
row = [1,3,3,1]
```

thì:

```text
triangle =
[
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1]
]
```

---

# 12. Tại sao không cần xử lý riêng hàng đầu tiên?

Một số implementation có thể viết:

```cpp
triangle.push_back({1});

for (...) {
    ...
}
```

Nhưng không cần thiết.

Với cách:

```cpp
for (int i = 0; i < numRows; ++i) {
    vector<int> row(i + 1, 1);
    ...
}
```

hàng đầu tiên tự động được xử lý.

Khi:

```text
i = 0
```

thì:

```cpp
vector<int> row(1, 1);
```

tạo:

```text
[1]
```

Sau đó:

```cpp
for (int j = 1; j < 0; ++j)
```

không chạy lần nào.

Ta chỉ:

```cpp
triangle.push_back([1]);
```

rất tự nhiên.

---

# 13. Độ phức tạp

Đây là phần cần chú ý vì output của bài toán vốn đã rất lớn.

Giả sử cần tạo `n` hàng.

Số phần tử trong tam giác là:

```text
1 + 2 + 3 + ... + n
```

Theo công thức tổng:

```text
n(n + 1) / 2
```

Do đó số phần tử output là:

```text
O(n²)
```

Vì chúng ta phải tạo và trả về toàn bộ `n(n+1)/2` phần tử, không thể làm tốt hơn `O(n²)` về thời gian nếu xét cả việc tạo output.

## Time Complexity

```text
O(n²)
```

## Space Complexity

Kết quả chứa:

```text
n(n + 1) / 2
```

phần tử.

Do đó:

```text
O(n²)
```

Đây là **output space** — bộ nhớ cần thiết để lưu kết quả trả về.

---

# 14. Có thể tối ưu Space xuống O(n) không?

Nếu bài toán chỉ yêu cầu:

> Tính một hàng cụ thể của Pascal's Triangle

thì có thể dùng `O(n)` space.

Nhưng bài **Pascal's Triangle** yêu cầu trả về:

```text
vector<vector<int>>
```

chứa **toàn bộ các hàng**.

Do đó output bắt buộc cần:

```text
O(n²)
```

space.

Ta không thể trả về toàn bộ tam giác với chỉ `O(n)` bộ nhớ.

Đây là một điểm rất quan trọng khi phân tích complexity:

> Không phải mọi bài toán đều cần cố gắng ép space xuống `O(1)`. Nếu output bản thân đã có kích thước `O(n²)`, thì `O(n²)` output space là điều không thể tránh.

---

# 15. Có thể dùng công thức tổ hợp không?

Có.

Các phần tử của Pascal's Triangle chính là các hệ số nhị thức:

```text
C(i, j)
```

với:

```text
C(i, j) = i! / (j! (i-j)!)
```

Ví dụ:

```text
[1, 4, 6, 4, 1]
```

tương ứng với:

```text
C(4,0) C(4,1) C(4,2) C(4,3) C(4,4)
```

tức:

```text
1  4  6  4  1
```

Về mặt toán học, điều này hoàn toàn đúng.

Nhưng dùng factorial để tính từng phần tử thường không phải cách tốt nhất cho bài này.

---

# 16. Tại sao không nên tính trực tiếp bằng factorial?

Công thức:

```text
C(n,k) = n! / (k!(n-k)!)
```

có một số vấn đề:

### 1. Tính factorial không cần thiết

Ta đang lặp lại rất nhiều phép tính.

### 2. Dễ overflow

Ngay cả khi kết quả cuối cùng còn nằm trong kiểu dữ liệu, các giá trị factorial trung gian có thể cực kỳ lớn.

Ví dụ:

```text
20!
```

đã rất lớn.

### 3. Không tận dụng cấu trúc bài toán

Pascal's Triangle đã cho ta recurrence rất đẹp:

```text
C(n,k) = C(n-1,k-1) + C(n-1,k)
```

Đây chính là công thức mà ta đang sử dụng.

Vì vậy xây từng hàng từ hàng trước là cách tự nhiên hơn.

---

# 17. Một cách viết khác

Ta có thể viết:

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;

        for (int i = 0; i < numRows; ++i) {
            vector<int> row;

            row.push_back(1);

            for (int j = 1; j < i; ++j) {
                row.push_back(ans[i - 1][j - 1]
                            + ans[i - 1][j]);
            }

            row.push_back(1);

            ans.push_back(row);
        }

        return ans;
    }
};
```

Cách này cũng rất dễ đọc.

Khác biệt là thay vì:

```cpp
vector<int> row(i + 1, 1);
```

ta tạo:

```text
1
+
các phần tử ở giữa
+
1
```

---

# 18. So sánh hai cách xây row

## Cách 1

```cpp
vector<int> row(i + 1, 1);

for (int j = 1; j < i; ++j) {
    row[j] = ...
}
```

Ưu điểm:

- Ngắn.
- Tận dụng việc hai đầu luôn là `1`.
- Không cần `push_back` nhiều lần.

---

## Cách 2

```cpp
vector<int> row;

row.push_back(1);

for (...) {
    row.push_back(...);
}

row.push_back(1);
```

Ưu điểm:

- Thể hiện trực quan cấu trúc:
  ```text
  1 + middle + 1
  ```
- Dễ hiểu khi mới học.

Cả hai đều có complexity giống nhau.

---

# 19. Edge case: `numRows = 1`

Input:

```text
numRows = 1
```

Kết quả:

```text
[[1]]
```

Code xử lý tự nhiên:

```cpp
i = 0
row = [1]
```

Không có phần tử ở giữa.

---

# 20. Edge case: `numRows = 2`

Kết quả:

```text
[
    [1],
    [1,1]
]
```

Với:

```text
i = 1
```

vòng:

```cpp
for (int j = 1; j < i; ++j)
```

tức:

```cpp
for (int j = 1; j < 1; ++j)
```

không chạy.

`row` ban đầu đã là:

```text
[1,1]
```

nên hoàn toàn chính xác.

---

# 21. Edge case: `numRows = 0`

Theo constraints thông thường của bài LeetCode này, `numRows >= 1`.

Nhưng nếu giả sử:

```text
numRows = 0
```

thì vòng lặp không chạy và:

```cpp
triangle
```

vẫn là:

```text
[]
```

Đây cũng là behavior hợp lý.

---

# 22. Một lỗi thường gặp

Có người viết:

```cpp
for (int j = 0; j < i; ++j) {
    row[j] = ...
}
```

Cách này có thể làm sai phần tử đầu tiên.

Vì:

```text
row[0] = 1
```

không nên được tính từ công thức hai phần tử phía trên.

Tương tự, phần tử cuối:

```text
row[i] = 1
```

cũng không cần tính.

Do đó chỉ cần:

```cpp
for (int j = 1; j < i; ++j)
```

---

# 23. Một lỗi khác: truy cập hàng trước khi tồn tại

Công thức:

```cpp
triangle[i - 1][j]
```

chỉ hợp lệ khi:

```text
i >= 1
```

Vì vậy nếu xử lý hàng đầu tiên riêng biệt thì phải cẩn thận.

Ví dụ cách viết:

```cpp
triangle.push_back({1});

for (int i = 1; i < numRows; ++i) {
    ...
}
```

sẽ an toàn.

Còn implementation trong file này bắt đầu từ:

```cpp
i = 0
```

nhưng không truy cập:

```cpp
triangle[i - 1]
```

vì:

```cpp
j = 1
j < 0
```

không bao giờ đúng.

---

# 24. Có cần Dynamic Programming không?

Về bản chất, đây là một dạng **Dynamic Programming rất đơn giản**.

Ta sử dụng kết quả của hàng trước để xây hàng hiện tại:

```text
row[i-1]
    ↓
row[i]
```

Recurrence:

```text
dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
```

Nhưng khi giải LeetCode, không nhất thiết phải gọi đây là một bài DP phức tạp.

Nó đơn giản hơn rất nhiều:

```text
Previous row
     ↓
tính Current row
     ↓
lưu Current row
     ↓
dùng Current row làm Previous row
```

---

# 25. Mối liên hệ với tổ hợp

Pascal's Triangle có công thức:

```text
C(n,k)
```

và tính chất:

```text
C(n,k)
=
C(n-1,k-1)
+
C(n-1,k)
```

Ví dụ:

```text
C(4,2)
=
C(3,1) + C(3,2)
=
3 + 3
=
6
```

Đây chính là số `6` trong:

```text
[1,4,6,4,1]
```

Vì vậy bài toán vừa có:

- Góc nhìn algorithmic: xây hàng từ hàng trước.
- Góc nhìn toán học: các phần tử là hệ số nhị thức.

Hiểu cả hai sẽ giúp ghi nhớ công thức tốt hơn.

---

# 26. Vì sao `vector<vector<int>>` là cấu trúc dữ liệu phù hợp?

Kết quả có dạng:

```text
[
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1],
    ...
]
```

Mỗi hàng có số lượng phần tử khác nhau.

Do đó không thể dùng một `vector<int>` đơn giản để biểu diễn trực tiếp toàn bộ cấu trúc mà vẫn giữ ranh giới giữa các hàng một cách tự nhiên.

`vector<vector<int>>` cho phép:

```cpp
triangle[i]
```

lấy toàn bộ hàng `i`.

Và:

```cpp
triangle[i][j]
```

lấy phần tử thứ `j` của hàng `i`.

---

# 27. Lời giải tối ưu nên submit

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> triangle;

        for (int i = 0; i < numRows; ++i) {
            vector<int> row(i + 1, 1);

            for (int j = 1; j < i; ++j) {
                row[j] = triangle[i - 1][j - 1]
                       + triangle[i - 1][j];
            }

            triangle.push_back(row);
        }

        return triangle;
    }
};
```

Complexity:

```text
Time:  O(numRows²)
Space: O(numRows²)  // output
```

Đây là complexity phù hợp và về bản chất là tối ưu cho việc **trả về toàn bộ tam giác**.

---

# 28. Nếu muốn code dễ đọc nhất

Có thể dùng version sau:

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;

        for (int i = 0; i < numRows; ++i) {
            vector<int> row(i + 1, 1);

            for (int j = 1; j < i; ++j) {
                row[j] = ans[i - 1][j - 1]
                       + ans[i - 1][j];
            }

            ans.push_back(row);
        }

        return ans;
    }
};
```

Đây là version nên ưu tiên khi học vì tên `ans` và `row` khá trực quan.

---

# 29. Pattern cần ghi nhớ

Khi gặp bài toán có dạng:

> Xây dựng một cấu trúc mà mỗi phần tử phụ thuộc vào các phần tử ở bước trước.

Hãy nghĩ đến:

```text
Previous state
      ↓
Recurrence
      ↓
Current state
      ↓
Store result
      ↓
Continue
```

Với Pascal's Triangle:

```text
previous[j-1] + previous[j]
```

là recurrence chính.

---

# 30. Tóm tắt toàn bộ tư duy

Ta có:

```text
Pascal's Triangle
       ↓
Mỗi hàng có i + 1 phần tử
       ↓
Phần tử đầu = 1
Phần tử cuối = 1
       ↓
Phần tử giữa =
2 phần tử ngay phía trên cộng lại
       ↓
row[j] = previous[j-1] + previous[j]
       ↓
Xây từng hàng từ trên xuống
```

Ví dụ:

```text
[1]
  ↓
[1,1]
  ↓
[1,2,1]
  ↓
[1,3,3,1]
  ↓
[1,4,6,4,1]
```

Số lượng phần tử output là:

```text
1 + 2 + ... + n
= n(n+1)/2
= O(n²)
```

Vì vậy:

```text
Time:  O(n²)
Space: O(n²) output
```

## Core insight

> **Không cần tính từng phần tử bằng factorial hay thử các tổ hợp. Chỉ cần tận dụng cấu trúc của Pascal's Triangle: mỗi phần tử ở giữa bằng tổng của hai phần tử ngay phía trên.**

Đây là cách tiếp cận đơn giản, tự nhiên, dễ chứng minh và phù hợp trực tiếp với yêu cầu trả về toàn bộ tam giác.
