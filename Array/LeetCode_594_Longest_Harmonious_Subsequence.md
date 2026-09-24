# LeetCode 594 - Longest Harmonious Subsequence

## Đề bài

Cho một mảng số nguyên `nums`.

Một dãy con (subsequence) được gọi là **harmonious** nếu:

```text
Giá trị lớn nhất - Giá trị nhỏ nhất = 1
```

Hãy tìm độ dài lớn nhất của một harmonious subsequence trong mảng.

Lưu ý:

- Subsequence không yêu cầu các phần tử phải liên tiếp.
- Có thể xóa tùy ý một số phần tử và giữ nguyên thứ tự các phần tử còn lại.

---

## Ví dụ 1

```cpp
Input: nums = [1,3,2,2,5,2,3,7]

Output: 5
```

Giải thích:

```text
Subsequence dài nhất là:

[3,2,2,2,3]

max = 3
min = 2

3 - 2 = 1
```

Độ dài:

```text
5
```

---

## Ví dụ 2

```cpp
Input: nums = [1,2,3,4]

Output: 2
```

Một trong các subsequence hợp lệ:

```text
[1,2]
```

---

## Ví dụ 3

```cpp
Input: nums = [1,1,1,1]

Output: 0
```

Vì:

```text
max - min = 0
```

không phải 1.

---

# Phân tích bài toán

Điều kiện của harmonious subsequence:

```text
max - min = 1
```

Điều này dẫn đến một nhận xét cực kỳ quan trọng.

---

## Quan sát then chốt

Giả sử:

```text
min = x
max = x + 1
```

Khi đó toàn bộ subsequence chỉ có thể chứa:

```text
x
và
x + 1
```

Không thể xuất hiện giá trị khác.

Ví dụ:

```text
2 2 2 3 3
```

hợp lệ.

Nhưng:

```text
2 2 3 4
```

không hợp lệ vì:

```text
4 - 2 = 2
```

---

## Chuyển đổi bài toán

Thay vì tìm subsequence trực tiếp.

Ta chỉ cần:

```text
Đếm số lần xuất hiện của từng số.
```

Giả sử:

```text
freq[x] = số lần xuất hiện của x
```

Nếu tồn tại:

```text
x + 1
```

thì ta có thể lấy toàn bộ:

```text
x
và
x + 1
```

để tạo harmonious subsequence.

Độ dài sẽ là:

```text
freq[x] + freq[x+1]
```

---

## Ví dụ minh họa

```cpp
nums = [1,3,2,2,5,2,3,7]
```

Tần suất:

```text
1 -> 1
2 -> 3
3 -> 2
5 -> 1
7 -> 1
```

---

Xét:

```text
1 và 2
```

độ dài:

```text
1 + 3 = 4
```

---

Xét:

```text
2 và 3
```

độ dài:

```text
3 + 2 = 5
```

---

Xét:

```text
5 và 6
```

không tồn tại.

---

Kết quả:

```text
5
```

---

# Hướng tiếp cận tối ưu

## Bước 1

Dùng Hash Map đếm số lần xuất hiện.

```cpp
unordered_map<int,int> freq;
```

---

## Bước 2

Duyệt từng phần tử trong Hash Map.

Giả sử đang xét:

```cpp
value = x
```

Nếu tồn tại:

```cpp
x + 1
```

thì:

```cpp
candidate = freq[x] + freq[x+1]
```

Cập nhật đáp án.

---

## Bước 3

Trả về giá trị lớn nhất.

---

# Vì sao thuật toán đúng?

Ta chứng minh như sau.

---

## Mệnh đề 1

Một harmonious subsequence chỉ chứa đúng hai giá trị:

```text
x
và
x+1
```

### Chứng minh

Theo định nghĩa:

```text
max - min = 1
```

Nếu min = x.

Thì:

```text
max = x+1
```

Mọi phần tử đều nằm trong:

```text
[x, x+1]
```

Nên chỉ có thể là:

```text
x hoặc x+1
```

Mệnh đề đúng.

---

## Mệnh đề 2

Nếu tồn tại cả:

```text
x
và
x+1
```

thì subsequence tốt nhất chứa toàn bộ số lần xuất hiện của hai giá trị này.

### Chứng minh

Thêm một phần tử bằng:

```text
x
```

hoặc

```text
x+1
```

không làm thay đổi:

```text
max - min
```

nên luôn có lợi.

Do đó ta lấy toàn bộ.

Mệnh đề đúng.

---

## Mệnh đề 3

Độ dài tối ưu bằng:

```text
max(freq[x] + freq[x+1])
```

với mọi x tồn tại.

Từ Mệnh đề 1 và 2 suy ra thuật toán là đúng.

---

# C++ tối ưu

```cpp
class Solution {
public:
    int findLHS(vector<int>& nums) {

        unordered_map<int, int> freq;

        for (int x : nums) {
            freq[x]++;
        }

        int ans = 0;

        for (auto& [num, cnt] : freq) {

            auto it = freq.find(num + 1);

            if (it != freq.end()) {
                ans = max(ans, cnt + it->second);
            }
        }

        return ans;
    }
};
```

---

# Phân tích độ phức tạp

Gọi:

```text
n = nums.size()
```

---

## Thời gian

Đếm tần suất:

```text
O(n)
```

Duyệt Hash Map:

```text
O(n)
```

(trung bình vì số khóa không vượt quá n)

Tổng:

```text
O(n)
```

---

## Bộ nhớ

Hash Map lưu tần suất:

```text
O(n)
```

---

# Có thể tốt hơn O(n) không?

Không.

Ta phải đọc toàn bộ mảng ít nhất một lần để biết:

```text
- giá trị nào xuất hiện
- xuất hiện bao nhiêu lần
```

nên tồn tại cận dưới:

```text
Ω(n)
```

Thuật toán Hash Map đạt:

```text
O(n)
```

=> tối ưu về thời gian.

---

# Kết luận

Ý tưởng cốt lõi:

```text
Harmonious subsequence chỉ gồm hai giá trị liên tiếp:
x và x+1
```

Do đó:

1. Đếm tần suất từng số.
2. Với mỗi x, kiểm tra x+1.
3. Tính:

   freq[x] + freq[x+1]

4. Lấy giá trị lớn nhất.

Độ phức tạp cuối cùng:

Time : O(n)

Space: O(n)

Đây là lời giải tối ưu nhất cho bài toán Longest Harmonious Subsequence.
