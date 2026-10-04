# Maximum Repeating Substring - LeetCode

## 1. Phát biểu bài toán

Cho hai chuỗi `sequence` và `word`.

Hãy tìm số nguyên `k` lớn nhất sao cho chuỗi `word` được lặp lại `k` lần
liên tiếp vẫn là một substring của `sequence`.

Nếu `word` không xuất hiện trong `sequence`, kết quả là `0`.

Ví dụ:

``` text
sequence = "ababc"
word = "ab"
```

Ta có:

``` text
"ab"   -> có
"abab" -> có
"ababab" -> không
```

Vậy đáp án là `2`.

------------------------------------------------------------------------

## 2. Điều quan trọng nhất cần hiểu

Bài này không hỏi:

> `word` xuất hiện trong `sequence` bao nhiêu lần?

Mà hỏi:

> `word` có thể xuất hiện liên tiếp nhau bao nhiêu lần?

Ví dụ:

``` text
sequence = "ababxabab"
word = "ab"
```

`"ab"` xuất hiện nhiều lần, nhưng số lần lặp liên tiếp lớn nhất chỉ là:

``` text
"abab" = "ab" + "ab"
```

nên đáp án là `2`.

------------------------------------------------------------------------

## 3. Quan sát chính

Nếu `word` lặp `k` lần liên tiếp thì chuỗi cần kiểm tra là:

``` text
word * k
```

Ví dụ:

``` text
word = "ab"

k = 1 -> "ab"
k = 2 -> "abab"
k = 3 -> "ababab"
k = 4 -> "abababab"
```

Ta chỉ cần tìm `k` lớn nhất sao cho chuỗi tương ứng là substring của
`sequence`.

------------------------------------------------------------------------

## 4. Phương hướng tiếp cận trực tiếp

Ta bắt đầu với:

``` text
k = 0
```

Sau đó liên tục thử thêm một `word`.

Quy trình:

1.  Thử `word`.
2.  Nếu xuất hiện trong `sequence`, tăng kết quả lên `1`.
3.  Thử `word + word`.
4.  Nếu vẫn xuất hiện, tăng kết quả lên `2`.
5.  Tiếp tục như vậy.
6.  Khi lần lặp tiếp theo không còn xuất hiện, dừng.

Ví dụ:

``` text
sequence = "abababc"
word = "ab"
```

Ta kiểm tra:

``` text
"ab"       -> có
"abab"     -> có
"ababab"   -> có
"abababab" -> không
```

Kết quả là `3`.

------------------------------------------------------------------------

## 5. Tại sao có thể dừng ngay khi thất bại?

Giả sử:

``` text
word * k
```

không phải substring của `sequence`.

Ta không cần kiểm tra:

``` text
word * (k + 1)
word * (k + 2)
...
```

Bởi vì nếu:

``` text
word * (k + 1)
```

xuất hiện thì phần đầu của nó chắc chắn phải chứa:

``` text
word * k
```

Nhưng `word * k` đã không xuất hiện.

Vậy các trường hợp lớn hơn cũng không thể xuất hiện.

Do đó, lần đầu tiên thất bại chính là thời điểm có thể dừng.

------------------------------------------------------------------------

## 6. Sử dụng string::find()

C++ có hàm:

``` cpp
sequence.find(chuoi)
```

để tìm `chuoi` bên trong `sequence`.

Nếu tìm thấy, hàm trả về vị trí xuất hiện.

Nếu không tìm thấy, nó trả về:

``` cpp
string::npos
```

Ví dụ:

``` cpp
if (sequence.find("abab") != string::npos) {
    // "abab" xuất hiện
}
```

Ta có thể tận dụng trực tiếp hàm này.

------------------------------------------------------------------------

## 7. Xây dựng chuỗi lặp

Ta dùng:

``` cpp
string lap = "";
```

để lưu chuỗi hiện tại.

Mỗi lần có thể lặp thêm:

``` cpp
lap += word;
```

Ví dụ:

``` text
Ban đầu:
lap = ""

Lần 1:
lap = "ab"

Lần 2:
lap = "abab"

Lần 3:
lap = "ababab"
```

Đồng thời:

``` text
ans = 0
1
2
3
```

------------------------------------------------------------------------

## 8. Công thức kiểm tra

Ở mỗi vòng lặp, thay vì kiểm tra `lap` hiện tại, ta thử:

``` cpp
lap + word
```

Nếu:

``` cpp
sequence.find(lap + word) != string::npos
```

thì có thể thêm một lần `word`.

Sau đó:

``` cpp
lap += word;
ans++;
```

Điều này giúp ta kiểm tra trực tiếp "lần lặp tiếp theo có còn hợp lệ
không".

------------------------------------------------------------------------

## 9. Chạy thử từng bước

Xét:

``` text
sequence = "abababc"
word = "ab"
```

Ban đầu:

``` text
lap = ""
ans = 0
```

### Lần 1

Kiểm tra:

``` text
lap + word
= "" + "ab"
= "ab"
```

Có trong `sequence`.

Cập nhật:

``` text
lap = "ab"
ans = 1
```

### Lần 2

Kiểm tra:

``` text
"ab" + "ab"
= "abab"
```

Có.

Cập nhật:

``` text
lap = "abab"
ans = 2
```

### Lần 3

Kiểm tra:

``` text
"abab" + "ab"
= "ababab"
```

Có.

Cập nhật:

``` text
lap = "ababab"
ans = 3
```

### Lần 4

Kiểm tra:

``` text
"ababab" + "ab"
= "abababab"
```

Không có.

Dừng.

Kết quả:

``` text
ans = 3
```

------------------------------------------------------------------------

## 10. Chứng minh tính đúng đắn

Giả sử thuật toán đã tìm được:

``` text
word
word + word
...
word * k
```

đều là substring của `sequence`.

Vậy `k` là một số lần lặp hợp lệ.

Thuật toán tiếp tục kiểm tra:

``` text
word * (k + 1)
```

Nếu chuỗi này xuất hiện, `k` chưa phải đáp án và thuật toán tăng lên.

Nếu chuỗi này không xuất hiện, không thể có một số lần lặp lớn hơn `k`.

Bởi vì nếu `word * (k + 2)` xuất hiện thì phần đầu của nó chắc chắn chứa
`word * (k + 1)`, mâu thuẫn với việc `word * (k + 1)` không xuất hiện.

Vì vậy khi thuật toán dừng, `k` chính là số lần lặp lớn nhất.

------------------------------------------------------------------------

## 11. Tại sao không chỉ dùng find(word)?

Ví dụ:

``` text
sequence = "abxab"
word = "ab"
```

`word` có xuất hiện.

Nhưng:

``` text
"abab"
```

không xuất hiện.

Do đó đáp án là:

``` text
1
```

Chứ không phải số lần `"ab"` xuất hiện riêng lẻ.

Bài toán yêu cầu **liên tiếp**.

------------------------------------------------------------------------

## 12. Tại sao không cần Dynamic Programming?

Không phải cứ bài toán hỏi giá trị lớn nhất là phải dùng DP.

Ở đây ta không có nhiều trạng thái con phức tạp.

Ta chỉ cần thử tuần tự:

``` text
word
word * 2
word * 3
...
```

Mỗi bước chỉ mở rộng kết quả của bước trước.

Không cần lưu một bảng trạng thái.

Vì vậy dùng DP sẽ làm bài toán phức tạp hơn cần thiết.

------------------------------------------------------------------------

## 13. Tại sao không cần Greedy?

Bài này cũng không có lựa chọn giữa nhiều loại vật phẩm hay nhiều chiến
lược.

Chỉ có một chuỗi cố định:

``` text
word
```

Ta chỉ hỏi có thể nối nó với chính nó bao nhiêu lần.

Do đó việc tăng dần:

``` text
1 -> 2 -> 3 -> ...
```

là cách tự nhiên nhất.

------------------------------------------------------------------------

## 14. Tại sao không cần KMP?

Có thể dùng KMP, Z Algorithm hoặc Rabin-Karp để tìm substring.

Tuy nhiên, với giới hạn của bài toán, điều đó không cần thiết.

C++ đã có:

``` cpp
string::find()
```

và nó giúp code:

-   ngắn;
-   dễ đọc;
-   dễ chứng minh;
-   đủ nhanh.

Không nên dùng thuật toán phức tạp hơn khi giới hạn bài không yêu cầu.

------------------------------------------------------------------------

## 15. Giới hạn số lần lặp

Nếu:

``` text
N = sequence.size()
M = word.size()
```

thì số lần lặp không thể lớn hơn:

``` text
N / M
```

vì:

``` text
k * M <= N
```

Do đó ta có thể biết trước giới hạn tối đa của `k`.

Tuy nhiên không bắt buộc phải viết giới hạn này trong code, vì điều kiện
`find()` sẽ tự làm vòng lặp dừng.

------------------------------------------------------------------------

## 16. Trường hợp word không xuất hiện

Ví dụ:

``` text
sequence = "abcdef"
word = "xy"
```

Kiểm tra:

``` text
"xy"
```

không xuất hiện.

Vòng lặp không chạy lần nào.

Kết quả:

``` text
0
```

------------------------------------------------------------------------

## 17. Trường hợp word xuất hiện đúng một lần

``` text
sequence = "abcde"
word = "abc"
```

Ta có:

``` text
"abc" -> có
"abcabc" -> không
```

Kết quả:

``` text
1
```

------------------------------------------------------------------------

## 18. Trường hợp sequence gồm hoàn toàn các word nối tiếp

``` text
sequence = "abababab"
word = "ab"
```

Ta có:

``` text
"ab"       -> có
"abab"     -> có
"ababab"   -> có
"abababab" -> có
"ababababab" -> không
```

Vậy:

``` text
ans = 4
```

------------------------------------------------------------------------

## 19. Độ phức tạp

Gọi:

``` text
N = sequence.size()
M = word.size()
```

Số lần lặp tối đa không vượt quá:

``` text
N / M
```

Ở mỗi lần ta thực hiện tìm kiếm substring bằng:

``` cpp
sequence.find(...)
```

Độ phức tạp cụ thể của `find()` phụ thuộc vào cách triển khai và dữ liệu
đầu vào, nhưng với giới hạn của bài LeetCode thì cách làm này hoàn toàn
phù hợp.

Bộ nhớ:

``` text
O(N)
```

vì chuỗi `lap` có thể dài tới kích thước của `sequence`.

------------------------------------------------------------------------

## 20. Lời giải C++ tối ưu, ngắn gọn và dễ hiểu

``` cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxRepeating(string sequence, string word) {
        string lap = "";
        int ans = 0;

        while (sequence.find(lap + word) != string::npos) {
            lap += word;
            ans++;
        }

        return ans;
    }
};
```

------------------------------------------------------------------------

## 21. Phân tích từng dòng code

### Khai báo chuỗi lặp

``` cpp
string lap = "";
```

`lap` lưu chuỗi `word` được lặp lại nhiều lần.

Ví dụ:

``` text
"ab"
"abab"
"ababab"
...
```

### Biến kết quả

``` cpp
int ans = 0;
```

Ban đầu chưa tìm được lần lặp nào.

### Điều kiện vòng lặp

``` cpp
while (sequence.find(lap + word) != string::npos)
```

Đây là dòng quan trọng nhất.

Ta thử thêm một `word` vào chuỗi hiện tại.

Nếu chuỗi mới vẫn là substring của `sequence`, ta tiếp tục.

### Nối thêm word

``` cpp
lap += word;
```

Sau khi kiểm tra thành công, chính thức thêm một lần `word`.

### Tăng đáp án

``` cpp
ans++;
```

Ta vừa tìm được thêm một lần lặp hợp lệ.

### Trả kết quả

``` cpp
return ans;
```

Khi vòng lặp dừng, `ans` chính là số lần lặp liên tiếp lớn nhất.

------------------------------------------------------------------------

## 22. Một phiên bản khác có kiểm tra độ dài

Có thể viết:

``` cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxRepeating(string sequence, string word) {
        string lap = "";
        int ans = 0;

        while (lap.size() + word.size() <= sequence.size()) {
            lap += word;

            if (sequence.find(lap) == string::npos) {
                break;
            }

            ans++;
        }

        return ans;
    }
};
```

Phiên bản này chủ động kiểm tra:

``` text
lap + word
```

có vượt quá độ dài `sequence` hay không.

Tuy nhiên phiên bản:

``` cpp
while (sequence.find(lap + word) != string::npos)
```

ngắn hơn và trực tiếp hơn.

------------------------------------------------------------------------

## 23. Nên chọn phiên bản nào?

Với bài này, nên ưu tiên:

``` cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxRepeating(string sequence, string word) {
        string lap = "";
        int ans = 0;

        while (sequence.find(lap + word) != string::npos) {
            lap += word;
            ans++;
        }

        return ans;
    }
};
```

Vì:

-   Code ngắn.
-   Ý tưởng bám sát đề bài.
-   Không cần DP.
-   Không cần mảng.
-   Không cần KMP.
-   Dễ đọc.
-   Dễ chứng minh.
-   Đủ nhanh với giới hạn của LeetCode.

------------------------------------------------------------------------

## 24. Luồng xử lý tổng quát

``` text
Bắt đầu
   |
   v
lap = ""
ans = 0
   |
   v
Thử lap + word
   |
   +---- Không xuất hiện ----> Dừng
   |
   v
Xuất hiện
   |
   v
lap += word
ans++
   |
   v
Quay lại kiểm tra
   |
   v
Trả về ans
```

Điểm mấu chốt:

> Chỉ tăng `ans` khi toàn bộ chuỗi `word` lặp thêm một lần vẫn nằm trong
> `sequence`.

------------------------------------------------------------------------

## 25. Kết luận

Bài Maximum Repeating Substring có bản chất là tìm số `k` lớn nhất sao
cho:

``` text
word được nối với chính nó k lần
```

vẫn là substring của:

``` text
sequence
```

Cách giải đơn giản nhất là xây dựng dần:

``` text
word
word + word
word + word + word
...
```

và dùng:

``` cpp
sequence.find(...)
```

để kiểm tra.

Tư duy quan trọng nhất:

> Nếu có thể thêm một `word` nữa mà toàn bộ chuỗi mới vẫn xuất hiện
> trong `sequence`, ta tăng số lần lặp. Ngay khi lần tiếp theo không
> xuất hiện, đáp án hiện tại chính là lớn nhất.

Đây là lời giải trực tiếp, ngắn gọn và phù hợp với giới hạn của bài
toán.
