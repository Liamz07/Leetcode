# Find Pivot Index — LeetCode

## 1. Thông tin bài toán

**LeetCode:** Find Pivot Index  
**Độ khó:** Easy

### Mô tả

Cho một mảng số nguyên `nums`, hãy tìm **pivot index** — chỉ số `i` sao cho:

- Tổng các phần tử bên trái `i` bằng tổng các phần tử bên phải `i`.
- Phần tử tại chính `i` **không được tính** vào cả hai phía.
- Nếu có nhiều pivot index, trả về pivot index xuất hiện đầu tiên.
- Nếu không tồn tại, trả về `-1`.

Ví dụ:

```text
nums = [1, 7, 3, 6, 5, 6]

i = 3

Bên trái: 1 + 7 + 3 = 11
Bên phải: 5 + 6 = 11

=> pivot index = 3
```

---

# 2. Phân tích bản chất bài toán

Với một vị trí `i`, ta cần kiểm tra:

```text
sum(nums[0 ... i-1]) == sum(nums[i+1 ... n-1])
```

Cách nghĩ trực tiếp là:

1. Với mỗi `i`, tính tổng bên trái.
2. Tính tổng bên phải.
3. So sánh hai tổng.

Nếu mỗi lần tính tổng lại duyệt một đoạn của mảng, trong trường hợp xấu nhất ta sẽ phải duyệt rất nhiều phần tử lặp lại.

Ví dụ:

```text
i = 0 → tính tổng bên phải
i = 1 → lại tính tổng bên phải
i = 2 → lại tính tổng bên phải
...
```

Điều này có thể dẫn đến:

```text
O(n²)
```

Vì vậy, cần tận dụng thông tin tổng đã biết để tránh tính lại.

---

# 3. Phương pháp Prefix Sum

## 3.1. Ý tưởng

Ta có thể tạo một mảng `pre` trong đó:

```text
pre[i] = tổng nums[0] đến nums[i - 1]
```

Hay:

```text
pre[i] = nums[0] + nums[1] + ... + nums[i - 1]
```

Do đó:

```text
pre[0] = 0
pre[1] = nums[0]
pre[2] = nums[0] + nums[1]
...
```

Ví dụ:

```text
nums = [1, 7, 3, 6, 5, 6]
```

Ta có:

```text
pre = [0, 1, 8, 11, 17, 22, 28]
```

---

## 3.2. Tính tổng bên trái

Với vị trí `i`:

```text
leftSum = pre[i]
```

Ví dụ tại `i = 3`:

```text
pre[3] = 1 + 7 + 3 = 11
```

Đây chính là tổng bên trái.

---

## 3.3. Tính tổng bên phải

Tổng toàn bộ mảng là:

```text
pre[n]
```

Tổng từ đầu mảng đến và bao gồm `nums[i]` là:

```text
pre[i + 1]
```

Vì vậy:

```text
rightSum = pre[n] - pre[i + 1]
```

Ví dụ tại `i = 3`:

```text
pre[6] = 28
pre[4] = 17

rightSum = 28 - 17 = 11
```

Do đó:

```text
leftSum == rightSum
11 == 11
```

`i = 3` là pivot index.

---

# 4. Đánh giá lời giải của bạn

Lời giải bạn đưa ra:

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        vector<int> pre(nums.size() + 1, 0);

        for (int i = 1; i <= nums.size(); i++) {
            pre[i] = pre[i - 1] + nums[i - 1];
        }

        for (int i = 0; i < nums.size(); i++) {
            if (pre[i] == pre[nums.size()] - pre[i + 1]) {
                return i;
            }
        }

        return -1;
    }
};
```

## 4.1. Kiểm tra tính đúng đắn

Lời giải của bạn **đúng**.

Bạn xây dựng prefix sum:

```cpp
pre[i] = pre[i - 1] + nums[i - 1];
```

Sau đó kiểm tra:

```cpp
pre[i] == pre[nums.size()] - pre[i + 1]
```

Trong đó:

```text
pre[i]
```

là tổng bên trái.

Còn:

```text
pre[n] - pre[i + 1]
```

là tổng bên phải.

Vì vậy điều kiện kiểm tra hoàn toàn chính xác.

---

# 5. Độ phức tạp của lời giải hiện tại

Bạn có hai vòng lặp:

### Vòng lặp 1

```cpp
for (int i = 1; i <= nums.size(); i++)
```

Chạy `n` lần:

```text
O(n)
```

### Vòng lặp 2

```cpp
for (int i = 0; i < nums.size(); i++)
```

Cũng chạy tối đa `n` lần:

```text
O(n)
```

Tổng:

```text
O(n) + O(n) = O(n)
```

### Bộ nhớ

Bạn tạo:

```cpp
vector<int> pre(nums.size() + 1, 0);
```

Kích thước `n + 1`.

Do đó:

```text
Space = O(n)
```

Kết luận:

```text
Time  = O(n)
Space = O(n)
```

---

# 6. Lời giải của bạn đã tối ưu chưa?

Cần phân biệt **tối ưu thời gian** và **tối ưu bộ nhớ**.

### Thời gian

Bạn đạt:

```text
O(n)
```

Đây là độ phức tạp thời gian tối ưu theo Big-O cho bài toán này, vì ít nhất ta cần xét các phần tử để biết tổng của chúng.

### Bộ nhớ

Bạn đang dùng:

```text
O(n)
```

cho mảng prefix sum.

Tuy nhiên, bài này **không cần lưu toàn bộ prefix sum**.

Ta chỉ cần:

1. Tổng toàn bộ mảng.
2. Tổng bên trái hiện tại.

Vì vậy có thể giảm bộ nhớ phụ từ:

```text
O(n)
```

xuống:

```text
O(1)
```

Đây là điểm mà lời giải của bạn có thể cải thiện.

---

# 7. Phương pháp tối ưu nhất: Total Sum + Left Sum

## 7.1. Ý tưởng

Thay vì tạo `pre`, trước tiên tính:

```text
totalSum = tổng toàn bộ mảng
```

Sau đó duyệt mảng từ trái sang phải.

Tại vị trí `i`, giả sử:

```text
leftSum
```

là tổng các phần tử trước `i`.

Ta có:

```text
totalSum = leftSum + nums[i] + rightSum
```

Suy ra:

```text
rightSum = totalSum - leftSum - nums[i]
```

Điều kiện pivot là:

```text
leftSum == rightSum
```

nên:

```text
leftSum == totalSum - leftSum - nums[i]
```

Nếu đúng, trả về `i`.

Sau khi kiểm tra vị trí `i`, ta cập nhật:

```text
leftSum += nums[i]
```

để chuẩn bị cho vị trí tiếp theo.

---

# 8. Ví dụ chi tiết

Cho:

```text
nums = [1, 7, 3, 6, 5, 6]
```

Tổng toàn bộ:

```text
totalSum = 28
```

Ban đầu:

```text
leftSum = 0
```

### i = 0

```text
leftSum  = 0
rightSum = 28 - 0 - 1
         = 27
```

Không bằng nhau.

Cập nhật:

```text
leftSum = 0 + 1 = 1
```

---

### i = 1

```text
leftSum  = 1
rightSum = 28 - 1 - 7
         = 20
```

Không bằng nhau.

Cập nhật:

```text
leftSum = 1 + 7 = 8
```

---

### i = 2

```text
leftSum  = 8
rightSum = 28 - 8 - 3
         = 17
```

Không bằng nhau.

Cập nhật:

```text
leftSum = 8 + 3 = 11
```

---

### i = 3

```text
leftSum  = 11
rightSum = 28 - 11 - 6
         = 11
```

Hai bên bằng nhau:

```text
11 == 11
```

Vậy:

```text
answer = 3
```

---

# 9. Tại sao không cần Prefix Sum?

Điểm quan trọng là tại mỗi `i`, ta chỉ cần biết:

```text
leftSum
```

và:

```text
totalSum
```

Không cần biết tổng bên trái của **tất cả** các vị trí cùng lúc.

Ta có thể cập nhật `leftSum` ngay trong quá trình duyệt:

```text
leftSum += nums[i]
```

Do đó không cần:

```cpp
vector<int> pre;
```

Đây là một ví dụ điển hình của kỹ thuật:

> **Không lưu toàn bộ trạng thái nếu chỉ cần trạng thái hiện tại.**

---

# 10. Lời giải tối ưu về bộ nhớ

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        int totalSum = 0;

        // Tính tổng toàn bộ mảng
        for (int num : nums) {
            totalSum += num;
        }

        int leftSum = 0;

        // Kiểm tra từng vị trí
        for (int i = 0; i < nums.size(); i++) {
            int rightSum = totalSum - leftSum - nums[i];

            if (leftSum == rightSum) {
                return i;
            }

            leftSum += nums[i];
        }

        return -1;
    }
};
```

Độ phức tạp:

```text
Time  = O(n)
Space = O(1)
```

Đây là phiên bản tối ưu hơn về bộ nhớ so với lời giải sử dụng prefix sum của bạn.

---

# 11. Một cách viết khác: Không cần biến `rightSum`

Ta cũng có thể biến đổi trực tiếp điều kiện:

```text
leftSum = totalSum - leftSum - nums[i]
```

thành:

```text
2 * leftSum + nums[i] == totalSum
```

Code:

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        int totalSum = 0;

        for (int num : nums) {
            totalSum += num;
        }

        int leftSum = 0;

        for (int i = 0; i < nums.size(); i++) {
            if (leftSum == totalSum - leftSum - nums[i]) {
                return i;
            }

            leftSum += nums[i];
        }

        return -1;
    }
};
```

Tuy nhiên, cách viết có biến:

```cpp
rightSum
```

thường dễ đọc và dễ hiểu hơn khi mới học.

---

# 12. So sánh hai cách tiếp cận

| Tiêu chí | Prefix Sum của bạn | Total Sum + Left Sum |
|---|---:|---:|
| Time | `O(n)` | `O(n)` |
| Space | `O(n)` | `O(1)` |
| Đúng | Có | Có |
| Dễ hiểu | Rất dễ hiểu | Dễ hiểu |
| Có cần mảng phụ | Có | Không |
| Tối ưu bộ nhớ | Chưa | Có |

Như vậy:

```text
Lời giải của bạn:
O(n) time + O(n) space

Lời giải tối ưu:
O(n) time + O(1) space
```

Điểm cần cải thiện duy nhất về mặt độ phức tạp là **loại bỏ mảng `pre`**.

---

# 13. Một số edge case quan trọng

## Trường hợp 1: Pivot ở đầu

```text
nums = [2, 1, -1]
```

Tại `i = 0`:

```text
leftSum = 0
rightSum = 1 + (-1) = 0
```

Vậy:

```text
answer = 0
```

Điều này cho thấy phần bên trái có thể là tổng rỗng và được xem là `0`.

---

## Trường hợp 2: Pivot ở cuối

```text
nums = [-1, -1, 0, 1, 1, 0]
```

Ở một vị trí cuối phù hợp, tổng bên phải có thể là tổng rỗng:

```text
rightSum = 0
```

Vì vậy khi kiểm tra pivot, không được quên trường hợp một phía không có phần tử.

---

## Trường hợp 3: Không có pivot

```text
nums = [1, 2, 3]
```

Không có vị trí nào thỏa mãn:

```text
leftSum == rightSum
```

Kết quả:

```text
-1
```

---

## Trường hợp 4: Có nhiều pivot

Nếu có nhiều vị trí thỏa mãn, đề bài yêu cầu trả về vị trí đầu tiên.

Do đó việc duyệt:

```cpp
for (int i = 0; i < nums.size(); i++)
```

và:

```cpp
return i;
```

ngay khi tìm thấy pivot là chính xác.

---

# 14. Có cần `long long` không?

Về mặt kỹ thuật, khi tổng các phần tử có thể lớn, sử dụng:

```cpp
long long
```

sẽ an toàn hơn:

```cpp
long long totalSum = 0;
long long leftSum = 0;
```

Tuy nhiên, với giới hạn dữ liệu của bài LeetCode **Find Pivot Index**, `int` vẫn đủ.

Phiên bản dùng `long long`:

```cpp
class Solution {
public:
    int pivotIndex(vector<int>& nums) {
        long long totalSum = 0;

        for (int num : nums) {
            totalSum += num;
        }

        long long leftSum = 0;

        for (int i = 0; i < nums.size(); i++) {
            long long rightSum = totalSum - leftSum - nums[i];

            if (leftSum == rightSum) {
                return i;
            }

            leftSum += nums[i];
        }

        return -1;
    }
};
```

---

# 15. Kết luận

Lời giải của bạn **hoàn toàn đúng** và đã đạt độ phức tạp thời gian:

```text
O(n)
```

Đây là mức tối ưu về thời gian.

Điểm duy nhất có thể cải thiện là bộ nhớ:

```text
Hiện tại: O(n)
Tối ưu:   O(1)
```

Lý do là mảng prefix sum không thực sự cần thiết. Chỉ cần lưu:

```text
totalSum
leftSum
```

là đủ để tính tổng bên phải:

```text
rightSum = totalSum - leftSum - nums[i]
```

### Cách tư duy quan trọng cần ghi nhớ

Bài toán này là một ví dụ rất tốt cho kỹ thuật:

```text
Tổng toàn bộ
      ↓
Tổng bên trái hiện tại
      ↓
Tính tổng bên phải bằng phép trừ
      ↓
So sánh
      ↓
Cập nhật leftSum
```

Và pattern tổng quát:

```text
right = total - left - current
```

có thể xuất hiện trong nhiều bài toán liên quan đến **prefix/suffix sum**, cân bằng hai phía của mảng và tìm vị trí thỏa mãn một điều kiện tổng.
