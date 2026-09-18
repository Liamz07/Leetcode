# Longest Common Prefix — LeetCode

## 1. Mô tả bài toán

Cho một mảng các chuỗi `strs`, hãy tìm **Longest Common Prefix (LCP)** — tiền tố chung dài nhất của tất cả các chuỗi.

Một **prefix (tiền tố)** là phần ký tự nằm ở đầu chuỗi.

Ví dụ:

```text
strs = ["flower", "flow", "flight"]
```

Các chuỗi đều bắt đầu bằng:

```text
"f"
"fl"
```

nhưng không cùng có ký tự thứ 3:

```text
flower -> o
flow   -> o
flight -> i
```

Vì vậy kết quả là:

```text
"fl"
```

### Ví dụ khác

```text
["dog", "racecar", "car"]
```

Không có ký tự đầu tiên nào giống nhau, nên kết quả là:

```text
""
```

---

# 2. Phương hướng tiếp cận

## Ý tưởng chính: dùng một chuỗi làm "mốc"

Ta chọn chuỗi đầu tiên:

```cpp
string moc = strs[0];
```

làm **chuỗi mốc**.

Ban đầu, ta giả sử toàn bộ chuỗi mốc là tiền tố chung:

```text
moc = "flower"
dem = 6
```

Sau đó lần lượt so sánh `moc` với từng chuỗi còn lại.

Điểm quan trọng là:

> `dem` luôn biểu diễn độ dài tiền tố chung của tất cả các chuỗi đã xét cho tới thời điểm hiện tại.

Ví dụ:

```text
["flower", "flow", "flight"]
```

### Bước 1: khởi tạo

```text
moc = "flower"
dem = 6
```

Ta đang tạm giả sử:

```text
"flower"
```

là tiền tố chung.

### Bước 2: so sánh với `"flow"`

So sánh từng ký tự:

```text
flower
flow
^^^^
```

Các ký tự giống nhau:

```text
f l o w
```

nên:

```text
dem = 4
```

Lúc này tiền tố chung của các chuỗi đã xét là:

```text
"flow"
```

### Bước 3: so sánh với `"flight"`

Ta chỉ cần kiểm tra tối đa `dem = 4` ký tự:

```text
flower
flight
^^
```

- `f == f`
- `l == l`
- `o != i`

Vì vậy:

```text
dem = 2
```

Kết quả cuối cùng:

```text
moc.substr(0, dem)
```

tức:

```text
"fl"
```

---

# 3. Vì sao chỉ cần dùng chuỗi đầu tiên làm mốc?

Giả sử:

```text
strs = ["abcdef", "abc", "abcd", "ab"]
```

Nếu tất cả các chuỗi có một tiền tố chung, tiền tố đó **chắc chắn phải là tiền tố của `strs[0]`**.

Do đó ta có thể lấy:

```cpp
string moc = strs[0];
```

và chỉ cần tìm xem có bao nhiêu ký tự đầu tiên của `moc` còn xuất hiện giống hệt trong tất cả các chuỗi khác.

Ta không cần tạo ra một chuỗi tiền tố mới sau mỗi lần so sánh.

Thay vào đó, chỉ cần lưu **độ dài tiền tố chung hiện tại** bằng biến:

```cpp
int dem;
```

Đây là điểm giúp lời giải đơn giản và tiết kiệm bộ nhớ.

---

# 4. Phân tích vòng lặp

Phần quan trọng nhất của lời giải là:

```cpp
for (int i = 1; i < strs.size(); i++) {
    int j = 0;

    while (j < dem && j < strs[i].length() && moc[j] == strs[i][j]) {
        j++;
    }

    dem = j;

    if (dem == 0) break;
}
```

Ta phân tích từng phần.

## 4.1. Duyệt từ chuỗi thứ hai

```cpp
for (int i = 1; i < strs.size(); i++)
```

Chuỗi đầu tiên đã được chọn làm `moc`, nên không cần so sánh nó với chính nó.

Do đó bắt đầu từ:

```cpp
i = 1
```

---

## 4.2. Biến `j`

```cpp
int j = 0;
```

`j` là vị trí ký tự đang được so sánh.

Ví dụ:

```text
moc    = "flower"
strs[i] = "flow"
```

thì:

```text
j = 0 -> so sánh f với f
j = 1 -> so sánh l với l
j = 2 -> so sánh o với o
j = 3 -> so sánh w với w
```

Sau đó `j = 4` và vòng `while` kết thúc.

---

# 5. Vì sao điều kiện `j < dem` rất quan trọng?

Ta có:

```cpp
while (j < dem && j < strs[i].length() && moc[j] == strs[i][j])
```

Trong đó:

```cpp
j < dem
```

có ý nghĩa rất quan trọng.

Giả sử sau khi so sánh các chuỗi trước đó, ta đã biết:

```text
dem = 3
```

Điều đó có nghĩa là tiền tố chung hiện tại chỉ dài 3 ký tự.

Ví dụ:

```text
["abcdef", "abcxyz", "abcpqr"]
```

Sau hai chuỗi đầu:

```text
dem = 3
```

Khi xét chuỗi tiếp theo, chúng ta **không cần kiểm tra ký tự thứ 4 trở đi**.

Bởi vì chúng ta đã biết tiền tố chung của các chuỗi trước chỉ dài 3.

Do đó:

```cpp
j < dem
```

giúp giới hạn số ký tự cần kiểm tra.

Đây cũng là lý do biến `dem` không chỉ là biến dùng để trả về kết quả, mà còn giúp giảm số phép so sánh.

---

# 6. Vì sao cần `j < strs[i].length()`?

Xét:

```text
["flower", "flow"]
```

Sau khi so sánh:

```text
flower
flow
^^^^
```

ta có:

```text
j = 4
```

Nhưng `"flow"` chỉ có 4 ký tự.

Nếu tiếp tục truy cập:

```cpp
strs[i][4]
```

thì ta đang truy cập vượt quá phạm vi hợp lệ của chuỗi `"flow"`.

Vì vậy cần:

```cpp
j < strs[i].length()
```

để đảm bảo `j` vẫn là vị trí hợp lệ trong chuỗi hiện tại.

---

# 7. Điều kiện so sánh ký tự

Phần:

```cpp
moc[j] == strs[i][j]
```

kiểm tra hai ký tự ở cùng vị trí có giống nhau hay không.

Ví dụ:

```text
moc     = "flower"
strs[i] = "flight"
```

Ta có:

```text
j = 0:
f == f -> đúng

j = 1:
l == l -> đúng

j = 2:
o != i -> sai
```

Khi gặp ký tự khác nhau, vòng `while` dừng.

Lúc đó:

```cpp
j = 2;
```

nên:

```cpp
dem = j;
```

tức:

```text
dem = 2
```

---

# 8. Vì sao `dem = j`?

Sau vòng `while`, có hai khả năng chính.

### Trường hợp 1: gặp ký tự khác nhau

Ví dụ:

```text
flower
flight
^^
```

`j` dừng tại vị trí `2`.

Các vị trí:

```text
0, 1
```

còn giống nhau.

Do đó độ dài tiền tố chung là:

```text
2
```

nên:

```cpp
dem = j;
```

---

### Trường hợp 2: chuỗi hiện tại ngắn hơn

Ví dụ:

```text
moc     = "flower"
strs[i] = "flow"
```

Sau khi kiểm tra:

```text
f l o w
```

`j = 4`.

Chuỗi `"flow"` đã hết.

Vì vậy tiền tố chung có độ dài:

```text
4
```

và:

```cpp
dem = j;
```

vẫn chính xác.

---

# 9. Tại sao có thể `break` khi `dem == 0`?

Ta có:

```cpp
if (dem == 0) break;
```

Nếu:

```text
dem = 0
```

thì không có bất kỳ ký tự đầu tiên nào giống nhau.

Ví dụ:

```text
["dog", "racecar", "car"]
```

Ngay khi so sánh:

```text
d != r
```

ta có:

```text
dem = 0
```

Khi đó kết quả chắc chắn là:

```text
""
```

Không cần xét các chuỗi còn lại nữa.

Đây là một dạng **early termination (dừng sớm)**.

---

# 10. Vì sao cuối cùng dùng `substr`?

Sau khi xét tất cả các chuỗi, `dem` chứa độ dài tiền tố chung dài nhất.

Ví dụ:

```text
moc = "flower"
dem = 2
```

Ta cần lấy 2 ký tự đầu tiên của `moc`.

Dùng:

```cpp
return moc.substr(0, dem);
```

Kết quả:

```text
"fl"
```

Cú pháp:

```cpp
substr(vitri_bat_dau, so_luong_ky_tu)
```

nên:

```cpp
moc.substr(0, dem)
```

có nghĩa là:

> Lấy `dem` ký tự bắt đầu từ vị trí `0`.

---

# 11. Kiểm tra trường hợp mảng rỗng

Đầu tiên có:

```cpp
if (strs.empty()) return "";
```

Điều này cần thiết vì nếu `strs` rỗng mà ta viết:

```cpp
string moc = strs[0];
```

thì `strs[0]` không tồn tại.

Vì vậy phải kiểm tra trước.

---

# 12. Kiểm tra lời giải trên một số test case

## Test 1

```text
Input:
["flower", "flow", "flight"]
```

Ban đầu:

```text
moc = "flower"
dem = 6
```

So với `"flow"`:

```text
dem = 4
```

So với `"flight"`:

```text
f == f
l == l
o != i
```

nên:

```text
dem = 2
```

Kết quả:

```text
"fl"
```

---

## Test 2

```text
Input:
["dog", "racecar", "car"]
```

So sánh:

```text
d != r
```

nên:

```text
dem = 0
```

`break`.

Kết quả:

```text
""
```

---

## Test 3

```text
Input:
["interview", "internet", "internal"]
```

Ta có:

```text
interview
internet
internal
^^^^^^
```

Tiền tố chung là:

```text
"inter"
```

Kết quả:

```text
"inter"
```

---

## Test 4

```text
Input:
["a", "a", "a"]
```

Tất cả giống nhau.

Kết quả:

```text
"a"
```

---

## Test 5

```text
Input:
["abc", "ab"]
```

Sau khi so sánh:

```text
abc
ab
^^
```

ta có:

```text
dem = 2
```

Kết quả:

```text
"ab"
```

---

## Test 6

```text
Input:
["", "abc"]
```

Chuỗi đầu tiên có độ dài 0:

```text
dem = 0
```

Kết quả:

```text
""
```

---

# 13. Độ phức tạp

Gọi:

- `N` = số lượng chuỗi.
- `L` = độ dài của tiền tố chung cần kiểm tra, hoặc chính xác hơn là số ký tự tối đa được so sánh trong quá trình tìm LCP.

Ta duyệt qua tối đa `N - 1` chuỗi và mỗi chuỗi có thể cần so sánh tối đa `L` ký tự.

Do đó độ phức tạp thời gian là:

```text
O(N × L)
```

Đây là mức độ phức tạp phù hợp cho cách tiếp cận so sánh theo ký tự.

### Độ phức tạp bộ nhớ

Ngoài chuỗi đầu vào, thuật toán chỉ sử dụng một số biến:

```cpp
moc
dem
i
j
```

Không tạo thêm cấu trúc dữ liệu phụ thuộc vào số lượng chuỗi.

Có thể xem **auxiliary space** là:

```text
O(1)
```

nếu coi `moc` là một tham chiếu/đại diện của dữ liệu đầu vào; trong code hiện tại `string moc = strs[0]` thực tế tạo một bản sao chuỗi đầu tiên nên bộ nhớ cho bản sao này là `O(|strs[0]|)`.

---

# 14. Vì sao không cần sắp xếp?

Một hướng khác có thể là sắp xếp các chuỗi rồi chỉ so sánh chuỗi đầu tiên và cuối cùng.

Nhưng cách đó cần thêm chi phí sắp xếp:

```text
O(N log N)
```

trong khi bài toán này không yêu cầu phải sắp xếp.

Lời giải hiện tại chỉ cần duyệt qua các chuỗi:

```text
O(N × L)
```

và dừng sớm nếu tiền tố chung trở thành rỗng.

Do đó cách tiếp cận trực tiếp này rất tự nhiên cho bài toán.

---

# 15. Có thể tối ưu thêm code hiện tại không?

Có một điểm nhỏ có thể cải thiện:

```cpp
int dem = moc.length();
```

`length()` trả về kiểu `size_t`, còn `dem` là `int`.

Trong phạm vi ràng buộc thông thường của LeetCode, việc này không gây vấn đề thực tế, nhưng về mặt C++ hiện đại có thể dùng `size_t`.

Tuy nhiên, nếu mục tiêu là **code dễ học, dễ đọc và phù hợp với cách viết hiện tại**, dùng `int` như lời giải dưới đây hoàn toàn dễ hiểu.

Ngoài ra, điều kiện:

```cpp
j < dem && j < strs[i].length()
```

được viết theo thứ tự hợp lý: trước tiên giới hạn bởi tiền tố chung hiện tại, sau đó đảm bảo không vượt quá độ dài chuỗi đang xét.

---

# 16. Code lời giải của bạn

Code dưới đây giữ nguyên ý tưởng và cấu trúc lời giải của bạn:

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if (strs.empty()) return "";

        string moc = strs[0];
        int dem = moc.length(); // Khoi tao dem bang do dai chuoi moc

        for (int i = 1; i < strs.size(); i++) {
            int j = 0;

            // Chi can so sanh j < dem vi vuot qua dem thi khong can xet nua
            while (j < dem && j < strs[i].length() && moc[j] == strs[i][j]) {
                j++;
            }

            // Cap nhat lai do dai tien to chung ngan nhat thu duoc
            dem = j;

            // Dung som neu tien to chung bang rong
            if (dem == 0) break;
        }

        // Cat chuoi moc lay dem ky tu dau
        return moc.substr(0, dem);
    }
};
```

---

# 17. Tóm tắt tư duy cần nhớ

Có thể ghi nhớ bài này bằng 5 bước:

```text
1. Chọn strs[0] làm chuỗi mốc.
2. dem = độ dài chuỗi mốc.
3. Với từng chuỗi còn lại, so sánh từ ký tự đầu tiên.
4. Cập nhật dem thành số ký tự giống nhau.
5. Trả về dem ký tự đầu của chuỗi mốc.
```

Quan trọng nhất là hiểu invariant:

> **Sau khi xét xong `strs[i]`, `dem` chính là độ dài tiền tố chung của tất cả các chuỗi từ `strs[0]` đến `strs[i]`.**

Nhờ invariant này, mỗi lần gặp một chuỗi mới, ta chỉ cần **thu hẹp** tiền tố chung hiện tại chứ không cần xây dựng lại từ đầu.

