# Distribute Candies — LeetCode

## 1. Tóm tắt bài toán

Cho một mảng `candyType` gồm `n` phần tử, trong đó mỗi phần tử biểu diễn **một loại kẹo**.

Ta biết rằng:

- Có đúng `n` viên kẹo.
- `n` là số chẵn.
- Alice chỉ được ăn đúng `n / 2` viên kẹo.
- Mục tiêu là **tối đa hóa số loại kẹo khác nhau** mà Alice có thể ăn.

Hãy trả về số lượng loại kẹo khác nhau lớn nhất mà Alice có thể nhận được.

Ví dụ:

```text
Input:  candyType = [1,1,2,2,3,3]
Output: 3
```

Alice có thể ăn `[1, 2, 3]`, tức là nhận được 3 loại khác nhau.

---

## 2. Điều quan trọng nhất cần nhận ra

Thoạt nhìn, bài toán có vẻ yêu cầu thử nhiều cách chọn `n / 2` viên kẹo.

Nhưng thực tế ta **không cần quan tâm đến thứ tự các viên kẹo**, cũng không cần tìm chính xác Alice sẽ ăn những viên nào.

Ta chỉ cần biết:

> Có bao nhiêu **loại kẹo khác nhau** xuất hiện trong toàn bộ mảng?

Gọi:

- `n` = tổng số viên kẹo.
- `n / 2` = số viên Alice được ăn.
- `distinct` = số loại kẹo khác nhau.

Số loại Alice có thể nhận được không thể vượt quá:

```text
n / 2
```

vì Alice chỉ ăn `n / 2` viên.

Đồng thời, số loại Alice có thể nhận được cũng không thể vượt quá:

```text
distinct
```

vì toàn bộ thế giới chỉ có `distinct` loại.

Do đó đáp án chắc chắn không vượt quá:

```text
min(distinct, n / 2)
```

Điểm mấu chốt là:

> Giá trị `min(distinct, n / 2)` không chỉ là một upper bound — nó luôn đạt được.

Vì vậy:

```text
answer = min(số loại khác nhau, n / 2)
```

---

# 3. Phân tích trực giác

Giả sử có `n = 6` viên kẹo.

Alice được ăn:

```text
6 / 2 = 3
```

viên.

### Trường hợp 1: Có 3 loại khác nhau

Ví dụ:

```text
[1, 1, 2, 2, 3, 3]
```

Có:

```text
distinct = 3
n / 2 = 3
```

Alice có thể lấy:

```text
[1, 2, 3]
```

nên đáp án là:

```text
3
```

Tức:

```text
min(3, 3) = 3
```

---

### Trường hợp 2: Có nhiều loại hơn số viên Alice được ăn

Ví dụ:

```text
[1, 2, 3, 4, 5, 6]
```

Có:

```text
distinct = 6
n / 2 = 3
```

Alice chỉ được ăn 3 viên nên dù có 6 loại khác nhau, cô ấy cũng chỉ có thể nhận tối đa 3 loại.

Chọn:

```text
[1, 2, 3]
```

là đủ.

Do đó:

```text
answer = min(6, 3) = 3
```

---

### Trường hợp 3: Có ít loại hơn số viên Alice được ăn

Ví dụ:

```text
[1, 1, 1, 2, 2, 2]
```

Có:

```text
distinct = 2
n / 2 = 3
```

Alice phải ăn 3 viên, nhưng toàn bộ chỉ có 2 loại.

Ví dụ Alice ăn:

```text
[1, 1, 2]
```

thì cô ấy chỉ nhận được 2 loại.

Không thể có loại thứ 3 vì loại thứ 3 không tồn tại.

Do đó:

```text
answer = min(2, 3) = 2
```

---

# 4. Chứng minh đáp án `min(distinct, n / 2)`

Đây là phần quan trọng nhất để hiểu bản chất bài toán.

Đặt:

```text
k = n / 2
```

là số viên Alice được ăn.

Và:

```text
d = distinct
```

là số loại kẹo khác nhau.

Ta cần chứng minh:

```text
answer = min(d, k)
```

## 4.1. Upper bound

Có hai giới hạn hiển nhiên.

### Giới hạn 1: Alice chỉ ăn được `k` viên

Mỗi loại khác nhau mà Alice nhận được phải tương ứng với ít nhất một viên kẹo.

Vì Alice chỉ có `k` viên nên:

```text
answer <= k
```

### Giới hạn 2: Chỉ có `d` loại trên toàn bộ mảng

Alice không thể nhận một loại kẹo chưa từng xuất hiện.

Do đó:

```text
answer <= d
```

Kết hợp hai điều kiện:

```text
answer <= min(d, k)
```

---

## 4.2. Chứng minh upper bound luôn đạt được

Bây giờ ta chứng minh rằng Alice luôn có thể đạt được đúng:

```text
min(d, k)
```

### Nếu `d <= k`

Có không quá `k` loại khác nhau.

Alice có thể lấy ít nhất một viên từ mỗi loại.

Vì có `d <= k`, việc lấy một viên từ mỗi loại vẫn không vượt quá số viên Alice được phép ăn.

Sau đó, nếu cần Alice phải ăn đủ `k` viên, cô ấy có thể lấy thêm các viên thuộc những loại đã có.

Vì vậy Alice có thể nhận được:

```text
d
```

loại.

---

### Nếu `d > k`

Có nhiều hơn `k` loại khác nhau.

Alice chỉ cần chọn `k` loại bất kỳ và lấy một viên của mỗi loại.

Vì mỗi loại xuất hiện ít nhất một lần nên việc này luôn thực hiện được.

Alice sẽ nhận được:

```text
k
```

loại.

---

## 4.3. Kết luận

Hai trường hợp trên cho thấy:

```text
answer >= min(d, k)
```

Trong khi phần upper bound đã chứng minh:

```text
answer <= min(d, k)
```

Suy ra:

```text
answer = min(d, k)
```

Đây chính là toàn bộ bản chất của bài toán.

---

# 5. Phương hướng giải quyết

Ta chỉ cần thực hiện hai bước:

## Bước 1: Đếm số loại kẹo khác nhau

Sử dụng `unordered_set<int>`.

Mỗi giá trị `candyType[i]` được đưa vào set:

```cpp
unordered_set<int> types;

for (int candy : candyType) {
    types.insert(candy);
}
```

Sau vòng lặp:

```cpp
types.size()
```

chính là số loại kẹo khác nhau.

---

## Bước 2: Lấy giá trị nhỏ hơn giữa hai giới hạn

Alice chỉ ăn được:

```cpp
candyType.size() / 2
```

viên.

Số loại thực tế có:

```cpp
types.size()
```

Vì vậy:

```cpp
return min(types.size(), candyType.size() / 2);
```

---

# 6. Cài đặt C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        unordered_set<int> types;

        for (int candy : candyType) {
            types.insert(candy);
        }

        return min(
            (int)types.size(),
            (int)candyType.size() / 2
        );
    }
};
```

---

# 7. Giải thích code

## Khởi tạo `unordered_set`

```cpp
unordered_set<int> types;
```

`unordered_set` chỉ lưu mỗi giá trị một lần.

Ví dụ:

```text
candyType = [1, 1, 2, 2, 3, 3]
```

Sau khi insert:

```text
types = {1, 2, 3}
```

nên:

```cpp
types.size() == 3
```

---

## Duyệt qua toàn bộ mảng

```cpp
for (int candy : candyType) {
    types.insert(candy);
}
```

Mỗi viên kẹo được đưa vào set.

Nếu loại kẹo đã tồn tại thì `unordered_set` không tạo thêm phần tử trùng.

Ví dụ:

```text
1 -> {1}
1 -> {1}
2 -> {1,2}
2 -> {1,2}
3 -> {1,2,3}
```

---

## Tính số viên Alice được ăn

```cpp
candyType.size() / 2
```

Theo đề bài, Alice được ăn đúng một nửa tổng số kẹo.

Nếu:

```text
n = 10
```

thì:

```text
n / 2 = 5
```

---

## Lấy giới hạn nhỏ hơn

```cpp
min(types.size(), candyType.size() / 2)
```

Có hai giới hạn:

```text
types.size()
```

là số loại có thể tồn tại.

Và:

```text
candyType.size() / 2
```

là số viên Alice có thể ăn.

Đáp án là giới hạn nhỏ hơn.

---

# 8. Ví dụ chạy từng bước

Xét:

```text
candyType = [1, 1, 2, 2, 3, 3, 4, 4]
```

Có:

```text
n = 8
```

Alice được ăn:

```text
8 / 2 = 4
```

viên.

Các loại khác nhau:

```text
{1, 2, 3, 4}
```

nên:

```text
distinct = 4
```

Do đó:

```text
answer = min(4, 4)
       = 4
```

Alice có thể lấy:

```text
[1, 2, 3, 4]
```

và nhận được 4 loại.

---

# 9. Ví dụ khi số loại ít hơn số viên được ăn

```text
candyType = [1, 1, 1, 1, 2, 2, 2, 2]
```

Ta có:

```text
n = 8
n / 2 = 4
```

Nhưng chỉ có:

```text
{1, 2}
```

nên:

```text
distinct = 2
```

Kết quả:

```text
answer = min(2, 4)
       = 2
```

Dù Alice phải ăn 4 viên, cô ấy không thể có hơn 2 loại vì toàn bộ hộp chỉ có 2 loại.

Ví dụ:

```text
[1, 1, 2, 2]
```

vẫn chỉ có 2 loại.

---

# 10. Ví dụ khi số loại nhiều hơn số viên được ăn

```text
candyType = [1, 2, 3, 4, 5, 6, 7, 8]
```

Ta có:

```text
n = 8
n / 2 = 4
```

Số loại:

```text
distinct = 8
```

Alice chỉ được ăn 4 viên nên:

```text
answer = min(8, 4)
       = 4
```

Ví dụ chọn:

```text
[1, 3, 5, 7]
```

Alice nhận được 4 loại.

---

# 11. Độ phức tạp

Gọi:

```text
n = candyType.size()
```

## Time Complexity

Ta duyệt qua `n` phần tử và thực hiện `insert` vào `unordered_set`.

Trung bình, thao tác insert có độ phức tạp:

```text
O(1)
```

Do đó:

```text
Time: O(n)
```

## Space Complexity

Trong trường hợp tất cả các viên đều thuộc loại khác nhau, set chứa `n` phần tử.

Do đó:

```text
Space: O(n)
```

Tổng kết:

```text
Time:  O(n) trung bình
Space: O(n)
```

---

# 12. Có thể dùng `set` thay cho `unordered_set` không?

Có.

Ví dụ:

```cpp
set<int> types;

for (int candy : candyType) {
    types.insert(candy);
}
```

Nhưng `set` thường có:

```text
insert = O(log n)
```

nên tổng thời gian:

```text
O(n log n)
```

Trong khi `unordered_set` có thời gian trung bình:

```text
O(1)
```

cho mỗi lần insert.

Vì vậy với bài toán này, `unordered_set` là lựa chọn tự nhiên nếu chỉ cần biết số lượng phần tử khác nhau.

---

# 13. Có thể giải bằng `sort` không?

Có.

Ta có thể:

1. Sort mảng.
2. Đếm số lần giá trị thay đổi.
3. Lấy `min(distinct, n / 2)`.

Ví dụ:

```cpp
class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        sort(candyType.begin(), candyType.end());

        int distinct = 0;

        for (int i = 0; i < candyType.size(); ++i) {
            if (i == 0 || candyType[i] != candyType[i - 1]) {
                ++distinct;
            }
        }

        return min(distinct, (int)candyType.size() / 2);
    }
};
```

Độ phức tạp:

```text
Time:  O(n log n)
Space: O(1) extra
```

Nếu được phép thay đổi thứ tự mảng, cách này hợp lệ.

Tuy nhiên, xét riêng bài toán này, `unordered_set` giúp biểu diễn trực tiếp ý tưởng "đếm số loại khác nhau", nên lời giải bằng hash set thường ngắn gọn và dễ đọc hơn.

---

# 14. Có cần Dynamic Programming / Greedy không?

Không.

Đây là một điểm đáng chú ý.

Bài toán có từ "maximize" nên dễ khiến ta nghĩ đến:

- Greedy.
- Dynamic Programming.
- Backtracking.
- Combinatorics.

Nhưng không cần những kỹ thuật đó.

Lý do là mọi loại kẹo đều có giá trị giống nhau đối với mục tiêu của chúng ta:

> Mỗi loại chỉ cần xuất hiện ít nhất một lần trong tập kẹo Alice ăn.

Không có:

- trọng số,
- chi phí,
- thứ tự,
- ràng buộc giữa các loại,
- hay lựa chọn này ảnh hưởng đến lựa chọn kia.

Vì vậy bài toán được rút gọn hoàn toàn thành:

```text
Đếm số giá trị khác nhau
+
Giới hạn số viên Alice được ăn
```

---

# 15. Những cách tiếp cận dễ bị overthink

## Cách 1: Sinh tất cả các tập con

Có thể nghĩ:

> Alice chọn `n / 2` viên trong `n` viên, vậy phải thử tất cả tổ hợp.

Số tổ hợp là:

```text
C(n, n/2)
```

rất lớn.

Hoàn toàn không cần thiết.

Chúng ta không cần biết chính xác tập con nào được chọn.

Chỉ cần biết số loại khác nhau trong toàn bộ mảng.

---

## Cách 2: Đếm frequency của từng loại

Có thể tạo:

```cpp
unordered_map<int, int> freq;
```

rồi đếm số lần xuất hiện.

Điều này vẫn đúng, nhưng thực ra là thừa thông tin.

Chúng ta chỉ cần:

```text
Có xuất hiện loại này hay không?
```

chứ không cần:

```text
Loại này xuất hiện bao nhiêu lần?
```

Vì vậy `unordered_set` là đủ.

---

# 16. Pattern quan trọng cần ghi nhớ

Bài toán này là một ví dụ rất điển hình của pattern:

> **Maximize the number of distinct categories under a fixed selection limit.**

Nếu có:

```text
D = số loại khác nhau
K = số phần tử được chọn
```

và mỗi loại có thể đóng góp ít nhất một phần tử, thì số loại tối đa thường sẽ bị giới hạn bởi:

```text
min(D, K)
```

Điều quan trọng là phải kiểm tra xem giới hạn đó **có luôn đạt được hay không**.

Trong bài này:

- Nếu `D <= K`: lấy ít nhất một phần tử của mỗi loại.
- Nếu `D > K`: lấy một phần tử của `K` loại bất kỳ.

Vì vậy upper bound chính là đáp án.

Đây là tư duy rất hữu ích khi gặp các bài toán tối ưu hóa tưởng như cần thử nhiều trường hợp.

---

# 17. Template tư duy để áp dụng cho bài khác

Khi gặp một bài có dạng:

> Chọn tối đa / chính xác `K` phần tử để tối đa hóa số lượng giá trị khác nhau.

Hãy tự hỏi:

### Câu hỏi 1

Có bao nhiêu giá trị khác nhau trong toàn bộ dữ liệu?

```text
D
```

### Câu hỏi 2

Ta được phép chọn bao nhiêu phần tử?

```text
K
```

### Câu hỏi 3

Mỗi giá trị khác nhau có thể được đại diện bởi ít nhất một phần tử không?

Nếu có, hãy thử kiểm tra:

```text
min(D, K)
```

### Câu hỏi 4

Có ràng buộc nào khiến một số loại không thể cùng xuất hiện trong lựa chọn không?

Nếu không, `min(D, K)` thường chính là đáp án.

---

# 18. Lời giải tối ưu đề xuất

Đây là phiên bản nên dùng khi submit:

```cpp
class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        unordered_set<int> types(candyType.begin(), candyType.end());

        return min(
            (int)types.size(),
            (int)candyType.size() / 2
        );
    }
};
```

Cách viết này tận dụng constructor của `unordered_set` để đưa toàn bộ phần tử của vector vào set.

## Vì sao phiên bản này tốt?

Nó thể hiện rất trực tiếp ý tưởng toán học:

```text
distinct types = size(unordered_set)
answer = min(distinct types, number of candies Alice can eat)
```

Không có logic dư thừa.

---

# 19. Kết luận

Bài toán `Distribute Candies` thực chất không phải là bài toán chọn tổ hợp.

Điểm mấu chốt là nhận ra hai giới hạn:

```text
1. Alice chỉ ăn được n / 2 viên
2. Toàn bộ mảng chỉ có D loại khác nhau
```

Do đó:

```text
answer <= min(D, n / 2)
```

và vì giới hạn này luôn đạt được:

```text
answer = min(D, n / 2)
```

Cài đặt bằng `unordered_set`:

```cpp
class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        unordered_set<int> types(candyType.begin(), candyType.end());

        return min(
            (int)types.size(),
            (int)candyType.size() / 2
        );
    }
};
```

### Complexity

```text
Time:  O(n) average
Space: O(n)
```

### Core insight

```text
Đếm số loại khác nhau
        ↓
So sánh với số viên được phép chọn
        ↓
min(distinct, n / 2)
```

Đây là một ví dụ rất tốt về việc tìm **giới hạn (bound)** trước khi nghĩ đến việc mô phỏng hoặc thử tất cả các lựa chọn.
