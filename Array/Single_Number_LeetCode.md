# Single Number — LeetCode

## 1. Đề bài

Cho một mảng số nguyên `nums`, trong đó:

- Mỗi phần tử xuất hiện **đúng 2 lần**.
- Chỉ có **một phần tử xuất hiện đúng 1 lần**.

Hãy tìm và trả về phần tử xuất hiện đúng 1 lần.

### Ví dụ

```text
Input:  nums = [2, 2, 1]
Output: 1
```

```text
Input:  nums = [4, 1, 2, 1, 2]
Output: 4
```

```text
Input:  nums = [1]
Output: 1
```

---

# 2. Phân tích yêu cầu

Điểm quan trọng nhất của bài toán nằm ở điều kiện:

> Mọi số đều xuất hiện đúng 2 lần, ngoại trừ một số xuất hiện đúng 1 lần.

Ví dụ:

```text
nums = [4, 1, 2, 1, 2]
```

Ta có:

```text
4 → xuất hiện 1 lần
1 → xuất hiện 2 lần
2 → xuất hiện 2 lần
```

Mục tiêu là tìm `4`.

---

# 3. Những hướng tiếp cận có thể nghĩ đến

Có nhiều cách giải bài toán này.

## Cách 1: Dùng đếm tần suất

Ta có thể sử dụng `map` hoặc `unordered_map` để đếm số lần xuất hiện của từng phần tử.

Ví dụ:

```text
nums = [4, 1, 2, 1, 2]

4 → 1 lần
1 → 2 lần
2 → 2 lần
```

Sau đó tìm phần tử có tần suất bằng `1`.

### Độ phức tạp

Với `unordered_map`:

- Thời gian trung bình: `O(n)`
- Bộ nhớ: `O(n)`

Cách này dễ nghĩ và dễ cài đặt, nhưng chưa phải lời giải tối ưu về **bộ nhớ phụ**.

---

# 4. Quan sát quan trọng: phép XOR

Để giải bài này tối ưu hơn, ta cần nhận ra một số tính chất đặc biệt của phép XOR (`^`).

## 4.1. XOR một số với chính nó

Ta có:

```text
a ^ a = 0
```

Ví dụ:

```text
5 ^ 5 = 0
```

Ở dạng bit:

```text
101
101
---
000
```

---

## 4.2. XOR một số với 0

Ta có:

```text
a ^ 0 = a
```

Ví dụ:

```text
7 ^ 0 = 7
```

Ở dạng bit:

```text
111
000
---
111
```

---

## 4.3. XOR có tính giao hoán

Ta có:

```text
a ^ b = b ^ a
```

Ví dụ:

```text
2 ^ 5 = 5 ^ 2
```

Điều này có nghĩa là **thứ tự thực hiện XOR không quan trọng**.

---

## 4.4. XOR có tính kết hợp

Ta có:

```text
(a ^ b) ^ c = a ^ (b ^ c)
```

Do đó:

```text
a ^ b ^ c
```

có thể nhóm các phần tử theo bất kỳ cách nào.

---

# 5. Ý tưởng cốt lõi

Giả sử:

```text
nums = [4, 1, 2, 1, 2]
```

Ta XOR tất cả các phần tử:

```text
4 ^ 1 ^ 2 ^ 1 ^ 2
```

Do XOR có tính giao hoán và kết hợp, ta có thể sắp xếp lại:

```text
4 ^ (1 ^ 1) ^ (2 ^ 2)
```

Vì:

```text
1 ^ 1 = 0
2 ^ 2 = 0
```

nên:

```text
4 ^ 0 ^ 0
```

Và:

```text
4 ^ 0 = 4
```

Kết quả cuối cùng là:

```text
4
```

Đây chính là số xuất hiện đúng một lần.

---

# 6. Tại sao XOR loại bỏ được các phần tử xuất hiện 2 lần?

Giả sử phần tử xuất hiện 2 lần là `x`.

Trong phép XOR toàn bộ mảng, ta sẽ có:

```text
... ^ x ^ ... ^ x ^ ...
```

Nhờ tính giao hoán và kết hợp, ta có thể đưa hai `x` cạnh nhau:

```text
... ^ (x ^ x) ^ ...
```

Mà:

```text
x ^ x = 0
```

Do đó cặp `x` bị triệt tiêu.

Điều này xảy ra với **tất cả các phần tử xuất hiện 2 lần**.

Cuối cùng chỉ còn lại phần tử xuất hiện 1 lần.

---

# 7. Ví dụ mô phỏng từng bước

Xét:

```text
nums = [4, 1, 2, 1, 2]
```

Ta khởi tạo:

```text
ans = 0
```

Sau đó XOR lần lượt:

### Bước 1

```text
ans = 0 ^ 4
    = 4
```

### Bước 2

```text
ans = 4 ^ 1
```

### Bước 3

```text
ans = (4 ^ 1) ^ 2
```

### Bước 4

```text
ans = (4 ^ 1 ^ 2) ^ 1
```

Do:

```text
1 ^ 1 = 0
```

nên các số `1` sẽ triệt tiêu nhau.

### Bước 5

```text
ans = 4 ^ 1 ^ 2 ^ 1 ^ 2
```

Nhóm lại:

```text
ans = 4 ^ (1 ^ 1) ^ (2 ^ 2)
```

Suy ra:

```text
ans = 4 ^ 0 ^ 0
    = 4
```

Vậy đáp án là:

```text
4
```

---

# 8. Có cần quan tâm đến thứ tự các phần tử không?

**Không.**

Ví dụ:

```text
[4, 1, 2, 1, 2]
```

và:

```text
[1, 2, 4, 2, 1]
```

đều cho cùng kết quả.

Bởi vì XOR có tính giao hoán:

```text
a ^ b = b ^ a
```

và tính kết hợp:

```text
(a ^ b) ^ c = a ^ (b ^ c)
```

Vì vậy ta không cần:

- sắp xếp mảng;
- tìm vị trí của các cặp;
- lưu tần suất;
- kiểm tra từng phần tử bằng vòng lặp lồng nhau.

---

# 9. Vì sao không cần `set` hoặc `map`?

Ta có thể nghĩ đến việc:

```cpp
map<int, int> dem;
```

sau đó:

```text
dem[x]++
```

và tìm phần tử có:

```text
dem[x] == 1
```

Nhưng cách này phải sử dụng thêm bộ nhớ để lưu các phần tử.

Trong khi XOR cho phép ta xử lý toàn bộ bài toán chỉ với **một biến kết quả**:

```cpp
int ans = 0;
```

Đây là điểm tối ưu quan trọng của bài toán.

---

# 10. Độ phức tạp

Giả sử mảng có `n` phần tử.

## Thời gian

Ta duyệt mảng đúng một lần:

```cpp
for (int x : nums)
```

Do đó:

```text
Time Complexity = O(n)
```

Đây là tối ưu về thời gian theo bậc lớn, vì ít nhất ta cũng cần xem các phần tử trong mảng để biết số nào xuất hiện đơn lẻ.

## Bộ nhớ phụ

Ta chỉ sử dụng:

```cpp
int ans = 0;
```

nên:

```text
Space Complexity = O(1)
```

Không cần tạo `map`, `set`, mảng phụ hay cấu trúc dữ liệu khác.

---

# 11. Vì sao đây là lời giải tối ưu?

Lời giải XOR đạt:

```text
Thời gian: O(n)
Bộ nhớ phụ: O(1)
```

Đây là mục tiêu rất tốt cho bài toán.

So sánh:

| Phương pháp | Thời gian | Bộ nhớ phụ |
|---|---:|---:|
| Hai vòng lặp | `O(n²)` | `O(1)` |
| Sort | `O(n log n)` | tùy cách sort |
| `map` / `unordered_map` | `O(n)` trung bình | `O(n)` |
| **XOR** | **`O(n)`** | **`O(1)`** |

Vì vậy, khi thấy điều kiện:

> **Mỗi phần tử xuất hiện đúng 2 lần, chỉ có một phần tử xuất hiện 1 lần**

thì phép **XOR** là một kỹ thuật rất đáng nghĩ đến.

---

# 12. Cách triển khai trong C++

Ta chỉ cần một biến:

```cpp
int ans = 0;
```

Sau đó duyệt từng phần tử:

```cpp
for (int x : nums) {
    ans ^= x;
}
```

Cuối cùng:

```cpp
return ans;
```

Có thể hiểu dòng:

```cpp
ans ^= x;
```

tương đương với:

```cpp
ans = ans ^ x;
```

---

# 13. Lời giải C++

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;

        for (int x : nums) {
            ans ^= x;
        }

        return ans;
    }
};
```

---

# 14. Giải thích code từng dòng

## Khai báo class

```cpp
class Solution {
```

LeetCode yêu cầu chúng ta viết lời giải bên trong class `Solution`.

---

## Hàm `singleNumber`

```cpp
int singleNumber(vector<int>& nums) {
```

Hàm nhận vào:

```cpp
vector<int>& nums
```

là mảng số nguyên và trả về:

```cpp
int
```

là số xuất hiện đúng một lần.

---

## Khởi tạo biến kết quả

```cpp
int ans = 0;
```

Ta bắt đầu với `0` vì:

```text
0 ^ x = x
```

nên việc bắt đầu XOR từ `0` không làm thay đổi kết quả.

---

## Duyệt toàn bộ mảng

```cpp
for (int x : nums) {
```

Mỗi vòng lặp lấy một phần tử `x` trong `nums`.

---

## XOR vào kết quả

```cpp
ans ^= x;
```

Tương đương:

```cpp
ans = ans ^ x;
```

Mỗi số xuất hiện 2 lần sẽ tự triệt tiêu:

```text
x ^ x = 0
```

Còn số xuất hiện 1 lần sẽ được giữ lại.

---

## Trả về kết quả

```cpp
return ans;
```

Sau khi XOR toàn bộ mảng, `ans` chính là số xuất hiện đúng một lần.

---

# 15. Kiểm tra với các test case

## Test 1

```text
nums = [2, 2, 1]
```

Ta có:

```text
0 ^ 2 ^ 2 ^ 1
= (2 ^ 2) ^ 1
= 0 ^ 1
= 1
```

Kết quả:

```text
1
```

---

## Test 2

```text
nums = [4, 1, 2, 1, 2]
```

Ta có:

```text
0 ^ 4 ^ 1 ^ 2 ^ 1 ^ 2
= 4 ^ (1 ^ 1) ^ (2 ^ 2)
= 4
```

Kết quả:

```text
4
```

---

## Test 3

```text
nums = [1]
```

Ta có:

```text
0 ^ 1 = 1
```

Kết quả:

```text
1
```

---

# 16. Góc nhìn bằng bit

XOR hoạt động trên từng bit.

Ví dụ:

```text
5 = 101
```

XOR chính nó:

```text
101
101
---
000
```

Do đó:

```text
5 ^ 5 = 0
```

Tương tự:

```text
7 ^ 7 = 0
```

và:

```text
12 ^ 12 = 0
```

Điều này không phụ thuộc vào việc số đó là số dương hay âm; phép XOR vẫn được thực hiện trên biểu diễn bit của số nguyên.

---

# 17. Insight quan trọng cần nhớ

Bài này không chỉ kiểm tra khả năng duyệt mảng.

Điểm quan trọng là nhận ra **tính chất toán học của XOR**:

```text
x ^ x = 0
x ^ 0 = x
```

kết hợp với:

```text
a ^ b = b ^ a
(a ^ b) ^ c = a ^ (b ^ c)
```

Từ đó:

```text
a ^ b ^ b
= a ^ (b ^ b)
= a ^ 0
= a
```

Vì mọi số xuất hiện 2 lần đều bị triệt tiêu, số xuất hiện 1 lần là phần tử còn lại.

---

# 18. Pattern cần ghi nhớ khi làm LeetCode

Khi gặp bài có dạng:

- Các phần tử xuất hiện theo **cặp**.
- Chỉ có một phần tử không có cặp.
- Cần tìm phần tử đó.
- Không cần biết vị trí ban đầu.

Hãy nghĩ ngay đến:

```text
XOR
```

Mẫu tư duy:

```cpp
int ans = 0;

for (int x : nums) {
    ans ^= x;
}

return ans;
```

Đây là một trong những pattern bit manipulation cơ bản và rất quan trọng trong các bài thuật toán.

---

# 19. Kết luận

Lời giải tối ưu của **Single Number** là XOR toàn bộ các phần tử trong mảng.

Ta có:

```text
x ^ x = 0
x ^ 0 = x
```

và XOR có tính giao hoán, kết hợp nên mọi cặp phần tử giống nhau sẽ bị triệt tiêu, chỉ còn lại phần tử xuất hiện một lần.

### Độ phức tạp cuối cùng

```text
Time Complexity:  O(n)
Space Complexity: O(1)
```

### Code hoàn chỉnh

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;

        for (int x : nums) {
            ans ^= x;
        }

        return ans;
    }
};
```
