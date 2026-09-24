# Largest Number — LeetCode

> **Bài toán:** [Largest Number](https://leetcode.com/problems/largest-number/)  
> **Mục tiêu:** Sắp xếp các số sao cho khi nối chúng lại với nhau, ta thu được số lớn nhất có thể.

---

## 1. Phân tích bài toán

Cho một mảng số nguyên không âm `nums`.

Ta được phép sắp xếp lại thứ tự các phần tử. Sau đó, nối tất cả các số lại thành một chuỗi duy nhất.

### Ví dụ

```text
nums = [10, 2]

Có hai cách:

10 + 2 = "102"
2 + 10 = "210"

=> Kết quả: "210"
```

Vì `210 > 102`, ta đặt `2` trước `10`.

Một ví dụ khác:

```text
nums = [3, 30, 34, 5, 9]
```

Thứ tự tối ưu:

```text
9, 5, 34, 3, 30
```

Nối lại:

```text
"9534330"
```

Vì vậy đáp án là:

```text
9534330
```

---

# 2. Điểm khó của bài toán

Thoạt nhìn, bài toán có vẻ giống một bài **sắp xếp giảm dần**.

Ta có thể nghĩ:

```text
9 > 5 > 34 > 3 > 30
```

nên cứ sort giảm dần là được.

Nhưng cách này **không đúng**.

Ví dụ:

```text
nums = [3, 30]
```

Nếu so sánh số thông thường:

```text
30 > 3
```

ta sẽ đặt:

```text
30, 3
```

và nhận được:

```text
"303"
```

Nhưng nếu đặt:

```text
3, 30
```

ta nhận được:

```text
"330"
```

Rõ ràng:

```text
330 > 303
```

Do đó, **thứ tự giữa hai số không thể được quyết định chỉ bằng cách so sánh giá trị của chúng**.

---

# 3. Ý tưởng quan trọng nhất: so sánh bằng phép nối

Giả sử ta có hai số:

```text
a
b
```

Ta cần quyết định xem `a` nên đứng trước `b` hay ngược lại.

Có hai khả năng:

```text
a + b
```

hoặc:

```text
b + a
```

Trong đó `+` ở đây có nghĩa là **nối chuỗi**, không phải phép cộng số học.

### Quy tắc

Nếu:

```text
a + b > b + a
```

thì:

```text
a đứng trước b
```

Ngược lại:

```text
b đứng trước a
```

Đây chính là quy tắc sắp xếp quan trọng nhất của bài toán.

---

# 4. Ví dụ với `3` và `30`

Chuyển hai số thành chuỗi:

```text
a = "3"
b = "30"
```

Xét hai cách:

```text
a + b = "330"
b + a = "303"
```

So sánh:

```text
"330" > "303"
```

Vì vậy:

```text
3 đứng trước 30
```

---

# 5. Ví dụ với `34` và `3`

Ta có:

```text
a = "34"
b = "3"
```

Hai cách:

```text
a + b = "343"
b + a = "334"
```

Vì:

```text
"343" > "334"
```

nên:

```text
34 đứng trước 3
```

---

# 6. Ví dụ với `9` và `34`

```text
a = "9"
b = "34"
```

Ta xét:

```text
"934"
"349"
```

Vì:

```text
934 > 349
```

nên:

```text
9 đứng trước 34
```

---

# 7. Comparator cần xây dựng

Ta có thể sử dụng `sort()` của C++.

Thông thường:

```cpp
sort(nums.begin(), nums.end());
```

sẽ sắp xếp dựa trên thứ tự thông thường.

Nhưng bài toán này yêu cầu một quy tắc khác.

Ta cần một comparator:

```cpp
bool compare(string a, string b) {
    return a + b > b + a;
}
```

Ý nghĩa:

```cpp
a + b > b + a
```

thì `a` được đặt trước `b`.

Sau khi `sort()` hoàn thành, các phần tử sẽ nằm theo thứ tự giúp tạo ra số lớn nhất.

---

# 8. Vì sao chỉ cần so sánh từng cặp?

Đây là phần quan trọng nhất về mặt thuật toán.

Ta không cần thử tất cả các hoán vị.

Nếu với mọi cặp `a`, `b`, ta luôn quyết định thứ tự bằng:

```text
a + b > b + a
```

thì ta có một quan hệ sắp xếp phù hợp với việc tối đa hóa chuỗi nối cuối cùng.

Có thể hiểu trực giác như sau:

Giả sử trong một kết quả nào đó có hai phần tử liên tiếp:

```text
... a b ...
```

Nếu:

```text
b + a > a + b
```

thì đổi chúng thành:

```text
... b a ...
```

sẽ làm toàn bộ kết quả lớn hơn.

Do đó, một kết quả tối ưu không thể chứa cặp liền kề nào đang ở sai thứ tự theo comparator.

Vì `sort()` đưa các phần tử về đúng thứ tự đó, chuỗi sau khi nối là lớn nhất.

---

# 9. Ví dụ đầy đủ

Cho:

```text
nums = [3, 30, 34, 5, 9]
```

Chuyển thành:

```text
["3", "30", "34", "5", "9"]
```

Một số phép so sánh:

### `3` và `30`

```text
330 > 303
```

=> `3` trước `30`.

### `34` và `3`

```text
343 > 334
```

=> `34` trước `3`.

### `5` và `34`

```text
534 > 345
```

=> `5` trước `34`.

### `9` và `5`

```text
95 > 59
```

=> `9` trước `5`.

Sau khi sắp xếp:

```text
["9", "5", "34", "3", "30"]
```

Nối lại:

```text
9534330
```

Đây là đáp án.

---

# 10. Tại sao không thể dùng so sánh số thông thường?

Giả sử:

```text
nums = [3, 30, 34, 5, 9]
```

Nếu sort giảm dần theo giá trị:

```text
34, 30, 9, 5, 3
```

Nối lại:

```text
3430953
```

Nhưng đáp án đúng là:

```text
9534330
```

Điều này cho thấy:

> Bài toán không yêu cầu sắp xếp các số theo giá trị, mà yêu cầu sắp xếp theo **khả năng tạo ra chuỗi lớn hơn khi ghép nối**.

---

# 11. Xử lý trường hợp toàn số `0`

Đây là một edge case quan trọng.

Ví dụ:

```text
nums = [0, 0, 0]
```

Sau khi sort, ta vẫn có:

```text
["0", "0", "0"]
```

Nếu nối bình thường:

```text
"000"
```

Nhưng đáp án mong muốn là:

```text
"0"
```

Vì:

```text
000 = 0
```

Trong bài toán, ta không cần trả về các số 0 dư thừa ở đầu.

Cách đơn giản nhất:

```cpp
if (nums[0] == "0")
    return "0";
```

Tại sao chỉ cần kiểm tra `nums[0]`?

Sau khi sắp xếp theo comparator:

```cpp
a + b > b + a
```

nếu phần tử đầu tiên là `"0"` thì mọi phần tử còn lại cũng chỉ có thể là `"0"`.

Do đó toàn bộ mảng là:

```text
0, 0, 0, ...
```

và ta chỉ cần trả về:

```text
"0"
```

---

# 12. Tại sao phải chuyển sang `string`?

Đề bài có thể chứa các số có nhiều chữ số.

Nếu nối trực tiếp bằng số nguyên, ta sẽ gặp vấn đề:

- phép cộng số nguyên không phải phép nối;
- kết quả có thể vượt giới hạn kiểu dữ liệu;
- ta thực chất cần tạo ra một chuỗi có thể rất dài.

Ví dụ:

```text
nums = [999999999, 999999998, 999999997]
```

Kết quả nối có thể có hàng chục chữ số.

Vì vậy, ta chuyển từng số thành:

```cpp
to_string(nums[i])
```

và làm việc với `string`.

---

# 13. Thuật toán tối ưu

Các bước:

### Bước 1

Chuyển tất cả số nguyên thành chuỗi.

```cpp
vector<string> nums;
```

### Bước 2

Sắp xếp bằng comparator:

```cpp
a + b > b + a
```

### Bước 3

Nếu phần tử đầu tiên là `"0"`:

```cpp
return "0";
```

### Bước 4

Nối tất cả chuỗi lại.

```cpp
string ans;

for (string s : nums) {
    ans += s;
}
```

### Bước 5

Trả về `ans`.

---

# 14. C++ Implementation

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {

        // Chuyển các số nguyên thành chuỗi
        vector<string> arr;

        for (int num : nums) {
            arr.push_back(to_string(num));
        }

        // Sắp xếp theo quy tắc:
        // a đứng trước b nếu a + b > b + a
        sort(arr.begin(), arr.end(), [](const string& a, const string& b) {
            return a + b > b + a;
        });

        // Nếu phần tử đầu tiên là "0",
        // toàn bộ các phần tử đều là "0"
        if (arr[0] == "0") {
            return "0";
        }

        // Ghép tất cả chuỗi lại
        string ans;

        for (const string& s : arr) {
            ans += s;
        }

        return ans;
    }
};
```

---

# 15. Giải thích code

## 15.1. Chuyển `int` thành `string`

```cpp
vector<string> arr;

for (int num : nums) {
    arr.push_back(to_string(num));
}
```

Ví dụ:

```text
nums = [3, 30, 34, 5, 9]
```

sẽ trở thành:

```text
arr = ["3", "30", "34", "5", "9"]
```

---

## 15.2. Custom comparator

```cpp
sort(arr.begin(), arr.end(), [](const string& a, const string& b) {
    return a + b > b + a;
});
```

Đây là phần cốt lõi.

Ví dụ:

```text
a = "3"
b = "30"
```

ta kiểm tra:

```text
a + b = "330"
b + a = "303"
```

Do:

```text
330 > 303
```

nên comparator trả về:

```cpp
true
```

và `3` được đặt trước `30`.

---

## 15.3. Xử lý toàn số 0

```cpp
if (arr[0] == "0") {
    return "0";
}
```

Ví dụ:

```text
[0, 0, 0]
```

Sau sort:

```text
["0", "0", "0"]
```

Không cần tạo:

```text
"000"
```

mà trả về:

```text
"0"
```

---

## 15.4. Nối kết quả

```cpp
string ans;

for (const string& s : arr) {
    ans += s;
}
```

Nếu:

```text
arr = ["9", "5", "34", "3", "30"]
```

thì:

```text
ans = "9"
ans = "95"
ans = "9534"
ans = "95343"
ans = "9534330"
```

Cuối cùng:

```text
"9534330"
```

---

# 16. Độ phức tạp

Gọi:

- `n` là số lượng phần tử.
- `k` là độ dài trung bình của một số khi biểu diễn dưới dạng chuỗi.

## Thời gian

`sort()` thực hiện khoảng:

```text
O(n log n)
```

lần so sánh.

Mỗi lần so sánh cần tạo/so sánh:

```text
a + b
b + a
```

nên tốn khoảng:

```text
O(k)
```

Do đó độ phức tạp tổng quát:

```text
O(n log n × k)
```

Nếu coi độ dài số là bị giới hạn bởi một hằng số, thường có thể viết đơn giản là:

```text
O(n log n)
```

## Bộ nhớ

Ta cần lưu các số dưới dạng chuỗi:

```text
O(n × k)
```

Ngoài ra `sort()` sử dụng bộ nhớ phụ tùy thuộc implementation, nhưng phần dữ liệu chính vẫn là mảng chuỗi.

---

# 17. Tại sao comparator này là đúng?

Ta có thể chứng minh bằng phép đổi chỗ.

Giả sử trong một kết quả có hai phần tử liên tiếp:

```text
... a b ...
```

Phần tương ứng trong chuỗi cuối cùng là:

```text
a + b
```

Nếu:

```text
b + a > a + b
```

thì đổi vị trí thành:

```text
... b a ...
```

sẽ tạo ra một kết quả lớn hơn.

Phần còn lại của chuỗi nằm trước và sau `a, b` không thay đổi.

Do đó, trong một lời giải tối ưu, không thể tồn tại cặp `a, b` thỏa:

```text
b + a > a + b
```

nhưng lại đặt:

```text
a trước b
```

Vì vậy, thứ tự tối ưu phải tuân theo:

```text
a + b > b + a
```

Đây chính là quy tắc comparator.

---

# 18. Một ví dụ để hiểu chứng minh

Giả sử đang có:

```text
[34, 3]
```

Nếu đặt:

```text
34, 3
```

ta được:

```text
343
```

Nếu đổi thành:

```text
3, 34
```

ta được:

```text
334
```

Vì:

```text
343 > 334
```

nên:

```text
34
```

phải đứng trước:

```text
3
```

Tương tự, quy tắc này được áp dụng cho mọi cặp phần tử.

---

# 19. Một lỗi tư duy thường gặp

## Sai lầm 1: Sort giảm dần theo giá trị

```cpp
sort(nums.begin(), nums.end(), greater<int>());
```

Sai vì:

```text
3 > 30
```

là sai theo giá trị, nhưng trong bài toán:

```text
330 > 303
```

nên `3` phải đứng trước `30`.

---

## Sai lầm 2: So sánh chữ số đầu tiên

Có thể nghĩ rằng số có chữ số đầu lớn hơn nên đứng trước.

Ví dụ:

```text
9 > 8
```

đúng, nhưng khi hai số có cùng chữ số đầu hoặc độ dài khác nhau, cách này không đủ.

Quy tắc chính xác vẫn là:

```text
a + b > b + a
```

---

## Sai lầm 3: Cộng hai số

Không được làm:

```cpp
a + b
```

theo nghĩa số học.

Ở đây phải hiểu:

```cpp
string a;
string b;

a + b
```

là **nối chuỗi**.

Ví dụ:

```text
"3" + "30" = "330"
```

chứ không phải:

```text
33
```

---

## Sai lầm 4: Trả về `"000"`

Nếu tất cả phần tử đều bằng `0`, phải trả về:

```text
"0"
```

thay vì:

```text
"000..."
```

---

# 20. Dry Run

Với:

```text
nums = [3, 30, 34, 5, 9]
```

### Sau khi chuyển sang string

```text
["3", "30", "34", "5", "9"]
```

### So sánh một số cặp

```text
3 và 30:
330 > 303
=> 3 trước 30

34 và 3:
343 > 334
=> 34 trước 3

5 và 34:
534 > 345
=> 5 trước 34

9 và 5:
95 > 59
=> 9 trước 5
```

### Sau khi sort

```text
["9", "5", "34", "3", "30"]
```

### Nối

```text
9
95
9534
95343
9534330
```

### Kết quả

```text
"9534330"
```

---

# 21. Những kiến thức DSA rút ra từ bài này

Bài **Largest Number** rất đáng chú ý vì nó không đơn thuần là một bài sorting.

## Kiến thức 1 — Custom Comparator

Thay vì:

```cpp
a < b
```

ta có thể xây dựng một quy tắc hoàn toàn khác:

```cpp
a + b > b + a
```

Đây là kỹ năng quan trọng khi giải các bài cần sắp xếp theo tiêu chí đặc biệt.

---

## Kiến thức 2 — Không phải lúc nào "lớn hơn" cũng là đứng trước

Trong bài toán thông thường:

```text
10 < 20
```

nên `10` đứng trước `20` khi sort tăng dần.

Nhưng trong bài này, ta không quan tâm trực tiếp đến:

```text
10 < 20
```

mà quan tâm đến:

```text
1020
```

hay:

```text
2010
```

lớn hơn.

Vì vậy tiêu chí phải dựa trên **tác động của phần tử khi được ghép vào đáp án**.

---

## Kiến thức 3 — Greedy + Sorting

Ta không thử tất cả hoán vị.

Thay vào đó:

1. Xây dựng quy tắc ưu tiên giữa hai phần tử.
2. Sort toàn bộ mảng theo quy tắc đó.
3. Ghép các phần tử.

Đây là một dạng tư duy **greedy ordering** rất thường gặp.

---

# 22. Công thức cần nhớ

Nếu gặp bài yêu cầu:

> "Sắp xếp các phần tử để khi ghép chúng lại tạo thành số lớn nhất."

Hãy nghĩ ngay đến:

```text
a + b > b + a
```

Tương ứng trong C++:

```cpp
return a + b > b + a;
```

Đây là **key insight** của bài Largest Number.

---

# 23. Complexity Summary

| Thành phần | Độ phức tạp |
|---|---:|
| Chuyển `int` → `string` | `O(n × k)` |
| Sorting | `O(n log n × k)` |
| Ghép kết quả | `O(n × k)` |
| **Tổng** | **O(n log n × k)** |
| Bộ nhớ | `O(n × k)` |

Trong đó `k` là độ dài biểu diễn của số.

---

# 24. Final Code

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {

        vector<string> arr;

        for (int num : nums) {
            arr.push_back(to_string(num));
        }

        sort(arr.begin(), arr.end(), [](const string& a, const string& b) {
            return a + b > b + a;
        });

        if (arr[0] == "0") {
            return "0";
        }

        string ans;

        for (const string& s : arr) {
            ans += s;
        }

        return ans;
    }
};
```

## Tóm tắt một câu

> **Chuyển các số thành chuỗi, sắp xếp theo quy tắc `a + b > b + a`, sau đó nối toàn bộ chuỗi lại để thu được số lớn nhất.**
