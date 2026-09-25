# Climbing Stairs — LeetCode

**Bài toán:** [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

---

## 1. Mô tả bài toán

Bạn đang đứng ở bậc thang thứ `0` và muốn đi đến bậc thứ `n`.

Mỗi lần di chuyển, bạn chỉ được:

- đi **1 bậc**
- hoặc đi **2 bậc**

Hãy tính **số cách khác nhau** để đi đến bậc thứ `n`.

### Ví dụ

```text
Input: n = 2
Output: 2
```

Có 2 cách:

```text
1 + 1
2
```

---

```text
Input: n = 3
Output: 3
```

Có 3 cách:

```text
1 + 1 + 1
1 + 2
2 + 1
```

---

## 2. Phân tích bản chất bài toán

Điểm quan trọng nhất là:

> Muốn đến bậc `i`, bước cuối cùng chỉ có thể là bước 1 bậc hoặc bước 2 bậc.

Giả sử:

```text
dp[i] = số cách để đi đến bậc i
```

### Trường hợp 1: Bước cuối cùng đi 1 bậc

Nếu bước cuối cùng là từ `i - 1` lên `i`, thì trước đó ta phải ở `i - 1`.

Có:

```text
dp[i - 1]
```

cách để đến `i - 1`.

### Trường hợp 2: Bước cuối cùng đi 2 bậc

Nếu bước cuối cùng là từ `i - 2` lên `i`, thì trước đó ta phải ở `i - 2`.

Có:

```text
dp[i - 2]
```

cách để đến `i - 2`.

Hai trường hợp này không trùng nhau, vì bước cuối cùng khác nhau.

Do đó:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

Đây chính là công thức truy hồi của bài toán.

---

# 3. Xây dựng Dynamic Programming

## 3.1. Định nghĩa trạng thái

Ta định nghĩa:

```cpp
dp[i]
```

là:

> Số cách khác nhau để leo từ bậc `0` đến bậc `i`.

Ví dụ:

```text
dp[0] = 1
dp[1] = 1
dp[2] = 2
dp[3] = 3
dp[4] = 5
dp[5] = 8
```

Ta nhận được:

```text
i:    0  1  2  3  4  5
dp:   1  1  2  3  5  8
```

---

## 3.2. Base Case

### `dp[0] = 1`

Có **1 cách** để ở bậc `0`:

```text
không thực hiện bước nào
```

Điều này có vẻ hơi lạ, nhưng rất quan trọng đối với công thức DP.

Nếu đặt:

```text
dp[0] = 0
```

thì khi tính:

```text
dp[2] = dp[1] + dp[0]
```

ta sẽ nhận được:

```text
dp[2] = 1 + 0 = 1
```

trong khi đáp án đúng là `2`.

Vì vậy:

```cpp
dp[0] = 1;
```

---

### `dp[1] = 1`

Chỉ có một cách:

```text
1
```

nên:

```cpp
dp[1] = 1;
```

---

## 3.3. Công thức chuyển trạng thái

Với `i >= 2`:

```cpp
dp[i] = dp[i - 1] + dp[i - 2];
```

Lý do:

```text
             i
            / \
         i-1   i-2
          |     |
        +1     +2
```

Mọi cách đến `i` đều phải thuộc một trong hai nhóm:

```text
Các cách đến i-1 rồi đi 1
+
Các cách đến i-2 rồi đi 2
```

---

# 4. Ví dụ chạy từng bước

Giả sử:

```text
n = 5
```

Ban đầu:

```text
dp[0] = 1
dp[1] = 1
```

### i = 2

```text
dp[2] = dp[1] + dp[0]
      = 1 + 1
      = 2
```

### i = 3

```text
dp[3] = dp[2] + dp[1]
      = 2 + 1
      = 3
```

### i = 4

```text
dp[4] = dp[3] + dp[2]
      = 3 + 2
      = 5
```

### i = 5

```text
dp[5] = dp[4] + dp[3]
      = 5 + 3
      = 8
```

Kết quả:

```text
8
```

---

# 5. Cách tiếp cận 1 — Đệ quy thuần túy

Một cách tự nhiên là viết:

```cpp
int climbStairs(int n) {
    if (n <= 1) return 1;

    return climbStairs(n - 1) + climbStairs(n - 2);
}
```

Cách này thể hiện rất rõ công thức:

```text
f(n) = f(n - 1) + f(n - 2)
```

Nhưng **không tối ưu**.

## Vì sao?

Các bài toán con bị tính lại rất nhiều lần.

Ví dụ:

```text
f(5)
├── f(4)
│   ├── f(3)
│   └── f(2)
└── f(3)
    ├── f(2)
    └── f(1)
```

Ta thấy:

```text
f(3)
f(2)
```

bị tính nhiều lần.

Khi `n` lớn, số lần gọi hàm tăng rất nhanh.

Độ phức tạp xấp xỉ:

```text
O(2^n)
```

Không nên sử dụng cách này cho bài LeetCode này.

---

# 6. Cách tiếp cận 2 — Dynamic Programming với mảng

Ta lưu kết quả của những bài toán con đã tính:

```cpp
vector<int> dp(n + 1, 0);

dp[0] = 1;
dp[1] = 1;

for (int i = 2; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
}

return dp[n];
```

## Độ phức tạp

Ta duyệt từ `2` đến `n` đúng một lần:

```text
Time Complexity: O(n)
```

Ta sử dụng mảng `dp` có `n + 1` phần tử:

```text
Space Complexity: O(n)
```

Đây là một lời giải DP tốt.

Tuy nhiên, vẫn có thể tối ưu bộ nhớ.

---

# 7. Quan sát quan trọng để tối ưu bộ nhớ

Công thức:

```cpp
dp[i] = dp[i - 1] + dp[i - 2];
```

Để tính `dp[i]`, chúng ta **chỉ cần hai giá trị trước đó**:

```text
dp[i - 2]
dp[i - 1]
```

Chúng ta không cần giữ toàn bộ:

```text
dp[0], dp[1], dp[2], ..., dp[i - 3]
```

Vì vậy có thể thay toàn bộ mảng bằng hai biến.

Ví dụ:

```cpp
int truoc_2 = 1;
int truoc_1 = 1;

for (int i = 2; i <= n; i++) {
    int hien_tai = truoc_1 + truoc_2;

    truoc_2 = truoc_1;
    truoc_1 = hien_tai;
}

return truoc_1;
```

Khi đó:

```text
Time Complexity: O(n)
Space Complexity: O(1)
```

Đây là cách tối ưu hơn về bộ nhớ.

---

# 8. Kiểm tra lời giải C++ của bạn

Bạn đang sử dụng:

```cpp
class Solution {
public:
    int climbStairs(int n) {
        vector<int> dp(n + 1, 0);
        dp[0] = 1;
        dp[1] = 1;

        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
};
```

## 8.1. Đúng về logic

Lời giải của bạn hoàn toàn đúng.

Bạn đã xây dựng chính xác:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

và sử dụng đúng base case:

```cpp
dp[0] = 1;
dp[1] = 1;
```

---

## 8.2. Độ phức tạp

### Thời gian

Vòng lặp:

```cpp
for (int i = 2; i <= n; i++)
```

chạy khoảng `n` lần.

Do đó:

```text
Time Complexity: O(n)
```

### Bộ nhớ

Bạn tạo:

```cpp
vector<int> dp(n + 1, 0);
```

nên cần:

```text
Space Complexity: O(n)
```

---

# 9. Lời giải của bạn đã tối ưu chưa?

### Về thời gian

```text
O(n)
```

Đây là độ phức tạp rất tốt cho cách DP tuyến tính.

Bạn **không cần thay đổi thuật toán chỉ để giảm thời gian** nếu mục tiêu là một lời giải DP đơn giản, dễ hiểu.

### Về bộ nhớ

Bạn vẫn có thể tối ưu:

```text
O(n) → O(1)
```

vì chỉ cần hai trạng thái trước đó.

Do đó có thể kết luận:

> **Lời giải của bạn đã tối ưu về mặt thời gian theo cách tiếp cận DP tuyến tính, nhưng chưa tối ưu về bộ nhớ.**

---

# 10. Phiên bản tối ưu O(1) bộ nhớ

Giữ nguyên tư duy của bạn nhưng bỏ `vector`:

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int truoc_2 = 1;
        int truoc_1 = 1;

        for (int i = 2; i <= n; i++) {
            int hien_tai = truoc_1 + truoc_2;

            truoc_2 = truoc_1;
            truoc_1 = hien_tai;
        }

        return truoc_1;
    }
};
```

## Ý nghĩa của các biến

Tại mỗi vòng lặp:

```text
truoc_2 = dp[i - 2]
truoc_1 = dp[i - 1]
hien_tai = dp[i]
```

Sau khi tính:

```cpp
int hien_tai = truoc_1 + truoc_2;
```

ta dịch trạng thái:

```cpp
truoc_2 = truoc_1;
truoc_1 = hien_tai;
```

Ví dụ:

```text
Ban đầu:

truoc_2 = dp[0] = 1
truoc_1 = dp[1] = 1
```

Sau `i = 2`:

```text
hien_tai = 1 + 1 = 2

truoc_2 = 1
truoc_1 = 2
```

Tức là:

```text
truoc_2 = dp[1]
truoc_1 = dp[2]
```

Sau `i = 3`:

```text
hien_tai = 2 + 1 = 3
```

và tiếp tục như vậy.

---

# 11. So sánh hai phiên bản

| Phiên bản | Time | Space | Ưu điểm |
|---|---:|---:|---|
| Đệ quy thuần | O(2^n) | O(n) stack | Dễ thấy công thức |
| DP + vector | O(n) | O(n) | Dễ hiểu, dễ debug |
| DP tối ưu bộ nhớ | O(n) | O(1) | Tối ưu bộ nhớ |

Với mục tiêu học DP, phiên bản của bạn là một bước tiếp cận **rất tốt** vì nó thể hiện trực tiếp bảng trạng thái.

Sau khi hiểu rõ:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

mới tối ưu xuống `O(1)` bộ nhớ.

---

# 12. Một cách nhìn khác: Fibonacci

Dãy số của bài toán là:

```text
1, 1, 2, 3, 5, 8, 13, ...
```

Đây chính là dãy Fibonacci nếu đánh chỉ số phù hợp.

Ta có:

```text
climbStairs(n) = Fibonacci(n + 1)
```

Ví dụ:

```text
n = 1 → 1
n = 2 → 2
n = 3 → 3
n = 4 → 5
n = 5 → 8
```

Tuy nhiên, khi giải bài này trong bối cảnh học Dynamic Programming, không cần phải biến nó thành bài Fibonacci.

Điều quan trọng hơn là hiểu được:

```text
Trạng thái
    ↓
Base case
    ↓
Công thức chuyển trạng thái
    ↓
Tối ưu bộ nhớ
```

Đây là một pattern DP rất quan trọng và xuất hiện trong nhiều bài khác.

---

# 13. Những điều cần nhớ từ bài này

## Pattern 1 — Xác định trạng thái

Hỏi:

> `dp[i]` đại diện cho cái gì?

Ở đây:

```text
dp[i] = số cách để đến bậc i
```

---

## Pattern 2 — Xét bước cuối cùng

Hỏi:

> Để đến trạng thái `i`, trạng thái ngay trước đó có thể là gì?

Ở đây:

```text
i - 1 → i
i - 2 → i
```

Do đó:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

---

## Pattern 3 — Tìm base case

Ở đây:

```cpp
dp[0] = 1;
dp[1] = 1;
```

Base case phải phù hợp với công thức chuyển trạng thái.

---

## Pattern 4 — Kiểm tra khả năng tối ưu bộ nhớ

Nếu:

```text
dp[i]
```

chỉ phụ thuộc vào một vài trạng thái gần nhất, thường có thể giảm:

```text
O(n) → O(1)
```

---

# 14. Kết luận

Lời giải của bạn:

```cpp
vector<int> dp(n + 1, 0);
dp[0] = 1;
dp[1] = 1;

for (int i = 2; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
}
```

là **đúng và có độ phức tạp `O(n)` thời gian**.

Điểm duy nhất có thể cải thiện là bộ nhớ:

```text
Hiện tại:
Time  = O(n)
Space = O(n)

Tối ưu:
Time  = O(n)
Space = O(1)
```

Điều quan trọng nhất không phải chỉ là nhớ công thức Fibonacci, mà là nhận ra pattern:

```text
dp[i] phụ thuộc vào dp[i - 1] và dp[i - 2]
```

→ dùng DP  
→ sau đó kiểm tra xem có cần lưu toàn bộ mảng hay chỉ cần một vài trạng thái trước đó.

---

# 15. Code hoàn chỉnh đề xuất

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int truoc_2 = 1;
        int truoc_1 = 1;

        for (int i = 2; i <= n; i++) {
            int hien_tai = truoc_1 + truoc_2;

            truoc_2 = truoc_1;
            truoc_1 = hien_tai;
        }

        return truoc_1;
    }
};
```

**Độ phức tạp:**

```text
Time Complexity:  O(n)
Space Complexity: O(1)
```
