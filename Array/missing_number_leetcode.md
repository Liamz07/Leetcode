# Missing Number — Phương hướng tiếp cận tối ưu

## 1. Tổng quan bài toán

**LeetCode 268 — Missing Number** cho một mảng `nums` gồm `n` số nguyên khác nhau. Các giá trị thuộc tập:

```text
{0, 1, 2, ..., n}
```

Có tổng cộng `n + 1` giá trị nhưng mảng chỉ có `n` phần tử, nên **đúng một số bị thiếu**.

### Ví dụ

```text
Input:  [3, 0, 1]
Output: 2
```

Tập đầy đủ là:

```text
{0, 1, 2, 3}
```

Mảng có:

```text
{0, 1, 3}
```

nên `2` bị thiếu.

---

# 2. Quan sát quan trọng

Nếu:

```text
n = nums.size()
```

thì tập đầy đủ luôn là:

```text
0, 1, 2, ..., n
```

Trong khi `nums` chứa đúng `n` phần tử.

Do đó ta biết chắc:

- Có đúng một số bị thiếu.
- Không có số nào bị lặp.
- Số thiếu có thể là `0`.
- Số thiếu cũng có thể là `n`.

Ví dụ:

```text
nums = [0, 1, 2]
```

thì `n = 3`, tập đầy đủ là:

```text
0, 1, 2, 3
```

và số thiếu là `3`.

---

# 3. Các hướng tiếp cận

Có thể giải bài bằng:

1. Sorting.
2. Hash Set.
3. Tổng từ `0` đến `n`.
4. XOR.

Hai cách đạt:

```text
O(n) time
O(1) extra space
```

là **Sum** và **XOR**.

---

# 4. Cách 1 — Sorting

Ta sort mảng rồi tìm vị trí đầu tiên mà:

```text
nums[i] != i
```

Ví dụ:

```text
[3, 0, 1]
```

sort thành:

```text
[0, 1, 3]
```

Kiểm tra:

```text
i = 0 -> nums[0] = 0  đúng
i = 1 -> nums[1] = 1  đúng
i = 2 -> nums[2] = 3  sai
```

Vậy số thiếu là `2`.

Nếu mọi vị trí đều đúng thì số thiếu là `n`.

### Độ phức tạp

```text
Time:  O(n log n)
Space: O(1) hoặc phụ thuộc implementation
```

Cách này đúng nhưng không tối ưu về thời gian.

---

# 5. Cách 2 — Hash Set

Đưa toàn bộ phần tử vào `unordered_set`, sau đó kiểm tra:

```text
0, 1, 2, ..., n
```

số nào không tồn tại.

Ví dụ:

```text
nums = [3, 0, 1]
```

Set:

```text
{0, 1, 3}
```

Kiểm tra:

```text
0 -> có
1 -> có
2 -> không
```

=> đáp án là `2`.

### Độ phức tạp

Trung bình:

```text
Time:  O(n)
Space: O(n)
```

Thời gian tốt nhưng sử dụng thêm bộ nhớ tuyến tính.

---

# 6. Cách 3 — Tổng từ 0 đến n

Đây là cách rất trực quan.

Tổng của:

```text
0 + 1 + 2 + ... + n
```

là:

```text
n * (n + 1) / 2
```

Gọi tổng này là `expectedSum`.

Ta cũng tính tổng các phần tử thực tế:

```text
actualSum = nums[0] + nums[1] + ... + nums[n-1]
```

Vì chỉ thiếu một số:

```text
missing = expectedSum - actualSum
```

## Ví dụ

```text
nums = [3, 0, 1]
n = 3
```

Tổng kỳ vọng:

```text
0 + 1 + 2 + 3 = 6
```

Tổng thực tế:

```text
3 + 0 + 1 = 4
```

Do đó:

```text
missing = 6 - 4 = 2
```

### Độ phức tạp

```text
Time:  O(n)
Space: O(1)
```

### Lưu ý về overflow

Nếu `n` đủ lớn, biểu thức:

```cpp
n * (n + 1)
```

có thể overflow với `int`.

Vì vậy nên dùng:

```cpp
long long
```

cho phép tính tổng.

Ví dụ:

```cpp
long long n = nums.size();
long long expectedSum = n * (n + 1) / 2;
```

---

# 7. Cách 4 — XOR

Đây là cách rất đẹp cho bài toán này.

Ta sử dụng các tính chất:

```text
x ^ x = 0
x ^ 0 = x
```

và XOR có tính giao hoán, kết hợp:

```text
a ^ b = b ^ a

(a ^ b) ^ c = a ^ (b ^ c)
```

Điều này cho phép các số xuất hiện ở cả hai phía tự triệt tiêu.

---

# 8. Ý tưởng XOR

Tập đầy đủ là:

```text
0, 1, 2, ..., n
```

Mảng là:

```text
nums[0], nums[1], ..., nums[n-1]
```

Ta thực hiện:

```text
XOR(0..n) ^ XOR(tất cả phần tử trong nums)
```

Mọi số xuất hiện ở cả hai bên đều bị triệt tiêu:

```text
x ^ x = 0
```

Chỉ có số bị thiếu không có cặp tương ứng.

Vì vậy kết quả cuối cùng chính là số thiếu.

---

# 9. Ví dụ XOR

Với:

```text
nums = [3, 0, 1]
```

ta có:

```text
(0 ^ 1 ^ 2 ^ 3)
^
(3 ^ 0 ^ 1)
```

Nhóm lại:

```text
(0 ^ 0)
^
(1 ^ 1)
^
(3 ^ 3)
^
2
```

Các cặp giống nhau triệt tiêu:

```text
0 ^ 0 = 0
1 ^ 1 = 0
3 ^ 3 = 0
```

còn:

```text
2
```

Vậy đáp án là:

```text
2
```

---

# 10. Vì sao XOR không cần quan tâm thứ tự?

XOR có tính giao hoán:

```text
a ^ b = b ^ a
```

và tính kết hợp:

```text
(a ^ b) ^ c = a ^ (b ^ c)
```

Do đó:

```text
0 ^ 1 ^ 2 ^ 3 ^ 3 ^ 0 ^ 1
```

có thể nhóm thành:

```text
(0 ^ 0)
^ (1 ^ 1)
^ (3 ^ 3)
^ 2
```

Mặc dù các phần tử không nằm cạnh nhau trong dữ liệu ban đầu, ta vẫn có thể nhóm chúng để chứng minh tính đúng đắn.

---

# 11. Cách viết XOR tối ưu trong một vòng lặp

Ta có thể viết:

```cpp
int missing = nums.size();

for (int i = 0; i < nums.size(); ++i) {
    missing ^= i;
    missing ^= nums[i];
}

return missing;
```

Tại sao đúng?

Ban đầu:

```text
missing = n
```

Sau vòng lặp:

```text
missing =
    n
    ^ 0 ^ nums[0]
    ^ 1 ^ nums[1]
    ^ 2 ^ nums[2]
    ...
    ^ (n-1) ^ nums[n-1]
```

Sắp xếp lại:

```text
missing =
    (0 ^ 1 ^ 2 ^ ... ^ n)
    ^
    (nums[0] ^ nums[1] ^ ... ^ nums[n-1])
```

Đây chính là:

```text
XOR(tập đầy đủ) ^ XOR(mảng)
```

Mọi số xuất hiện ở cả hai phía bị triệt tiêu, chỉ còn số thiếu.

---

# 12. Dry run chi tiết

Xét:

```text
nums = [3, 0, 1]
```

Ban đầu:

```text
n = 3
missing = 3
```

## i = 0

```cpp
missing ^= 0;
missing ^= nums[0]; // 3
```

Tương đương:

```text
3 ^ 0 ^ 3
```

Vì:

```text
3 ^ 3 = 0
```

nên:

```text
missing = 0
```

## i = 1

`nums[1] = 0`:

```text
0 ^ 1 ^ 0
```

nên:

```text
missing = 1
```

## i = 2

`nums[2] = 1`:

```text
1 ^ 2 ^ 1
```

Ta có:

```text
1 ^ 1 = 0
```

nên:

```text
missing = 2
```

Kết quả:

```text
2
```

---

# 13. Edge cases

## Thiếu 0

```text
nums = [1, 2, 3]
```

Tập đầy đủ:

```text
0, 1, 2, 3
```

Kết quả:

```text
0
```

XOR xử lý bình thường vì:

```text
0 ^ x = x
```

---

## Thiếu n

```text
nums = [0, 1, 2]
```

`n = 3`.

Tập đầy đủ:

```text
0, 1, 2, 3
```

Kết quả:

```text
3
```

---

## Mảng chỉ có một phần tử

```text
nums = [0]
```

Tập đầy đủ:

```text
0, 1
```

Kết quả:

```text
1
```

---

## Mảng rỗng

```text
nums = []
```

Khi đó:

```text
n = 0
```

Tập đầy đủ là:

```text
{0}
```

Code XOR khởi tạo:

```cpp
int missing = nums.size(); // 0
```

và vòng lặp không chạy.

Kết quả:

```text
0
```

---

# 14. Chứng minh tính đúng đắn

Gọi `m` là số bị thiếu.

Ta có tập đầy đủ:

```text
F = {0, 1, 2, ..., n}
```

và tập các phần tử trong mảng là:

```text
A = F \ {m}
```

Ta tính:

```text
XOR(F) ^ XOR(A)
```

Mọi `x != m` xuất hiện đúng một lần trong `F` và đúng một lần trong `A`.

Do:

```text
x ^ x = 0
```

nên chúng bị triệt tiêu.

Chỉ có `m` xuất hiện trong `F` mà không xuất hiện trong `A`.

Vì:

```text
0 ^ m = m
```

kết quả cuối cùng là:

```text
m
```

Do đó thuật toán luôn trả về đúng số bị thiếu.

---

# 15. So sánh các phương pháp

| Phương pháp | Time | Extra Space | Ghi chú |
|---|---:|---:|---|
| Sorting | O(n log n) | O(1) hoặc tùy implementation | Không tối ưu về thời gian |
| Hash Set | O(n) trung bình | O(n) | Dễ hiểu nhưng tốn bộ nhớ |
| Sum | O(n) | O(1) | Đơn giản, cần chú ý overflow |
| XOR | O(n) | O(1) | Không cần extra data structure, tránh overflow do tổng |

Nếu yêu cầu:

```text
O(n) time
O(1) extra space
```

thì **Sum** và **XOR** đều đạt yêu cầu.

---

# 16. Sum hay XOR?

Không nhất thiết phải dùng XOR.

## Sum

Ưu điểm:

- Ý tưởng rất trực quan.
- Dễ giải thích.
- Code đơn giản.

Nhược điểm:

- Cần chú ý overflow.

## XOR

Ưu điểm:

- `O(n)` time.
- `O(1)` space.
- Không cần tính tổng.
- Không gặp vấn đề overflow từ phép cộng dồn.
- Khai thác trực tiếp tính chất `x ^ x = 0`.

Nhược điểm:

- Cần hiểu XOR và các tính chất của nó.

Trong phỏng vấn, cả hai đều là những lời giải hợp lệ nếu được triển khai đúng.

---

# 17. Vì sao không cần Dynamic Programming?

Không có trạng thái phụ thuộc theo nhiều bước.

Ta biết chính xác:

```text
tập đầy đủ = 0..n
```

và chỉ thiếu đúng một phần tử.

Không cần:

- DP;
- backtracking;
- recursion;
- brute force.

Bài toán có cấu trúc đại số rất rõ nên một phép toán đơn giản như XOR hoặc phép trừ tổng là đủ.

---

# 18. Vì sao không nên sort nếu muốn tối ưu?

Sorting cho:

```text
O(n log n)
```

trong khi ta có thể đạt:

```text
O(n)
```

Bởi vì để tìm số thiếu, ta không thực sự cần biết thứ tự của các phần tử.

Ta chỉ cần biết:

```text
phần tử nào thuộc tập đầy đủ
```

và XOR cho phép xử lý điều đó mà không cần lưu lại cấu trúc dữ liệu.

---

# 19. Pattern quan trọng: Expected vs Actual

Đây là insight lớn nhất của bài.

Ta có:

```text
EXPECTED
0, 1, 2, ..., n

ACTUAL
nums
```

và chỉ có một phần tử khác nhau giữa hai tập.

Có thể tìm phần "khác nhau" bằng:

### Tổng

```text
sum(Expected) - sum(Actual)
```

hoặc:

### XOR

```text
xor(Expected) ^ xor(Actual)
```

Pattern này rất hữu ích cho nhiều bài toán về:

- một phần tử bị thiếu;
- một phần tử xuất hiện một lần;
- các phần tử xuất hiện theo cặp;
- tìm phần tử không có cặp.

---

# 20. Liên hệ với Single Number

Trong bài **Single Number**, nếu một số xuất hiện một lần còn các số khác xuất hiện hai lần, ta có:

```text
x ^ x = 0
```

nên XOR toàn bộ mảng sẽ để lại số xuất hiện một lần.

Missing Number có cùng tinh thần:

```text
Tập đầy đủ
    XOR
Mảng thực tế
```

Các phần tử có mặt ở cả hai bên bị loại bỏ.

Điểm khác là ở Missing Number ta biết trước tập đầy đủ:

```text
0..n
```

---

# 21. Các lỗi thường gặp

## Lỗi 1 — Chỉ XOR các phần tử trong mảng

Sai:

```cpp
int result = 0;

for (int num : nums) {
    result ^= num;
}
```

Ta cần XOR cả tập:

```text
0..n
```

nữa.

---

## Lỗi 2 — Quên số n

Nếu chỉ viết:

```cpp
for (int i = 0; i < nums.size(); ++i)
```

thì các chỉ số chỉ chạy:

```text
0..n-1
```

Trong khi tập đầy đủ là:

```text
0..n
```

Cách khởi tạo:

```cpp
int missing = nums.size();
```

giúp đưa `n` vào ngay từ đầu.

---

## Lỗi 3 — Dùng `int` thiếu cẩn thận với công thức tổng

Không nên vô tư viết:

```cpp
int expected = n * (n + 1) / 2;
```

nếu giới hạn `n` có thể làm phép nhân overflow.

An toàn hơn:

```cpp
long long n = nums.size();
long long expected = n * (n + 1) / 2;
```

---

## Lỗi 4 — Sắp xếp rồi nghĩ đó là O(n)

Sorting thông thường có độ phức tạp:

```text
O(n log n)
```

không phải `O(n)`.

---

# 22. Lời giải C++ tối ưu bằng XOR

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int missing = nums.size();

        for (int i = 0; i < nums.size(); ++i) {
            missing ^= i;
            missing ^= nums[i];
        }

        return missing;
    }
};
```

---

# 23. Giải thích code

```cpp
int missing = nums.size();
```

Đặt `missing = n`, vì `n` là phần tử cuối cùng trong tập:

```text
0..n
```

Sau đó duyệt:

```cpp
for (int i = 0; i < nums.size(); ++i)
```

Ở mỗi bước:

```cpp
missing ^= i;
```

đưa chỉ số `i` vào phép XOR.

Tiếp theo:

```cpp
missing ^= nums[i];
```

đưa giá trị thực tế trong mảng vào.

Sau toàn bộ vòng lặp, biểu thức tương đương:

```text
(0 ^ 1 ^ 2 ^ ... ^ n)
^
(nums[0] ^ nums[1] ^ ... ^ nums[n-1])
```

Các phần tử xuất hiện ở cả hai phía triệt tiêu.

Cuối cùng chỉ còn số bị thiếu.

---

# 24. Phiên bản C++ dễ đọc hơn

Nếu muốn code dễ giải thích trong interview:

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        int result = 0;

        // XOR tất cả các số từ 0 đến n.
        for (int i = 0; i <= n; ++i) {
            result ^= i;
        }

        // XOR tất cả phần tử thực tế.
        for (int num : nums) {
            result ^= num;
        }

        return result;
    }
};
```

Phiên bản này có hai vòng lặp nhưng vẫn là:

```text
O(n) + O(n) = O(n)
```

và:

```text
O(1) space
```

Ưu điểm là ý tưởng nhìn rất rõ:

```text
result = XOR(full set) XOR XOR(actual array)
```

---

# 25. Lời giải bằng tổng

Một cách tối ưu khác:

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int missingNumber(vector<int>& nums) {
        long long n = nums.size();

        long long expectedSum = n * (n + 1) / 2;
        long long actualSum = 0;

        for (int num : nums) {
            actualSum += num;
        }

        return static_cast<int>(expectedSum - actualSum);
    }
};
```

Độ phức tạp:

```text
Time:  O(n)
Space: O(1)
```

---

# 26. Checklist khi đi thi / phỏng vấn

Khi gặp bài Missing Number, hãy nhớ:

```text
1. Có n phần tử.
2. Giá trị hợp lệ là 0..n.
3. Đúng một số bị thiếu.
4. Có thể dùng:
       expected sum - actual sum
   hoặc:
       XOR(expected) ^ XOR(actual)
5. Nếu dùng Sum, chú ý overflow.
6. Nếu dùng XOR, nhớ đưa cả n vào.
7. Không cần sort.
8. Không cần Hash Set.
9. Mục tiêu tối ưu:
       O(n) time
       O(1) extra space
```

---

# 27. Kết luận

Missing Number là một bài kinh điển để học cách nhận diện **invariant** và khai thác cấu trúc toán học.

Có hai lời giải tối ưu nổi bật:

```text
Sum:
missing = sum(0..n) - sum(nums)
```

và:

```text
XOR:
missing = XOR(0..n) ^ XOR(nums)
```

Với XOR, mọi số xuất hiện ở cả tập đầy đủ và mảng đều bị triệt tiêu:

```text
x ^ x = 0
```

chỉ để lại số bị thiếu.

Lời giải XOR một vòng lặp:

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int missing = nums.size();

        for (int i = 0; i < nums.size(); ++i) {
            missing ^= i;
            missing ^= nums[i];
        }

        return missing;
    }
};
```

Đạt:

```text
Time Complexity:  O(n)
Space Complexity: O(1)
```

Điều quan trọng nhất không phải chỉ nhớ code, mà là nhận ra pattern:

```text
EXPECTED SET
      +
ACTUAL SET
      ↓
triệt tiêu các phần tử xuất hiện ở cả hai
      ↓
phần tử còn lại = đáp án
```

Đây là một pattern rất đáng ghi nhớ cho các bài toán về phần tử thiếu, phần tử xuất hiện đơn lẻ và các bài toán có thể giải bằng XOR.
