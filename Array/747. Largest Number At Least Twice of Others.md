# Largest Number At Least Twice of Others

## 1. Thông tin bài toán

**LeetCode:** [Largest Number At Least Twice of Others](https://leetcode.com/problems/largest-number-at-least-twice-of-others/)

Cho một mảng số nguyên `nums`.

Cần tìm chỉ số `maxIndex` của phần tử lớn nhất trong mảng sao cho:

\[
nums[maxIndex] \ge 2 \times nums[i]
\]

với **mọi** chỉ số `i` khác `maxIndex`.

Nếu tồn tại phần tử thỏa mãn điều kiện, trả về chỉ số của nó. Nếu không tồn tại, trả về `-1`.

---

## 2. Ví dụ

### Ví dụ 1

```text
Input: nums = [3,6,1,0]
Output: 1
```

Phần tử lớn nhất là `6`, có chỉ số `1`.

Kiểm tra:

- `6 >= 2 * 3` → `6 >= 6` → đúng
- `6 >= 2 * 1` → `6 >= 2` → đúng
- `6 >= 2 * 0` → `6 >= 0` → đúng

Vậy `6` lớn ít nhất gấp đôi tất cả các phần tử còn lại.

---

### Ví dụ 2

```text
Input: nums = [1,2,3,4]
Output: -1
```

Phần tử lớn nhất là `4`.

Nhưng:

```text
4 >= 2 * 3
4 >= 6
```

là sai.

Vì vậy không có phần tử nào thỏa mãn.

---

## 3. Phân tích điều kiện của bài toán

Đề bài yêu cầu tìm **phần tử lớn nhất** và kiểm tra:

```text
max >= 2 * nums[i]
```

với mọi phần tử còn lại.

Thoạt nhìn, ta có thể:

1. Tìm phần tử lớn nhất.
2. Duyệt lại toàn bộ mảng.
3. Kiểm tra phần tử lớn nhất có lớn ít nhất gấp đôi từng phần tử hay không.

Cách này hoàn toàn đúng.

Tuy nhiên, ta có thể nhận ra một điều quan trọng:

> Nếu phần tử lớn nhất lớn ít nhất gấp đôi **phần tử lớn thứ hai**, thì nó chắc chắn lớn ít nhất gấp đôi tất cả các phần tử còn lại.

Lý do là mọi phần tử còn lại đều không lớn hơn phần tử lớn thứ hai.

Ví dụ:

```text
nums = [3, 6, 1, 0]
```

Hai phần tử lớn nhất là:

```text
6 và 3
```

Chỉ cần kiểm tra:

```text
6 >= 2 * 3
6 >= 6
```

đúng.

Do `1 <= 3` và `0 <= 3`, nên:

```text
6 >= 2 * 1
6 >= 2 * 0
```

cũng chắc chắn đúng.

---

# 4. Ý tưởng tối ưu

Ta chỉ cần tìm:

- `max1`: phần tử lớn nhất.
- `max2`: phần tử lớn thứ hai.
- `index`: vị trí của `max1`.

Sau đó kiểm tra:

```text
max1 >= 2 * max2
```

Nếu đúng:

```text
return index;
```

Nếu sai:

```text
return -1;
```

---

# 5. Tại sao chỉ cần kiểm tra phần tử lớn thứ hai?

Đây là phần quan trọng nhất của bài.

Giả sử:

- `max1` là phần tử lớn nhất.
- `max2` là phần tử lớn thứ hai.

Ta biết:

\[
nums[i] \le max2
\]

với mọi phần tử `nums[i]` khác `max1`.

Bây giờ nếu:

\[
max1 \ge 2 \times max2
\]

thì vì:

\[
nums[i] \le max2
\]

nên:

\[
2 \times nums[i] \le 2 \times max2
\]

Mà:

\[
max1 \ge 2 \times max2
\]

suy ra:

\[
max1 \ge 2 \times nums[i]
\]

Điều này đúng với **mọi phần tử còn lại**.

Vì vậy, chỉ cần kiểm tra phần tử lớn thứ hai.

---

# 6. Ví dụ minh họa

Xét:

```text
nums = [1, 2, 8, 3]
```

Ta có:

```text
max1 = 8
max2 = 3
index = 2
```

Kiểm tra:

```text
8 >= 2 * 3
8 >= 6
```

Đúng.

Do `3` là phần tử lớn thứ hai nên mọi phần tử còn lại đều `<= 3`.

Vì vậy:

```text
8 >= 2 * 1
8 >= 2 * 2
8 >= 2 * 3
```

đều đúng.

Kết quả:

```text
2
```

---

# 7. Trường hợp không thỏa mãn

Xét:

```text
nums = [1, 2, 3, 4]
```

Ta có:

```text
max1 = 4
max2 = 3
```

Kiểm tra:

```text
4 >= 2 * 3
4 >= 6
```

Sai.

Vậy chắc chắn `4` không lớn ít nhất gấp đôi phần tử `3`.

Mà `3` là phần tử lớn thứ hai, nên càng không thể có điều kiện đúng với toàn bộ các phần tử còn lại.

Kết quả:

```text
-1
```

---

# 8. Cách tìm hai phần tử lớn nhất trong một lần duyệt

Ta có thể tìm `max1` và `max2` bằng một vòng lặp duy nhất.

Ban đầu:

```cpp
int max1 = -1;
int max2 = -1;
int index = -1;
```

Khi gặp một phần tử lớn hơn `max1`:

```cpp
if (nums[i] > max1) {
    max2 = max1;
    max1 = nums[i];
    index = i;
}
```

Ý nghĩa:

Giả sử trước đó:

```text
max1 = 8
max2 = 5
```

Nếu gặp:

```text
10
```

thì `10` trở thành lớn nhất.

Giá trị lớn nhất cũ `8` sẽ trở thành lớn thứ hai:

```text
max1 = 10
max2 = 8
```

Đó chính là lý do phải thực hiện:

```cpp
max2 = max1;
```

trước khi cập nhật `max1`.

---

# 9. Nếu phần tử không lớn hơn max1

Nếu:

```cpp
nums[i] <= max1
```

thì `nums[i]` chưa chắc phải bỏ qua.

Nó có thể trở thành `max2`.

Vì vậy ta kiểm tra:

```cpp
else if (nums[i] > max2) {
    max2 = nums[i];
}
```

Ví dụ:

```text
max1 = 10
max2 = 3
```

Gặp:

```text
7
```

thì:

```text
7 <= 10
```

nên `7` không thể trở thành `max1`.

Nhưng:

```text
7 > 3
```

nên nó trở thành `max2`:

```text
max1 = 10
max2 = 7
```

---

# 10. Code lời giải C++

```cpp
class Solution {
public:
    int dominantIndex(vector<int>& nums) {
        int max1 = -1;
        int max2 = -1;
        int index = -1;

        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] > max1) {
                max2 = max1;
                max1 = nums[i];
                index = i;
            }
            else if (nums[i] > max2) {
                max2 = nums[i];
            }
        }

        if (max1 >= 2 * max2) {
            return index;
        }

        return -1;
    }
};
```

---

# 11. Giải thích từng phần code

## Khai báo hàm

```cpp
int dominantIndex(vector<int>& nums)
```

Hàm nhận vào mảng `nums` và trả về:

- chỉ số của phần tử thỏa mãn nếu tồn tại.
- `-1` nếu không tồn tại.

---

## Ba biến quan trọng

```cpp
int max1 = -1;
int max2 = -1;
int index = -1;
```

### `max1`

Lưu phần tử lớn nhất hiện tại.

### `max2`

Lưu phần tử lớn thứ hai hiện tại.

### `index`

Lưu chỉ số của `max1`.

---

## Duyệt mảng

```cpp
for (int i = 0; i < nums.size(); i++)
```

Ta duyệt từng phần tử đúng một lần.

---

## Khi tìm thấy phần tử lớn nhất mới

```cpp
if (nums[i] > max1) {
    max2 = max1;
    max1 = nums[i];
    index = i;
}
```

Có 3 việc:

### Bước 1

```cpp
max2 = max1;
```

Phần tử lớn nhất cũ trở thành phần tử lớn thứ hai.

### Bước 2

```cpp
max1 = nums[i];
```

Phần tử hiện tại trở thành phần tử lớn nhất.

### Bước 3

```cpp
index = i;
```

Lưu lại vị trí của phần tử lớn nhất.

---

## Khi phần tử hiện tại không lớn nhất

```cpp
else if (nums[i] > max2) {
    max2 = nums[i];
}
```

Nếu phần tử hiện tại nhỏ hơn hoặc bằng `max1`, nó vẫn có thể lớn hơn `max2`.

Khi đó cập nhật:

```cpp
max2 = nums[i];
```

---

# 12. Kiểm tra kết quả

Sau khi duyệt xong:

```cpp
if (max1 >= 2 * max2) {
    return index;
}
```

Ta chỉ cần kiểm tra `max1` với `max2`.

Nếu:

```text
max1 >= 2 * max2
```

thì `max1` chắc chắn lớn ít nhất gấp đôi tất cả các phần tử còn lại.

Ngược lại:

```cpp
return -1;
```

---

# 13. Mô phỏng thuật toán

Xét:

```text
nums = [3, 6, 1, 0]
```

### Ban đầu

```text
max1 = -1
max2 = -1
index = -1
```

### i = 0

```text
nums[0] = 3
```

`3 > -1`

Cập nhật:

```text
max1 = 3
max2 = -1
index = 0
```

### i = 1

```text
nums[1] = 6
```

`6 > 3`

Cập nhật:

```text
max2 = 3
max1 = 6
index = 1
```

### i = 2

```text
nums[2] = 1
```

`1 > 6` → sai.

`1 > 3` → sai.

Không thay đổi:

```text
max1 = 6
max2 = 3
index = 1
```

### i = 3

```text
nums[3] = 0
```

Không thay đổi.

Cuối cùng:

```text
max1 = 6
max2 = 3
index = 1
```

Kiểm tra:

```text
6 >= 2 * 3
6 >= 6
```

Đúng.

Trả về:

```text
1
```

---

# 14. Trường hợp có hai phần tử lớn nhất bằng nhau

Ví dụ:

```text
nums = [3, 6, 6]
```

Ta có:

```text
max1 = 6
max2 = 6
```

Kiểm tra:

```text
6 >= 2 * 6
6 >= 12
```

Sai.

Kết quả:

```text
-1
```

Điều này hoàn toàn hợp lý vì không có một phần tử nào lớn ít nhất gấp đôi phần tử `6` còn lại.

---

# 15. Một cách suy nghĩ khác: chỉ cần tìm hai phần tử lớn nhất

Có thể hiểu bài toán theo cách rất ngắn gọn:

> Muốn biết `max1` có lớn ít nhất gấp đôi tất cả những phần tử còn lại hay không, ta chỉ cần quan tâm đến phần tử còn lại lớn nhất.

Phần tử còn lại lớn nhất chính là `max2`.

Do đó bài toán:

```text
Kiểm tra max1 với mọi phần tử
```

được rút gọn thành:

```text
Kiểm tra max1 với max2
```

Đây chính là mấu chốt để có lời giải tối ưu.

---

# 16. Độ phức tạp

## Thời gian

Ta chỉ duyệt mảng một lần:

```text
O(n)
```

Trong đó `n` là số lượng phần tử của mảng.

---

## Bộ nhớ

Chỉ sử dụng một vài biến:

```text
max1
max2
index
```

Không tạo thêm mảng hay cấu trúc dữ liệu phụ.

Do đó:

```text
O(1)
```

---

# 17. So sánh với cách làm khác

## Cách 1: Tìm max rồi duyệt lại

Có thể làm:

```text
- Tìm max
- Duyệt lại mảng để kiểm tra max với từng phần tử
```

Độ phức tạp:

```text
O(n) + O(n) = O(n)
```

Cách này vẫn đạt độ phức tạp thời gian tối ưu về mặt Big-O.

---

## Cách 2: Sắp xếp mảng

Có thể sắp xếp:

```text
sort(nums.begin(), nums.end());
```

Sau đó lấy hai phần tử cuối.

Nhưng:

```text
O(n log n)
```

Trong khi ta không cần sắp xếp toàn bộ mảng.

Chỉ cần tìm hai phần tử lớn nhất là đủ.

---

## Cách 3: Tìm hai phần tử lớn nhất trong một lần duyệt

Đây là cách được sử dụng trong lời giải:

```text
O(n) thời gian
O(1) bộ nhớ
```

Ta không cần:

- sắp xếp.
- tạo mảng phụ.
- duyệt lần thứ hai.

---

# 18. Tóm tắt tư duy giải bài

Khi gặp bài này, có thể suy nghĩ theo chuỗi sau:

```text
Cần tìm phần tử lớn nhất
        ↓
Phần tử đó phải >= 2 * mọi phần tử còn lại
        ↓
Phần tử khó thỏa mãn nhất là phần tử lớn thứ hai
        ↓
Chỉ cần kiểm tra max1 >= 2 * max2
        ↓
Tìm max1, max2 và index trong một lần duyệt
        ↓
O(n) thời gian, O(1) bộ nhớ
```

---

# 19. Công thức cốt lõi cần nhớ

Nếu:

```text
max1 = phần tử lớn nhất
max2 = phần tử lớn thứ hai
```

thì đáp án là:

```text
max1 >= 2 * max2
```

Nếu đúng:

```text
return index của max1
```

Nếu sai:

```text
return -1
```

Đây là toàn bộ mấu chốt của bài **Largest Number At Least Twice of Others**.
