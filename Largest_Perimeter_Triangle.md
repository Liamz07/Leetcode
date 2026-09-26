# Largest Perimeter Triangle — LeetCode

## 1. Đề bài

Cho một mảng số nguyên `nums`, trong đó `nums[i]` biểu diễn độ dài của một cạnh.

Hãy chọn **3 cạnh** để tạo thành một tam giác có **chu vi lớn nhất**.

Nếu không thể tạo thành bất kỳ tam giác nào, trả về `0`.

### Ví dụ

```text
Input:  nums = [2, 1, 2]
Output: 5
```

Ta có thể chọn `1, 2, 2`.

Vì:

```text
1 + 2 > 2
```

nên 3 cạnh này tạo thành tam giác và chu vi là:

```text
1 + 2 + 2 = 5
```

---

## 2. Phân tích điều kiện tạo thành tam giác

Với ba cạnh có độ dài:

```text
a <= b <= c
```

Ba cạnh này tạo thành một tam giác khi và chỉ khi:

```text
a + b > c
```

Đây là điều kiện quan trọng nhất của bài toán.

### Tại sao chỉ cần kiểm tra `a + b > c`?

Ba bất đẳng thức của tam giác là:

```text
a + b > c
a + c > b
b + c > a
```

Do:

```text
a <= b <= c
```

nên:

```text
a + c >= b + c > b
```

và:

```text
b + c >= a + c > a
```

Hai điều kiện còn lại luôn đúng.

Vì vậy, sau khi biết ba cạnh theo thứ tự tăng dần, ta chỉ cần kiểm tra:

```cpp
a + b > c
```

---

# 3. Mục tiêu của bài toán

Ta cần tối đa hóa:

```text
a + b + c
```

nhưng đồng thời phải thỏa:

```text
a + b > c
```

Một cách tiếp cận trực tiếp là thử mọi bộ ba:

```text
for i
    for j
        for k
```

Tuy nhiên cách này có độ phức tạp:

```text
O(n^3)
```

và không cần thiết.

Ta cần khai thác tính chất của bài toán để giảm số lần thử.

---

# 4. Ý tưởng quan trọng: Sắp xếp mảng

Ta sắp xếp mảng theo thứ tự tăng dần:

```text
nums = [1, 2, 2, 3, 4, 5]
```

sau khi sort vẫn là:

```text
1 2 2 3 4 5
```

Giả sử đang xét ba phần tử liên tiếp:

```text
nums[i - 2], nums[i - 1], nums[i]
```

Do mảng đã được sắp xếp:

```text
nums[i - 2] <= nums[i - 1] <= nums[i]
```

nên chỉ cần kiểm tra:

```cpp
nums[i - 2] + nums[i - 1] > nums[i]
```

Nếu đúng, ta có một tam giác.

Nhưng tại sao có thể **duyệt từ phải sang trái và trả về ngay tam giác đầu tiên tìm được**?

Đây chính là phần quan trọng nhất của bài toán.

---

# 5. Vì sao phải xét cạnh lớn nhất trước?

Sau khi sắp xếp:

```text
a1 <= a2 <= a3 <= ... <= an
```

Ta muốn tìm tam giác có chu vi lớn nhất.

Cạnh lớn nhất của một tam giác tối ưu càng lớn thì chu vi có khả năng càng lớn.

Vì vậy, ta xét:

```text
an
```

trước.

Nếu `an` có thể trở thành cạnh lớn nhất của một tam giác, ta muốn chọn hai cạnh lớn nhất còn lại:

```text
a(n-1), a(n-2)
```

Bộ ba:

```text
a(n-2), a(n-1), an
```

có tổng lớn nhất trong tất cả các bộ ba có `an` là cạnh lớn nhất.

Nếu bộ ba này **không tạo thành tam giác**, tức:

```text
a(n-2) + a(n-1) <= an
```

thì mọi cặp cạnh nhỏ hơn `a(n-2), a(n-1)` cũng không thể tạo thành tam giác với `an`.

Do đó không cần thử các bộ ba khác có `an`.

Ta chuyển sang cạnh lớn nhất tiếp theo:

```text
a(n-1)
```

và tiếp tục quá trình.

---

# 6. Tại sao chỉ cần xét 3 phần tử liên tiếp?

Giả sử sau khi sort:

```text
a <= b <= c <= d
```

Ta đang xét `d`.

Bộ ba có tổng lớn nhất khi `d` là cạnh lớn nhất chính là:

```text
b, c, d
```

Nếu:

```text
b + c <= d
```

thì với mọi:

```text
x <= b
y <= c
```

ta luôn có:

```text
x + y <= b + c <= d
```

Do đó:

```text
x + y <= d
```

và không thể tạo thành tam giác.

Vì vậy, khi xét `d`, chỉ cần kiểm tra:

```text
b + c > d
```

Không cần thử các cặp nhỏ hơn.

Đây là lý do thuật toán chỉ cần xét **ba phần tử liên tiếp sau khi sắp xếp**.

---

# 7. Vì sao duyệt từ phải sang trái đảm bảo chu vi lớn nhất?

Sau khi sort:

```text
a1 <= a2 <= ... <= an
```

Ta xét:

```text
an
```

trước.

Nếu:

```text
a(n-2) + a(n-1) > an
```

thì:

```text
a(n-2) + a(n-1) + an
```

là chu vi lớn nhất có thể đạt được.

### Chứng minh

Vì `an` là phần tử lớn nhất trong toàn bộ mảng.

Với mọi bộ ba khác:

```text
x <= an
y <= a(n-1)
z <= a(n-2)
```

nên:

```text
x + y + z <= a(n-2) + a(n-1) + an
```

Do đó bộ ba hiện tại có tổng lớn nhất.

Nếu nó tạo thành tam giác, ta có ngay đáp án.

---

# 8. Nếu bộ ba lớn nhất không tạo thành tam giác thì sao?

Giả sử:

```text
a <= b <= c <= d
```

và:

```text
b + c <= d
```

Khi đó không có tam giác nào sử dụng `d`.

Bởi vì `b` và `c` đã là hai cạnh lớn nhất có thể chọn cùng với `d`.

Nếu ngay cả:

```text
b + c <= d
```

thì chọn hai cạnh nhỏ hơn sẽ chỉ làm tổng nhỏ hơn hoặc bằng:

```text
b + c
```

nên chắc chắn không thể thỏa:

```text
x + y > d
```

Vì vậy ta loại `d`.

Sau đó xét:

```text
c
```

làm cạnh lớn nhất.

Đây chính là lý do cho vòng lặp:

```cpp
for (int i = nums.size() - 1; i >= 2; i--)
```

---

# 9. Thuật toán tối ưu

## Bước 1: Sắp xếp

Sắp xếp `nums` tăng dần:

```cpp
sort(nums.begin(), nums.end());
```

## Bước 2: Duyệt từ cuối mảng

Mỗi lần lấy:

```text
nums[i - 2]
nums[i - 1]
nums[i]
```

Trong đó:

- `nums[i]` là cạnh lớn nhất.
- `nums[i - 1]` là cạnh lớn thứ hai.
- `nums[i - 2]` là cạnh lớn thứ ba.

## Bước 3: Kiểm tra điều kiện tam giác

```cpp
if (nums[i - 2] + nums[i - 1] > nums[i])
```

Nếu đúng:

```cpp
return nums[i - 2] + nums[i - 1] + nums[i];
```

Ta có thể `return` ngay vì đây là tam giác có chu vi lớn nhất.

## Bước 4: Nếu không hợp lệ

Giảm `i`:

```text
i--
```

và xét cạnh lớn nhất tiếp theo.

## Bước 5: Không tìm được

Nếu duyệt hết mà không có bộ ba hợp lệ:

```cpp
return 0;
```

---

# 10. Ví dụ mô phỏng

Xét:

```text
nums = [3, 6, 2, 3]
```

### Bước 1: Sort

```text
[2, 3, 3, 6]
```

### Lần 1

Xét:

```text
2, 3, 6
```

Kiểm tra:

```text
2 + 3 > 6
5 > 6
```

Sai.

Không thể tạo tam giác với `6` vì `2` và `3` đã là hai cạnh lớn nhất có thể chọn bên cạnh `6`.

---

### Lần 2

Xét:

```text
2, 3, 3
```

Kiểm tra:

```text
2 + 3 > 3
5 > 3
```

Đúng.

Chu vi:

```text
2 + 3 + 3 = 8
```

Vậy đáp án là:

```text
8
```

---

# 11. Ví dụ khác

```text
nums = [1, 2, 1]
```

Sau khi sort:

```text
[1, 1, 2]
```

Kiểm tra:

```text
1 + 1 > 2
```

tức:

```text
2 > 2
```

Sai.

Ba cạnh:

```text
1, 1, 2
```

không tạo thành tam giác vì đây là tam giác suy biến.

Không còn bộ ba nào khác.

Kết quả:

```text
0
```

---

# 12. Tại sao phải dùng `>` chứ không phải `>=`?

Đây là một lỗi rất dễ mắc.

Điều kiện đúng là:

```cpp
a + b > c
```

Không phải:

```cpp
a + b >= c
```

Ví dụ:

```text
1, 2, 3
```

Ta có:

```text
1 + 2 = 3
```

Ba cạnh này không tạo thành một tam giác thực sự.

Do đó:

```cpp
1 + 2 > 3
```

là `false`.

---

# 13. Độ phức tạp

## Thời gian

Bước tốn nhiều thời gian nhất là:

```cpp
sort(nums.begin(), nums.end());
```

Độ phức tạp:

```text
O(n log n)
```

Sau đó ta duyệt mảng một lần:

```text
O(n)
```

Tổng:

```text
O(n log n) + O(n)
```

suy ra:

```text
O(n log n)
```

## Bộ nhớ

Ngoài không gian mà `sort` sử dụng nội bộ, thuật toán không tạo thêm cấu trúc dữ liệu phụ đáng kể.

Có thể xem không gian phụ ở mức:

```text
O(1)
```

nếu không tính không gian phụ do hàm `sort` sử dụng.

---

# 14. So sánh với cách brute force

Một cách đơn giản là thử mọi bộ ba:

```text
(i, j, k)
```

Số bộ ba có thể chọn là:

```text
C(n, 3)
```

nên độ phức tạp:

```text
O(n^3)
```

Trong khi đó, cách tối ưu:

```text
Sort + Greedy
```

có độ phức tạp:

```text
O(n log n)
```

Đây là sự cải thiện rất lớn khi `n` lớn.

---

# 15. Tư duy Greedy của bài toán

Bài toán này có thể nhìn dưới góc độ **Greedy (tham lam)**.

Ta muốn chu vi lớn nhất nên:

1. Sắp xếp các cạnh.
2. Xét cạnh lớn nhất trước.
3. Với cạnh lớn nhất hiện tại, chọn hai cạnh lớn nhất còn lại.
4. Nếu tạo thành tam giác thì dừng ngay.
5. Nếu không, bỏ cạnh lớn nhất hiện tại và xét cạnh tiếp theo.

Điểm quan trọng là:

> Khi đã cố định cạnh lớn nhất `c`, việc chọn hai cạnh lớn nhất còn lại là lựa chọn duy nhất có cơ hội tạo ra chu vi lớn nhất.

Nếu ngay cả hai cạnh lớn nhất cũng không đủ để tạo thành tam giác, mọi cặp nhỏ hơn cũng không thể làm được.

---

# 16. Lời giải C++

```cpp
class Solution {
public:
    int largestPerimeter(vector<int>& nums) {
        // Sắp xếp các cạnh theo thứ tự tăng dần
        sort(nums.begin(), nums.end());

        // Duyệt từ cạnh lớn nhất về cạnh nhỏ hơn
        for (int i = nums.size() - 1; i >= 2; i--) {
            // nums[i] là cạnh lớn nhất
            // nums[i - 1] và nums[i - 2] là hai cạnh lớn tiếp theo

            if (nums[i - 2] + nums[i - 1] > nums[i]) {
                // Ba cạnh tạo thành tam giác
                // Vì đang duyệt từ lớn xuống nhỏ,
                // đây chính là tam giác có chu vi lớn nhất.
                return nums[i - 2] + nums[i - 1] + nums[i];
            }
        }

        // Không tồn tại bộ ba cạnh tạo thành tam giác
        return 0;
    }
};
```

---

# 17. Giải thích từng phần code

### Sắp xếp

```cpp
sort(nums.begin(), nums.end());
```

Sau câu lệnh này:

```text
nums[0] <= nums[1] <= ... <= nums[n-1]
```

Điều này cho phép ta dễ dàng xác định cạnh lớn nhất trong mỗi bộ ba.

---

### Vòng lặp

```cpp
for (int i = nums.size() - 1; i >= 2; i--)
```

Ta bắt đầu từ phần tử cuối cùng vì muốn ưu tiên cạnh lớn nhất.

`i >= 2` để đảm bảo vẫn tồn tại:

```text
i - 2
i - 1
i
```

---

### Kiểm tra tam giác

```cpp
if (nums[i - 2] + nums[i - 1] > nums[i])
```

Vì:

```text
nums[i - 2] <= nums[i - 1] <= nums[i]
```

nên đây là điều kiện duy nhất cần kiểm tra.

---

### Trả về ngay

```cpp
return nums[i - 2] + nums[i - 1] + nums[i];
```

Không cần tiếp tục tìm kiếm.

Lý do là ta đang xét các cạnh lớn nhất trước. Khi tìm được tam giác đầu tiên, mọi bộ ba phía sau đều có chu vi không lớn hơn nó.

---

# 18. Các lỗi thường gặp

## Lỗi 1: Dùng `>=`

Sai:

```cpp
if (a + b >= c)
```

Đúng:

```cpp
if (a + b > c)
```

---

## Lỗi 2: Duyệt từ trái sang phải

Nếu duyệt từ nhỏ đến lớn và gặp một tam giác, chưa chắc đó là tam giác có chu vi lớn nhất.

Ví dụ:

```text
[2, 3, 3, 4, 5]
```

Có thể tìm thấy:

```text
2 + 3 + 3 = 8
```

nhưng:

```text
3 + 4 + 5 = 12
```

mới có chu vi lớn hơn.

Do đó phải ưu tiên các cạnh lớn.

---

## Lỗi 3: Kiểm tra mọi bộ ba sau khi sort

Không sai về kết quả, nhưng không cần thiết.

Sau khi sort, chỉ cần:

```text
nums[i-2], nums[i-1], nums[i]
```

và duyệt `i` từ phải sang trái.

---

## Lỗi 4: Nghĩ rằng ba cạnh lớn nhất luôn tạo thành tam giác

Không đúng.

Ví dụ:

```text
[1, 2, 3, 10]
```

Ba cạnh lớn nhất:

```text
2, 3, 10
```

không tạo thành tam giác vì:

```text
2 + 3 <= 10
```

Khi đó phải bỏ `10` và xét tiếp.

---

# 19. Tóm tắt tư duy

Có thể ghi nhớ bài này bằng chuỗi suy luận:

```text
Muốn chu vi lớn nhất
        ↓
Ưu tiên các cạnh lớn nhất
        ↓
Sort tăng dần
        ↓
Duyệt từ phải sang trái
        ↓
Xét 3 phần tử liên tiếp
        ↓
a + b > c ?
   ↙          ↘
 Có            Không
 ↓               ↓
Return          Giảm i
chu vi          và xét tiếp
```

### Công thức cốt lõi

Sau khi sort:

```text
a <= b <= c
```

thì:

```text
a + b > c
```

là điều kiện để tạo thành tam giác.

### Độ phức tạp

```text
Time:  O(n log n)
Space: O(1) phụ (không tính sort)
```

### Mẫu code cần nhớ

```cpp
sort(nums.begin(), nums.end());

for (int i = nums.size() - 1; i >= 2; i--) {
    if (nums[i - 2] + nums[i - 1] > nums[i]) {
        return nums[i - 2] + nums[i - 1] + nums[i];
    }
}

return 0;
```

---

# 20. Kết luận

`Largest Perimeter Triangle` là một bài toán điển hình cho việc kết hợp:

- **Sorting**
- **Greedy**
- **Triangle inequality**

Điểm mấu chốt không nằm ở việc thử nhiều bộ ba, mà ở việc nhận ra rằng sau khi sắp xếp:

> Nếu đang xét một cạnh lớn nhất `c`, thì hai cạnh lớn nhất ngay trước nó là lựa chọn tốt nhất để tạo ra chu vi lớn nhất.

Nếu hai cạnh đó thậm chí không thỏa:

```text
a + b > c
```

thì mọi cặp cạnh nhỏ hơn cũng không thể tạo thành tam giác với `c`.

Nhờ tính chất này, ta có thể loại bỏ rất nhiều trường hợp không cần thiết và giảm độ phức tạp từ:

```text
O(n^3)
```

xuống:

```text
O(n log n)
```

Đây là một mẫu tư duy rất đáng ghi nhớ cho các bài toán **tối ưu hóa trên mảng sau khi sắp xếp**.
