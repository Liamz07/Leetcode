# LeetCode – Plus One

## 1. Tổng quan bài toán

Bài **Plus One** yêu cầu cộng `1` vào một số nguyên dương được biểu diễn bằng một mảng chữ số.

Ví dụ:

```text
digits = [1, 2, 3]
```

biểu diễn số `123`. Sau khi cộng `1`:

```text
123 + 1 = 124
```

Kết quả:

```text
[1, 2, 4]
```

Trường hợp có carry:

```text
[1, 2, 9] -> [1, 3, 0]
```

Và trường hợp đặc biệt:

```text
[9, 9, 9] -> [1, 0, 0, 0]
```

---

# 2. Nhận diện bản chất bài toán

Không nên chuyển toàn bộ mảng thành `int` hoặc `long long`, vì số có thể có rất nhiều chữ số và gây overflow.

Thay vào đó, xử lý trực tiếp từng chữ số.

Vì đang cộng:

```text
+1
```

nên phép cộng bắt đầu từ **chữ số cuối cùng**.

Đây chính là quy tắc cộng thông thường:

```text
   129
 +   1
 -----
   130
```

Ta bắt đầu ở hàng đơn vị. Nếu tạo carry, carry truyền sang bên trái.

---

# 3. Ý tưởng cốt lõi

Với mỗi chữ số từ phải sang trái:

### Nếu chữ số < 9

Ví dụ:

```text
7 + 1 = 8
```

Không có carry. Ta tăng chữ số lên `1` và kết thúc.

### Nếu chữ số = 9

Ta có:

```text
9 + 1 = 10
```

Do đó:

```text
9 -> 0
```

và carry `1` tiếp tục sang chữ số bên trái.

Nếu liên tiếp gặp nhiều số `9`, carry tiếp tục truyền:

```text
1299 + 1
   ↓
1300
```

Nếu toàn bộ chữ số đều là `9`, carry đi ra ngoài chữ số đầu tiên:

```text
999 + 1 = 1000
```

Khi đó phải thêm `1` vào đầu mảng.

---

# 4. Kiểm tra lời giải C++ của bạn

Code bạn cung cấp:

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int idx = digits.size() - 1;
        while (idx >= 0 && digits[idx] + 1 == 10) {
            digits[idx] = 0;
            idx--;
        }
        if (idx == -1) digits.insert(digits.begin(), 1);
        else digits[idx]++;
        return digits;
    }
};
```

## Kết luận

**Lời giải của bạn đúng cho bài Plus One.**

Thuật toán thực hiện chính xác:

1. Bắt đầu từ chữ số cuối.
2. Nếu gặp `9`, đổi thành `0`.
3. Di chuyển sang trái để tiếp tục truyền carry.
4. Khi gặp chữ số khác `9`, tăng nó lên `1`.
5. Nếu đi hết mảng (`idx == -1`), thêm `1` vào đầu.

Đây là lời giải `O(n)` thời gian và `O(1)` extra space.

---

# 5. Phân tích từng dòng code

## Khởi tạo `idx`

```cpp
int idx = digits.size() - 1;
```

`idx` trỏ tới chữ số cuối cùng.

Ví dụ:

```text
digits = [1, 2, 9]

index:   0  1  2
value:   1  2  9
                  ↑
                 idx
```

Ta bắt đầu từ bên phải vì phép cộng bắt đầu ở hàng đơn vị.

---

# 6. Phân tích vòng `while`

Bạn viết:

```cpp
while (idx >= 0 && digits[idx] + 1 == 10) {
    digits[idx] = 0;
    idx--;
}
```

Điều kiện:

```cpp
digits[idx] + 1 == 10
```

vì `digits[idx]` là một chữ số từ `0` đến `9`, nên tương đương với:

```cpp
digits[idx] == 9
```

Do đó có thể đọc vòng lặp là:

> Trong khi vẫn còn vị trí và chữ số hiện tại là `9`, biến nó thành `0` rồi truyền carry sang trái.

---

# 7. Tại sao `9` phải trở thành `0`?

Ví dụ:

```text
129 + 1
```

Ta bắt đầu:

```text
9 + 1 = 10
```

Số `10` được biểu diễn ở hai chữ số:

```text
1 0
↑ ↑
| └─ chữ số hiện tại
└─── carry
```

Vì vậy tại vị trí của `9`:

```text
9 -> 0
```

và carry `1` được xử lý ở chữ số bên trái.

Code:

```cpp
digits[idx] = 0;
idx--;
```

thực hiện đúng hai việc đó.

---

# 8. Dry run: `[1, 2, 9]`

Ban đầu:

```text
digits = [1, 2, 9]
idx = 2
```

`digits[2]` là `9`, nên:

```cpp
digits[2] = 0;
```

Ta có:

```text
[1, 2, 0]
```

Sau đó:

```cpp
idx--;
```

nên:

```text
idx = 1
```

Bây giờ:

```text
digits[1] = 2
```

không phải `9`, nên thoát `while`.

Ta thực hiện:

```cpp
digits[idx]++;
```

tức:

```cpp
digits[1]++;
```

Kết quả:

```text
[1, 3, 0]
```

Đúng:

```text
129 + 1 = 130
```

---

# 9. Dry run: `[1, 9, 9]`

Ban đầu:

```text
[1, 9, 9]
       ↑
      idx
```

Gặp `9`:

```text
[1, 9, 0]
```

sau đó:

```text
idx = 1
```

Tiếp tục gặp `9`:

```text
[1, 0, 0]
```

sau đó:

```text
idx = 0
```

Bây giờ:

```text
digits[0] = 1
```

không phải `9`.

Thoát vòng lặp và tăng:

```text
1 -> 2
```

Kết quả:

```text
[2, 0, 0]
```

Đúng:

```text
199 + 1 = 200
```

---

# 10. Trường hợp quan trọng nhất: `[9, 9, 9]`

Ban đầu:

```text
[9, 9, 9]
       ↑
      idx
```

Xử lý chữ số cuối:

```text
[9, 9, 0]
```

`idx = 1`.

Xử lý tiếp:

```text
[9, 0, 0]
```

`idx = 0`.

Xử lý tiếp:

```text
[0, 0, 0]
```

`idx = -1`.

Lúc này:

```cpp
if (idx == -1)
```

là `true`.

Ta thực hiện:

```cpp
digits.insert(digits.begin(), 1);
```

Kết quả:

```text
[1, 0, 0, 0]
```

Đúng:

```text
999 + 1 = 1000
```

---

# 11. Ý nghĩa của `idx == -1`

Đây là một điều kiện rất quan trọng.

Nếu:

```text
idx == -1
```

thì nghĩa là carry đã đi qua toàn bộ mảng.

Điều đó chỉ xảy ra khi tất cả chữ số đều là `9`.

Ví dụ:

```text
99
```

sau khi truyền carry:

```text
00
```

nhưng kết quả thực tế là:

```text
100
```

Do đó phải thêm `1` vào đầu.

---

# 12. Tại sao không cần biến `carry`?

Cách tổng quát thường thấy là:

```cpp
int carry = 1;
```

rồi cộng:

```cpp
digits[i] + carry
```

Nhưng bài này có một đặc điểm rất thuận lợi:

> Carry ban đầu chắc chắn là `1`, và nó chỉ tiếp tục tồn tại nếu chữ số hiện tại là `9`.

Do đó code của bạn mã hóa trực tiếp quy tắc:

```text
digit == 9
    -> digit = 0
    -> carry tiếp tục

digit != 9
    -> digit++
    -> carry kết thúc
```

Vì vậy không cần biến `carry` riêng.

Đây là một cách tận dụng tốt tính chất riêng của bài.

---

# 13. Có thể viết điều kiện dễ đọc hơn

Bạn viết:

```cpp
digits[idx] + 1 == 10
```

Điều này hoàn toàn đúng.

Nhưng vì `digits[idx]` luôn là một chữ số từ `0` đến `9`, ta có thể viết trực tiếp:

```cpp
digits[idx] == 9
```

Phiên bản dễ đọc hơn:

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int idx = digits.size() - 1;

        while (idx >= 0 && digits[idx] == 9) {
            digits[idx] = 0;
            idx--;
        }

        if (idx == -1) {
            digits.insert(digits.begin(), 1);
        } else {
            digits[idx]++;
        }

        return digits;
    }
};
```

Về thuật toán, hai phiên bản hoàn toàn tương đương.

---

# 14. Tại sao phải duyệt từ phải sang trái?

Vì carry luôn được truyền từ hàng thấp hơn sang hàng cao hơn.

Ví dụ:

```text
   199
 +   1
 -----
   200
```

Quá trình:

```text
9 + 1 = 10
```

→ `9` thành `0`, carry sang trái.

Tiếp:

```text
9 + 1 = 10
```

→ `9` thành `0`, carry sang trái.

Tiếp:

```text
1 + 1 = 2
```

→ carry kết thúc.

Do đó hướng tự nhiên là:

```text
right → left
```

---

# 15. Không nên chuyển thành số nguyên

Không nên làm:

```cpp
int number = 0;

for (int digit : digits) {
    number = number * 10 + digit;
}

number++;
```

Vấn đề là số có thể có rất nhiều chữ số.

Ví dụ:

```text
[9, 9, 9, 9, 9, ...]
```

với hàng trăm hoặc hàng nghìn chữ số không thể chứa trong `int` hay `long long`.

Bài toán dùng `vector<int>` chính là để cho phép biểu diễn số có độ dài lớn.

Do đó phải thao tác trực tiếp trên từng chữ số.

---

# 16. Độ phức tạp

Gọi:

```text
n = digits.size()
```

## Time Complexity

Trường hợp xấu nhất:

```text
[9, 9, 9, ..., 9]
```

phải duyệt qua toàn bộ `n` chữ số:

```text
O(n)
```

Trong trường hợp toàn `9`, thao tác:

```cpp
digits.insert(digits.begin(), 1);
```

cũng có thể phải dịch chuyển toàn bộ phần tử:

```text
O(n)
```

Tổng thể vẫn:

```text
O(n)
```

## Space Complexity

Không tạo một mảng phụ có kích thước `n`.

Chỉ sử dụng:

```cpp
int idx;
```

và sửa trực tiếp `digits`.

Do đó:

```text
O(1) extra space
```

Lưu ý: nếu xét riêng bộ nhớ của vector kết quả khi phải thêm chữ số `1`, output có thể có `n + 1` phần tử. Đây là bộ nhớ của kết quả, không phải một mảng phụ độc lập.

---

# 17. In-place modification

Code của bạn sửa trực tiếp:

```cpp
digits
```

Ví dụ:

```text
[1, 2, 9]
```

được biến thành:

```text
[1, 3, 0]
```

thay vì tạo:

```cpp
vector<int> result;
```

Đây là kỹ thuật **in-place** và giúp giữ extra space ở `O(1)`.

---

# 18. Complexity phụ thuộc vào số lượng `9` ở cuối

Nếu có:

```text
k = số lượng chữ số 9 liên tiếp ở cuối
```

thì khi `k < n`, thực tế ta chỉ cần xử lý khoảng:

```text
O(k + 1)
```

chữ số.

Ví dụ:

```text
[1, 2, 3, 4, 9, 9]
```

chỉ cần xử lý:

```text
9
9
4
```

Các chữ số `1, 2, 3` không cần thay đổi.

Tuy nhiên Big-O theo `n` vẫn là:

```text
O(n)
```

vì trường hợp xấu nhất có thể là:

```text
[9, 9, 9, ..., 9]
```

---

# 19. Invariant của thuật toán

Có thể hiểu invariant như sau:

> Sau khi xử lý các chữ số `9` ở bên phải `idx`, toàn bộ phần suffix đó đã trở thành `0` và carry vẫn đang được truyền tới vị trí `idx`.

Ví dụ:

```text
[2, 9, 9, 9]
       ↑
      idx
```

Sau một bước:

```text
[2, 9, 9, 0]
    ↑
   idx
```

Sau hai bước:

```text
[2, 9, 0, 0]
 ↑
idx
```

Sau ba bước:

```text
[2, 0, 0, 0]
 ↑
idx
```

Bây giờ carry dừng tại `2`:

```text
2 -> 3
```

Kết quả:

```text
[3, 0, 0, 0]
```

---

# 20. Edge Cases

### Không có carry

```text
[1, 2, 3] -> [1, 2, 4]
```

### Một `9` ở cuối

```text
[1, 2, 9] -> [1, 3, 0]
```

### Nhiều `9` ở cuối

```text
[1, 9, 9] -> [2, 0, 0]
```

### Toàn bộ là `9`

```text
[9, 9, 9] -> [1, 0, 0, 0]
```

### Một chữ số

```text
[5] -> [6]
[9] -> [1, 0]
```

---

# 21. Một lỗi thường gặp: chỉ tăng chữ số cuối

Ví dụ:

```cpp
digits[digits.size() - 1]++;
```

sẽ sai với:

```text
[1, 2, 9]
```

vì:

```text
9 + 1 = 10
```

Không thể biểu diễn `10` trong một phần tử chữ số.

Cần:

```text
9 -> 0
carry -> bên trái
```

---

# 22. Một lỗi thường gặp: không xử lý toàn `9`

Nếu sau vòng lặp:

```text
idx = -1
```

mà vẫn thực hiện:

```cpp
digits[idx]++;
```

thì sẽ truy cập index không hợp lệ.

Code của bạn xử lý chính xác bằng:

```cpp
if (idx == -1)
    digits.insert(digits.begin(), 1);
else
    digits[idx]++;
```

---

# 23. Một lỗi tư duy: duyệt từ trái sang phải

Không nên bắt đầu từ:

```text
digits[0]
```

vì chưa biết các chữ số phía sau có tạo carry hay không.

Ví dụ:

```text
[1, 9, 9]
```

Chữ số đầu tiên `1` cuối cùng phải trở thành `2`, nhưng chỉ biết điều đó sau khi carry truyền qua hai chữ số `9`.

Do đó:

```text
right → left
```

là hướng đúng.

---

# 24. Phiên bản tổng quát có biến `carry`

Một cách giải khác:

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int carry = 1;

        for (int i = digits.size() - 1; i >= 0; i--) {
            int sum = digits[i] + carry;

            digits[i] = sum % 10;
            carry = sum / 10;

            if (carry == 0) {
                break;
            }
        }

        if (carry == 1) {
            digits.insert(digits.begin(), 1);
        }

        return digits;
    }
};
```

Cách này tổng quát hơn vì mô hình hóa trực tiếp:

```text
sum
digit
carry
```

Nó gần với các bài toán cộng số lớn hơn.

Tuy nhiên, riêng với `Plus One`, code của bạn ngắn gọn hơn vì tận dụng việc carry ban đầu luôn bằng `1`.

---

# 25. So sánh hai cách

| Tiêu chí | Code của bạn | Phiên bản có `carry` |
|---|---|---|
| Đúng | Có | Có |
| Time | `O(n)` | `O(n)` |
| Extra space | `O(1)` | `O(1)` |
| Dễ hiểu cho Plus One | Rất cao | Cao |
| Tổng quát cho phép cộng | Thấp hơn | Cao hơn |
| Có biến `carry` | Không | Có |

Đối với riêng bài này, cách của bạn là một lời giải rất tự nhiên.

---

# 26. Một lưu ý nhỏ về `digits.size()`

Bạn viết:

```cpp
int idx = digits.size() - 1;
```

Với constraint chuẩn của bài, `digits` không rỗng nên không có vấn đề.

Tuy nhiên `digits.size()` có kiểu `size_t` (unsigned). Nếu muốn viết code tổng quát cho cả vector rỗng, nên xử lý trường hợp đó trước khi tính:

```cpp
digits.size() - 1
```

Ví dụ:

```cpp
if (digits.empty()) {
    return {1};
}
```

Điều này không cần thiết nếu ta dựa vào constraint của LeetCode, nhưng là một điểm kỹ thuật tốt cần biết.

---

# 27. Template tư duy cần ghi nhớ

Khi gặp bài số nguyên được biểu diễn bằng mảng chữ số và cần cộng `1`, hãy nghĩ:

```text
1. Đi từ phải sang trái.
2. Nếu digit == 9:
      digit = 0
      carry tiếp tục sang trái.
3. Nếu digit != 9:
      digit++
      kết thúc.
4. Nếu carry đi qua đầu:
      thêm 1 vào đầu.
```

Visual:

```text
[1, 2, 9, 9]
          ↑
        bắt đầu

[1, 2, 9, 0]
       ↑

[1, 2, 0, 0]
    ↑

[1, 3, 0, 0]
```

---

# 28. Đánh giá lời giải của bạn

| Tiêu chí | Kết quả |
|---|---|
| Đúng bài | Có |
| Đúng logic | Có |
| Xử lý carry | Có |
| Xử lý nhiều `9` | Có |
| Xử lý toàn `9` | Có |
| In-place | Có |
| Time Complexity | `O(n)` |
| Extra Space | `O(1)` |
| Cần thay đổi thuật toán? | Không |
| Cải thiện readability? | Có |

Cải thiện nhỏ đáng cân nhắc:

```cpp
digits[idx] + 1 == 10
```

→

```cpp
digits[idx] == 9
```

Cách sau thể hiện trực tiếp bản chất của thuật toán.

---

# 29. Phiên bản đề xuất

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int idx = digits.size() - 1;

        while (idx >= 0 && digits[idx] == 9) {
            digits[idx] = 0;
            idx--;
        }

        if (idx == -1) {
            digits.insert(digits.begin(), 1);
        } else {
            digits[idx]++;
        }

        return digits;
    }
};
```

Đây gần như chính xác lời giải bạn đã viết, chỉ thay đổi điều kiện để code dễ đọc hơn.

---

# 30. Kết luận

Bài **Plus One** về bản chất là bài về:

```text
Carry Propagation
```

kết hợp với:

```text
Traverse from right to left
```

Sơ đồ tư duy:

```text
                 digits
                    ↓
          bắt đầu từ chữ số cuối
                    ↓
              digit == 9?
              /          \
            YES           NO
             ↓             ↓
          digit = 0     digit++
             ↓             ↓
        đi sang trái      DONE
             ↓
      hết mảng chưa?
        /          \
      YES           NO
       ↓             ↓
  thêm 1 ở đầu   tiếp tục
```

Lời giải của bạn đã triển khai đúng pattern này.

### Complexity

```text
Time  : O(n)
Space : O(1) extra space
```

### Điều quan trọng nhất cần ghi nhớ

> **Khi cộng `1` vào một số được biểu diễn bằng mảng chữ số, hãy bắt đầu từ bên phải. Mỗi chữ số `9` biến thành `0` và truyền carry sang trái; khi gặp chữ số khác `9`, tăng nó lên `1` và kết thúc. Nếu carry đi qua toàn bộ mảng, thêm `1` vào đầu.**
