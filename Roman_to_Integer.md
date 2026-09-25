# Roman to Integer — LeetCode

**Link bài toán:** https://leetcode.com/problems/roman-to-integer/

---

## 1. Mô tả bài toán

Cho một chuỗi `s` biểu diễn một số La Mã (Roman numeral), hãy chuyển nó thành số nguyên tương ứng.

Các ký hiệu Roman được sử dụng:

| Ký hiệu | Giá trị |
|---|---:|
| `I` | 1 |
| `V` | 5 |
| `X` | 10 |
| `L` | 50 |
| `C` | 100 |
| `D` | 500 |
| `M` | 1000 |

Ví dụ:

```text
III   -> 3
LVIII -> 58
MCMXCIV -> 1994
```

### Các trường hợp trừ đặc biệt

Thông thường, các ký hiệu được cộng lại với nhau. Tuy nhiên, một ký hiệu nhỏ đứng trước một ký hiệu lớn sẽ tạo thành phép trừ.

Các cặp hợp lệ là:

```text
IV = 4
IX = 9
XL = 40
XC = 90
CD = 400
CM = 900
```

Ví dụ:

```text
MCMXCIV
= M + CM + XC + IV
= 1000 + 900 + 90 + 4
= 1994
```

---

# 2. Phân tích bản chất bài toán

Điểm quan trọng nhất của bài toán nằm ở việc xác định:

> Khi nào giá trị của ký hiệu hiện tại phải được cộng, và khi nào phải được trừ?

Giả sử ta đang xét hai ký hiệu liên tiếp:

```text
X I
```

Ta có:

```text
X = 10
I = 1
```

Vì:

```text
10 > 1
```

nên `X` được cộng:

```text
10 + 1 = 11
```

Ngược lại:

```text
I X
```

thì:

```text
1 < 10
```

nên `I` phải được trừ:

```text
-1 + 10 = 9
```

Vì vậy, thay vì phải liệt kê riêng từng trường hợp:

```text
IV
IX
XL
XC
CD
CM
```

ta có thể sử dụng một quy tắc tổng quát:

> **Nếu giá trị của ký hiệu hiện tại nhỏ hơn giá trị của ký hiệu ngay sau nó thì trừ giá trị hiện tại, ngược lại cộng giá trị hiện tại.**

Đây chính là ý tưởng tối ưu và đơn giản nhất cho bài toán.

---

# 3. Ý tưởng thuật toán

Ta duyệt chuỗi từ trái sang phải.

Tại vị trí `i`:

- Lấy giá trị của `s[i]`.
- Nếu `i` chưa phải ký tự cuối và:

```text
value(s[i]) < value(s[i + 1])
```

thì:

```text
ans -= value(s[i])
```

- Ngược lại:

```text
ans += value(s[i])
```

## Ví dụ: `MCMXCIV`

Ta xét từng ký tự:

| Vị trí | Ký tự | Giá trị | Ký tự sau | So sánh | Thao tác | Tổng |
|---:|:---:|---:|:---:|:---|---:|---:|
| 0 | `M` | 1000 | `C` = 100 | `1000 > 100` | `+1000` | 1000 |
| 1 | `C` | 100 | `M` = 1000 | `100 < 1000` | `-100` | 900 |
| 2 | `M` | 1000 | `X` = 10 | `1000 > 10` | `+1000` | 1900 |
| 3 | `X` | 10 | `C` = 100 | `10 < 100` | `-10` | 1890 |
| 4 | `C` | 100 | `I` = 1 | `100 > 1` | `+100` | 1990 |
| 5 | `I` | 1 | `V` = 5 | `1 < 5` | `-1` | 1989 |
| 6 | `V` | 5 | — | ký tự cuối | `+5` | 1994 |

Kết quả:

```text
1994
```

---

# 4. Vì sao chỉ cần so sánh với ký tự kế tiếp?

Đây là điểm quan trọng nhất của lời giải.

Xét một đoạn:

```text
XVI
```

Giá trị:

```text
X = 10
V = 5
I = 1
```

Ta có:

```text
10 > 5 -> +10
5 > 1  -> +5
1       -> +1
```

Kết quả:

```text
16
```

Bây giờ xét:

```text
XIV
```

Ta có:

```text
X = 10
I = 1
V = 5
```

Ở `I`:

```text
1 < 5
```

nên `I` phải được trừ:

```text
10 - 1 + 5 = 14
```

Như vậy, chính quan hệ giữa **ký tự hiện tại và ký tự kế tiếp** đã đủ để quyết định ký tự hiện tại là cộng hay trừ.

Ta không cần:

- xây dựng các cặp `IV`, `IX`, `XL`, ...
- dùng nhiều `if` để kiểm tra từng trường hợp;
- quay lui;
- tìm kiếm;
- sắp xếp.

---

# 5. Cách ánh xạ ký hiệu Roman sang giá trị

Ta cần một cách nhanh để lấy giá trị của từng ký hiệu.

Có thể sử dụng `switch`:

```cpp
int giaTri(char c) {
    switch (c) {
        case 'I': return 1;
        case 'V': return 5;
        case 'X': return 10;
        case 'L': return 50;
        case 'C': return 100;
        case 'D': return 500;
        case 'M': return 1000;
    }
    return 0;
}
```

Tuy nhiên, trong lời giải LeetCode, ta có thể dùng `unordered_map`, hoặc tối ưu hơn nữa bằng một mảng có kích thước cố định.

Vì các ký tự Roman chỉ nằm trong phạm vi ASCII và chỉ có 7 ký hiệu, ta có thể ánh xạ trực tiếp:

```cpp
int val[256] = {};
```

Sau đó:

```cpp
val['I'] = 1;
val['V'] = 5;
val['X'] = 10;
val['L'] = 50;
val['C'] = 100;
val['D'] = 500;
val['M'] = 1000;
```

Tuy nhiên, cách này cần khởi tạo mảng mỗi lần gọi hàm.

Một cách gọn hơn là dùng `switch`.

---

# 6. Thuật toán từng bước

Giả sử:

```text
s = "MCMXCIV"
```

## Bước 1

Xét `M`:

```text
M = 1000
C = 100
```

Vì:

```text
1000 > 100
```

nên cộng:

```text
ans = 1000
```

## Bước 2

Xét `C`:

```text
C = 100
M = 1000
```

Vì:

```text
100 < 1000
```

nên trừ:

```text
ans = 900
```

## Bước 3

Xét `M`:

```text
M = 1000
X = 10
```

Vì:

```text
1000 > 10
```

nên cộng:

```text
ans = 1900
```

## Bước 4

Xét `X`:

```text
X = 10
C = 100
```

Vì:

```text
10 < 100
```

nên trừ:

```text
ans = 1890
```

## Bước 5

Xét `C`:

```text
C = 100
I = 1
```

Vì:

```text
100 > 1
```

nên cộng:

```text
ans = 1990
```

## Bước 6

Xét `I`:

```text
I = 1
V = 5
```

Vì:

```text
1 < 5
```

nên trừ:

```text
ans = 1989
```

## Bước 7

Xét `V`.

Đây là ký tự cuối cùng nên chắc chắn cộng:

```text
ans = 1994
```

---

# 7. Pseudocode

```text
Khởi tạo ans = 0

Duyệt i từ 0 đến n - 1:

    value = giá trị của s[i]

    Nếu i + 1 < n và value < giá trị của s[i + 1]:
        ans -= value
    Ngược lại:
        ans += value

Trả về ans
```

---

# 8. Lời giải C++ tối ưu

```cpp
class Solution {
private:
    int giaTri(char c) {
        switch (c) {
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
        }

        return 0;
    }

public:
    int romanToInt(string s) {
        int ans = 0;

        for (int i = 0; i < s.size(); i++) {
            int hienTai = giaTri(s[i]);

            if (i + 1 < s.size() && hienTai < giaTri(s[i + 1]))
                ans -= hienTai;
            else
                ans += hienTai;
        }

        return ans;
    }
};
```

---

# 9. Kiểm tra lời giải

## Test case 1

```text
Input:
s = "III"

III
= 1 + 1 + 1
= 3
```

Kết quả:

```text
3
```

---

## Test case 2

```text
Input:
s = "LVIII"
```

Ta có:

```text
L = 50
V = 5
I = 1
I = 1
I = 1
```

Không có ký hiệu nào nhỏ hơn ký hiệu phía sau.

Do đó:

```text
50 + 5 + 1 + 1 + 1 = 58
```

Kết quả:

```text
58
```

---

## Test case 3

```text
Input:
s = "MCMXCIV"
```

Ta có:

```text
M + CM + XC + IV
= 1000 + 900 + 90 + 4
= 1994
```

Kết quả:

```text
1994
```

---

# 10. Độ phức tạp

Gọi:

```text
n = s.size()
```

## Time Complexity

Ta duyệt chuỗi đúng một lần.

Mỗi ký tự chỉ được xử lý một số lần cố định.

Do đó:

```text
O(n)
```

## Space Complexity

Ngoài chuỗi đầu vào, thuật toán chỉ sử dụng một vài biến:

```text
ans
hienTai
i
```

Do đó:

```text
O(1)
```

Đây là độ phức tạp tối ưu vì trong trường hợp tổng quát, ta phải đọc các ký tự của chuỗi để biết kết quả.

---

# 11. Có thể tối ưu code hơn nữa không?

Có.

Ta có thể tránh việc gọi `giaTri()` hai lần trong một vòng lặp bằng cách tính giá trị của ký tự hiện tại và ký tự kế tiếp.

Tuy nhiên, điều đó không tạo ra sự khác biệt về độ phức tạp:

```text
O(n) thời gian
O(1) bộ nhớ
```

Lời giải ở trên đã đạt độ phức tạp tối ưu.

Ngoài ra, `switch` trên chỉ có 7 trường hợp nên rất nhỏ. Không cần sử dụng cấu trúc dữ liệu phức tạp như:

```cpp
map
unordered_map
vector
```

cho bài toán này.

---

# 12. Một cách viết khác: duyệt từ phải sang trái

Một cách tiếp cận rất hay khác là duyệt từ phải sang trái.

Ý tưởng:

- Giữ lại giá trị lớn nhất đã gặp ở bên phải.
- Nếu giá trị hiện tại nhỏ hơn giá trị lớn nhất bên phải → trừ.
- Ngược lại → cộng và cập nhật giá trị lớn nhất.

Ví dụ:

```text
MCMXCIV
```

Duyệt từ phải:

```text
V = 5  -> +5
I = 1  -> -1
C = 100 -> +100
X = 10 -> -10
M = 1000 -> +1000
C = 100 -> -100
M = 1000 -> +1000
```

Tổng:

```text
1994
```

Cách này cũng có:

```text
Time:  O(n)
Space: O(1)
```

Tuy nhiên, với người mới học, cách duyệt từ trái sang phải và so sánh với phần tử kế tiếp thường trực quan hơn.

---

# 13. So sánh hai cách tiếp cận

| Tiêu chí | Trái → phải | Phải → trái |
|---|---|---|
| Ý tưởng | So sánh hiện tại với kế tiếp | So sánh hiện tại với lớn nhất bên phải |
| Time | `O(n)` | `O(n)` |
| Space | `O(1)` | `O(1)` |
| Dễ hiểu | Rất dễ | Cần suy nghĩ thêm |
| Code | Ngắn | Ngắn |
| Có tối ưu không? | Có | Có |

Cả hai đều đạt độ phức tạp tối ưu.

---

# 14. Những cách làm không cần thiết

## 14.1. Liệt kê từng cặp trừ

Có thể viết:

```cpp
if (s[i] == 'I' && s[i + 1] == 'V')
    ...
else if (s[i] == 'I' && s[i + 1] == 'X')
    ...
else if (s[i] == 'X' && s[i + 1] == 'L')
    ...
```

Nhưng cách này không cần thiết.

Ta chỉ cần một quy tắc tổng quát:

```text
current < next
```

thì trừ.

Điều này làm code ngắn hơn và tổng quát hơn.

---

## 14.2. Dùng `map` hoặc `unordered_map`

Ví dụ:

```cpp
unordered_map<char, int> mp;
```

Hoàn toàn có thể sử dụng, nhưng với chỉ 7 ký hiệu thì đây là một cấu trúc dữ liệu hơi dư thừa.

`switch` là đủ đơn giản và rõ ràng.

---

# 15. Insight quan trọng cần nhớ

Bài này là một ví dụ rất điển hình của kỹ thuật:

> **Nhìn vào phần tử kế tiếp để quyết định cách xử lý phần tử hiện tại.**

Quy tắc:

```text
current < next
    ↓
subtract current

current >= next
    ↓
add current
```

Có thể ghi nhớ ngắn gọn:

```text
Nhỏ hơn bên phải → trừ
Ngược lại        → cộng
```

Đây là toàn bộ mấu chốt của bài `Roman to Integer`.

---

# 16. Tổng kết

### Ý tưởng

1. Chuyển mỗi ký hiệu Roman thành giá trị số.
2. Duyệt chuỗi từ trái sang phải.
3. So sánh giá trị hiện tại với giá trị kế tiếp.
4. Nếu hiện tại nhỏ hơn kế tiếp → trừ.
5. Ngược lại → cộng.
6. Trả về tổng.

### Độ phức tạp

```text
Time Complexity:  O(n)
Space Complexity: O(1)
```

### Lời giải C++ cuối cùng

```cpp
class Solution {
private:
    int giaTri(char c) {
        switch (c) {
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
        }

        return 0;
    }

public:
    int romanToInt(string s) {
        int ans = 0;

        for (int i = 0; i < s.size(); i++) {
            int hienTai = giaTri(s[i]);

            if (i + 1 < s.size() && hienTai < giaTri(s[i + 1]))
                ans -= hienTai;
            else
                ans += hienTai;
        }

        return ans;
    }
};
```

Đây là lời giải **tuyến tính `O(n)` và hằng bộ nhớ `O(1)`**, đồng thời không cần lưu trữ thêm cấu trúc dữ liệu phụ.
