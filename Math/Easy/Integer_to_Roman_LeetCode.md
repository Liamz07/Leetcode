# Integer to Roman — Phương hướng tiếp cận và lời giải C++ tối ưu

## 1. Bài toán

Cho một số nguyên `num` trong khoảng:

```text
1 <= num <= 3999
```

Hãy chuyển `num` sang biểu diễn **số La Mã (Roman numeral)**.

Ví dụ:

```text
3    -> "III"
4    -> "IV"
9    -> "IX"
58   -> "LVIII"
1994 -> "MCMXCIV"
```

Bài toán tương ứng với **LeetCode 12 — Integer to Roman**.

---

# 2. Hiểu bản chất của số La Mã

Các ký hiệu Roman cơ bản:

| Giá trị | Ký hiệu |
|--------:|:-------:|
| 1       | I       |
| 5       | V       |
| 10      | X       |
| 50      | L       |
| 100     | C       |
| 500     | D       |
| 1000    | M       |

Ngoài cách cộng thông thường, Roman numeral có một số trường hợp **trừ**:

- `4 = IV`
- `9 = IX`
- `40 = XL`
- `90 = XC`
- `400 = CD`
- `900 = CM`

Điểm quan trọng nhất của bài toán là:

> Không thể chỉ xử lý các ký hiệu `1, 5, 10, 50, 100, 500, 1000`; cần xử lý cả 6 trường hợp subtractive (`IV`, `IX`, `XL`, `XC`, `CD`, `CM`).

---

# 3. Quan sát quan trọng

Ta có thể coi các giá trị sau là những "mệnh giá" Roman:

```text
1000 -> M
900  -> CM
500  -> D
400  -> CD
100  -> C
90   -> XC
50   -> L
40   -> XL
10   -> X
9    -> IX
5    -> V
4    -> IV
1    -> I
```

Danh sách này đã được sắp xếp **giảm dần**.

Nếu luôn lấy giá trị lớn nhất có thể ở thời điểm hiện tại, ta có thể xây dựng đáp án từ trái sang phải.

Ví dụ với:

```text
num = 1994
```

Ta xử lý:

```text
1994 >= 1000
=> thêm "M"
=> num = 994

994 >= 900
=> thêm "CM"
=> num = 94

94 >= 90
=> thêm "XC"
=> num = 4

4 >= 4
=> thêm "IV"
=> num = 0
```

Kết quả:

```text
"M" + "CM" + "XC" + "IV"
= "MCMXCIV"
```

---

# 4. Vì sao Greedy là phương pháp tự nhiên?

Đây là một bài toán rất phù hợp với **Greedy (tham lam)**.

Ý tưởng:

> Ở mỗi bước, chọn ký hiệu có giá trị lớn nhất nhưng không vượt quá phần số còn lại.

Ví dụ:

```text
num = 58
```

Ta có:

```text
50 -> L
8  -> VIII
```

nên:

```text
58 = L + VIII
```

Kết quả:

```text
LVIII
```

Với:

```text
num = 49
```

Nếu chỉ có các giá trị:

```text
50, 10, 5, 1
```

ta sẽ không biểu diễn được `49` theo đúng Roman numeral chuẩn.

Nhưng khi đưa thêm:

```text
40 -> XL
```

ta có:

```text
49 = 40 + 9
   = XL + IX
   = XLIX
```

Vì vậy, việc đưa **6 subtractive cases** vào bảng mệnh giá giúp thuật toán greedy xử lý toàn bộ bài toán rất gọn.

---

# 5. Danh sách giá trị cần sử dụng

Ta sử dụng hai mảng:

```cpp
vector<int> values = {
    1000, 900, 500, 400,
    100, 90, 50, 40,
    10, 9, 5, 4, 1
};

vector<string> symbols = {
    "M", "CM", "D", "CD",
    "C", "XC", "L", "XL",
    "X", "IX", "V", "IV", "I"
};
```

Hai mảng tương ứng theo từng vị trí:

```text
1000 -> M
900  -> CM
500  -> D
400  -> CD
100  -> C
90   -> XC
50   -> L
40   -> XL
10   -> X
9    -> IX
5    -> V
4    -> IV
1    -> I
```

---

# 6. Thuật toán Greedy

Giả sử:

```text
num = N
```

Duyệt các mệnh giá theo thứ tự giảm dần.

Tại mỗi vị trí `i`:

1. Nếu `num >= values[i]`:
   - Thêm `symbols[i]` vào kết quả.
   - Trừ `values[i]` khỏi `num`.
2. Tiếp tục cho đến khi `num == 0`.

Pseudo-code:

```text
result = ""

for mỗi i từ 0 đến hết:
    while num >= values[i]:
        result += symbols[i]
        num -= values[i]

return result
```

---

# 7. Tại sao phải dùng `while`?

Ví dụ:

```text
num = 3000
```

Ta có:

```text
1000 -> M
1000 -> M
1000 -> M
```

Kết quả:

```text
MMM
```

Do đó với một mệnh giá, có thể phải sử dụng nó nhiều lần.

Vì vậy:

```cpp
while (num >= values[i])
```

là cách tổng quát.

Tuy nhiên, do đề bài giới hạn `num <= 3999`, số lần lặp thực tế rất nhỏ.

---

# 8. Ví dụ chi tiết: `num = 1994`

Ban đầu:

```text
num = 1994
result = ""
```

### Bước 1

Xét:

```text
1000 -> M
```

Vì:

```text
1994 >= 1000
```

nên:

```text
result = "M"
num = 994
```

---

### Bước 2

Xét:

```text
900 -> CM
```

Ta có:

```text
994 >= 900
```

nên:

```text
result = "MCM"
num = 94
```

---

### Bước 3

Xét:

```text
500 -> D
400 -> CD
100 -> C
```

Không có giá trị nào phù hợp.

Tiếp theo:

```text
90 -> XC
```

Ta có:

```text
94 >= 90
```

nên:

```text
result = "MCMXC"
num = 4
```

---

### Bước 4

Xét:

```text
40 -> XL
10 -> X
9  -> IX
5  -> V
4  -> IV
```

Ta có:

```text
4 >= 4
```

nên:

```text
result = "MCMXCIV"
num = 0
```

Kết thúc.

Đáp án:

```text
MCMXCIV
```

---

# 9. Chứng minh tính đúng đắn

Ta cần chứng minh thuật toán greedy tạo ra Roman numeral đúng.

## Bổ đề 1: Mỗi token trong bảng đều là một biểu diễn Roman hợp lệ

Các token:

```text
M
CM
D
CD
C
XC
L
XL
X
IX
V
IV
I
```

đều là những cấu trúc Roman hợp lệ.

Các trường hợp đặc biệt:

```text
IV = 4
IX = 9
XL = 40
XC = 90
CD = 400
CM = 900
```

Do đó mỗi lần thuật toán thêm một token, phần kết quả mới vẫn là một tiền tố hợp lệ của biểu diễn Roman.

---

## Bổ đề 2: Luôn chọn token lớn nhất có thể không làm mất nghiệm

Các giá trị được sắp xếp giảm dần:

```text
1000 > 900 > 500 > 400 > 100 > 90 > ...
```

Roman numeral chuẩn biểu diễn một số bằng cách xử lý theo các nhóm hàng nghìn, hàng trăm, hàng chục và hàng đơn vị.

Các token subtractive đã được đưa vào đúng vị trí:

```text
900 trước 500
400 trước 100
90  trước 50
40  trước 10
9   trước 5
4   trước 1
```

Vì vậy khi một token như `900` có thể được sử dụng, biểu diễn chuẩn phải sử dụng `CM` thay vì tách thành các token nhỏ hơn.

Tương tự:

```text
4  -> IV
9  -> IX
40 -> XL
90 -> XC
400 -> CD
900 -> CM
```

Do đó chọn token lớn nhất có thể chính là tuân theo quy tắc biểu diễn chuẩn của Roman numeral.

---

## Bổ đề 3: Thuật toán luôn kết thúc

Mỗi lần thực hiện:

```cpp
num -= values[i];
```

thì `num` giảm một lượng dương.

Vì:

```text
num >= 1
```

ban đầu và `num` bị giảm dần, cuối cùng:

```text
num = 0
```

Do đó thuật toán luôn kết thúc.

---

## Kết luận

Sau khi thuật toán kết thúc:

```text
num = 0
```

toàn bộ giá trị ban đầu đã được chuyển thành các Roman token hợp lệ.

Vì các token được xét theo thứ tự giảm dần và bao gồm đầy đủ 6 subtractive cases, kết quả chính là biểu diễn Roman chuẩn của số đầu vào.

---

# 10. Độ phức tạp

Có tổng cộng chỉ **13 token**:

```text
1000, 900, 500, 400,
100, 90, 50, 40,
10, 9, 5, 4, 1
```

Do đó số vòng lặp là rất nhỏ.

Nếu gọi `k` là số lượng token trong bảng thì:

```text
k = 13
```

Và với mỗi token, số lần thêm vào kết quả bị giới hạn.

Về mặt Big-O, có thể xem:

```text
Time:  O(k + |answer|)
Space: O(|answer|)
```

Với bài toán này:

```text
k = 13
```

và:

```text
num <= 3999
```

nên trên thực tế đây là một thuật toán **O(1)** theo giới hạn của đề bài.

Phần `O(|answer|)` là cần thiết vì chúng ta phải tạo ra chuỗi kết quả.

---

# 11. Có cách nào còn đơn giản hơn không?

Có.

Vì `num <= 3999`, ta có thể xử lý độc lập từng chữ số:

```text
hàng nghìn
hàng trăm
hàng chục
hàng đơn vị
```

Ví dụ:

```text
1994
```

tách thành:

```text
1 | 9 | 9 | 4
```

Sau đó:

```text
1 nghìn -> M
9 trăm  -> CM
9 chục  -> XC
4 đơn vị -> IV
```

Kết quả:

```text
MCMXCIV
```

Cách này cũng rất hiệu quả và có thể viết bằng bảng tra cứu.

Ví dụ:

```cpp
thousands = {"", "M", "MM", "MMM"};

hundreds = {
    "", "C", "CC", "CCC", "CD",
    "D", "DC", "DCC", "DCCC", "CM"
};

tens = {
    "", "X", "XX", "XXX", "XL",
    "L", "LX", "LXX", "LXXX", "XC"
};

ones = {
    "", "I", "II", "III", "IV",
    "V", "VI", "VII", "VIII", "IX"
};
```

Sau đó:

```cpp
return thousands[num / 1000]
     + hundreds[(num / 100) % 10]
     + tens[(num / 10) % 10]
     + ones[num % 10];
```

Cách này có ưu điểm là rất ngắn và thực sự chạy theo số lượng chữ số cố định.

Tuy nhiên, cách **greedy với 13 mệnh giá** thường dễ tổng quát hóa và thể hiện tư duy thuật toán rõ ràng hơn.

---

# 12. So sánh hai hướng tiếp cận

| Tiêu chí | Greedy 13 token | Lookup theo từng chữ số |
|---|---|---|
| Ý tưởng | Tham lam | Tra bảng |
| Code | Ngắn | Rất ngắn |
| Dễ hiểu | Rất dễ | Dễ |
| Tính tổng quát | Tốt hơn | Phụ thuộc giới hạn 3999 |
| Thể hiện tư duy thuật toán | Rõ | Ít hơn |
| Complexity | O(1) theo constraint | O(1) |
| Cài đặt | `while` + bảng | 4 bảng |

Nếu đang học thuật toán hoặc phỏng vấn, nên hiểu **greedy** trước.

---

# 13. Lời giải C++ tối ưu — Greedy

```cpp
class Solution {
public:
    string intToRoman(int num) {
        static const vector<pair<int, string>> roman = {
            {1000, "M"},
            {900,  "CM"},
            {500,  "D"},
            {400,  "CD"},
            {100,  "C"},
            {90,   "XC"},
            {50,   "L"},
            {40,   "XL"},
            {10,   "X"},
            {9,    "IX"},
            {5,    "V"},
            {4,    "IV"},
            {1,    "I"}
        };

        string ans;

        for (const auto& [value, symbol] : roman) {
            while (num >= value) {
                ans += symbol;
                num -= value;
            }
        }

        return ans;
    }
};
```

---

# 14. Giải thích từng phần của code

## `static const`

```cpp
static const vector<pair<int, string>> roman = {
    ...
};
```

Bảng Roman không thay đổi trong quá trình chạy.

`const` thể hiện rằng dữ liệu không bị thay đổi.

`static` giúp bảng không cần được khởi tạo lại theo cách thông thường mỗi lần gọi hàm.

Trong LeetCode, điều này không bắt buộc; viết đơn giản như sau cũng hoàn toàn ổn:

```cpp
const vector<pair<int, string>> roman = {
    ...
};
```

---

## `pair<int, string>`

Mỗi phần tử lưu:

```text
giá trị -> ký hiệu
```

Ví dụ:

```cpp
{900, "CM"}
```

có nghĩa:

```text
900 -> CM
```

---

## Structured binding

```cpp
for (const auto& [value, symbol] : roman)
```

Tương đương với việc lấy:

```cpp
value
symbol
```

từ từng `pair`.

Ví dụ:

```cpp
{900, "CM"}
```

sẽ được tách thành:

```text
value  = 900
symbol = "CM"
```

---

## Vòng `while`

```cpp
while (num >= value) {
    ans += symbol;
    num -= value;
}
```

Nếu còn đủ giá trị để dùng Roman token hiện tại thì:

1. Thêm token.
2. Trừ giá trị tương ứng.

Ví dụ:

```text
num = 3000
value = 1000
symbol = "M"
```

ta thực hiện 3 lần:

```text
ans = "M"
num = 2000

ans = "MM"
num = 1000

ans = "MMM"
num = 0
```

---

# 15. Dry run với một số test

## Test 1

```text
num = 3
```

Xét `1 -> I`:

```text
3 >= 1
```

Thêm:

```text
I
```

tiếp tục:

```text
I
II
III
```

Kết quả:

```text
III
```

---

## Test 2

```text
num = 4
```

Khi tới:

```text
4 -> IV
```

ta có:

```text
ans = "IV"
num = 0
```

Kết quả:

```text
IV
```

---

## Test 3

```text
num = 58
```

Ta chọn:

```text
50 -> L
```

còn:

```text
8
```

sau đó:

```text
5 -> V
```

còn:

```text
3
```

cuối cùng:

```text
1 -> I
1 -> I
1 -> I
```

Kết quả:

```text
LVIII
```

---

## Test 4

```text
num = 1994
```

Ta có:

```text
1000 -> M
900  -> CM
90   -> XC
4    -> IV
```

Kết quả:

```text
MCMXCIV
```

---

## Test 5

```text
num = 3999
```

Ta có:

```text
3000 -> MMM
900  -> CM
90   -> XC
9    -> IX
```

Kết quả:

```text
MMMCMXCIX
```

---

# 16. Những lỗi thường gặp

## Lỗi 1: Chỉ dùng 7 ký hiệu cơ bản

Sai:

```cpp
1000 -> M
500  -> D
100  -> C
50   -> L
10   -> X
5    -> V
1    -> I
```

Cách này không xử lý đúng:

```text
4
9
40
90
400
900
```

Ví dụ:

```text
4
```

không thể trả về:

```text
IIII
```

vì Roman numeral chuẩn dùng:

```text
IV
```

---

## Lỗi 2: Đặt thứ tự không giảm dần

Nếu bảng được viết sai thứ tự, ví dụ:

```text
1000
500
900
...
```

thì khi gặp `1994`, thuật toán có thể chọn:

```text
M
D
...
```

trước khi xét `900`.

Điều này phá vỡ greedy.

Do đó bảng phải được sắp xếp:

```text
1000
900
500
400
100
90
50
40
10
9
5
4
1
```

---

## Lỗi 3: Dùng `if` thay vì `while`

Ví dụ:

```cpp
if (num >= value) {
    ans += symbol;
    num -= value;
}
```

Cách này chỉ sử dụng mỗi mệnh giá tối đa một lần.

Với:

```text
3000
```

nó chỉ thêm:

```text
M
```

trong khi đáp án cần:

```text
MMM
```

Vì vậy phải dùng:

```cpp
while
```

hoặc một cách tính số lần sử dụng.

---

# 17. Có thể tối ưu thêm bằng cách tính số lần sử dụng

Thay vì:

```cpp
while (num >= value) {
    ans += symbol;
    num -= value;
}
```

ta có thể tính trực tiếp:

```cpp
int count = num / value;
```

sau đó thêm `symbol` `count` lần.

Ví dụ:

```text
num = 3000
value = 1000

count = 3000 / 1000 = 3
```

nên thêm:

```text
MMM
```

Code:

```cpp
class Solution {
public:
    string intToRoman(int num) {
        static const vector<pair<int, string>> roman = {
            {1000, "M"},
            {900,  "CM"},
            {500,  "D"},
            {400,  "CD"},
            {100,  "C"},
            {90,   "XC"},
            {50,   "L"},
            {40,   "XL"},
            {10,   "X"},
            {9,    "IX"},
            {5,    "V"},
            {4,    "IV"},
            {1,    "I"}
        };

        string ans;

        for (const auto& [value, symbol] : roman) {
            int count = num / value;

            for (int i = 0; i < count; ++i) {
                ans += symbol;
            }

            num %= value;
        }

        return ans;
    }
};
```

Tuy nhiên, với constraint của bài, sự khác biệt thực tế không đáng kể.

---

# 18. Có nên dùng `unordered_map` không?

Không nên.

Một số bạn có thể nghĩ đến:

```cpp
unordered_map<int, string>
```

nhưng bài toán này cần duyệt các giá trị theo **thứ tự giảm dần**.

`unordered_map` không cung cấp thứ tự phù hợp.

Ta vẫn cần thêm logic để sắp xếp hoặc tạo một danh sách thứ tự.

Do đó:

```cpp
vector<pair<int, string>>
```

phù hợp hơn.

---

# 19. Có nên dùng đệ quy không?

Không cần.

Bài toán có cấu trúc tuần tự rất rõ:

```text
giá trị lớn -> giá trị nhỏ
```

Dùng vòng lặp:

```cpp
for
```

đơn giản hơn, dễ đọc hơn và không có overhead của recursion.

---

# 20. Phiên bản ngắn gọn để đi phỏng vấn

Nếu cần code nhanh trong interview:

```cpp
class Solution {
public:
    string intToRoman(int num) {
        vector<int> val = {
            1000, 900, 500, 400,
            100, 90, 50, 40,
            10, 9, 5, 4, 1
        };

        vector<string> sym = {
            "M", "CM", "D", "CD",
            "C", "XC", "L", "XL",
            "X", "IX", "V", "IV", "I"
        };

        string ans;

        for (int i = 0; i < val.size(); ++i) {
            while (num >= val[i]) {
                ans += sym[i];
                num -= val[i];
            }
        }

        return ans;
    }
};
```

Đây là phiên bản dễ nhớ nhất:

```text
values giảm dần
+
symbols tương ứng
+
while
```

---

# 21. Template tư duy có thể áp dụng cho các bài khác

Bài này đáng học không chỉ vì Roman numeral mà vì nó minh họa một pattern quan trọng:

## Pattern: Greedy + Ordered Choices

Khi gặp bài toán có:

- một tập lựa chọn có giá trị,
- cần biểu diễn một tổng,
- lựa chọn lớn hơn nên được ưu tiên,
- và có quy tắc đặc biệt cho một số trường hợp,

hãy thử:

```text
1. Liệt kê các lựa chọn.
2. Xử lý theo thứ tự ưu tiên.
3. Chọn lựa chọn lớn nhất có thể.
4. Giảm phần còn lại.
5. Lặp lại cho đến khi hoàn thành.
```

Trong bài này:

```text
Choices:
1000, 900, 500, 400, ...

Priority:
giảm dần

Action:
thêm Roman symbol

State update:
num -= value
```

---

# 22. Kết luận

Cách tiếp cận nên ghi nhớ:

```text
Integer -> Roman
        ↓
Xây dựng 13 token hợp lệ
        ↓
Sắp xếp theo giá trị giảm dần
        ↓
Greedy: lấy token lớn nhất có thể
        ↓
Trừ giá trị token khỏi num
        ↓
Lặp lại đến num = 0
```

13 token cần nhớ:

```text
1000 -> M
900  -> CM
500  -> D
400  -> CD
100  -> C
90   -> XC
50   -> L
40   -> XL
10   -> X
9    -> IX
5    -> V
4    -> IV
1    -> I
```

Lời giải cốt lõi:

```cpp
for (const auto& [value, symbol] : roman) {
    while (num >= value) {
        ans += symbol;
        num -= value;
    }
}
```

Đây là lời giải có cấu trúc đơn giản, thời gian chạy thực tế là hằng số do miền giá trị của bài toán bị giới hạn (`1..3999`), và đặc biệt dễ mở rộng hoặc giải thích trong phỏng vấn.
