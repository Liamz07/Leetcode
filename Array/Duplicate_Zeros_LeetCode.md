# Duplicate Zeros — LeetCode

## 1. Mô tả bài toán

Cho một mảng số nguyên `arr`.

Yêu cầu **nhân đôi mỗi số 0 xuất hiện trong mảng**, đồng thời dịch các phần tử phía sau sang phải.

Điểm quan trọng:

> Mảng phải được thay đổi **in-place**, nghĩa là không được tạo một mảng phụ để chứa toàn bộ kết quả.

Nếu phần tử mới tạo ra vượt quá kích thước mảng thì phần đó bị bỏ đi.

### Ví dụ

```text
Input:
arr = [1,0,2,3,0,4,5,0]

Output:
[1,0,0,2,3,0,0,4]
```

Nếu không giới hạn kích thước, sau khi nhân đôi các số `0` ta có:

```text
[1,0,0,2,3,0,0,4,5,0,0]
```

Nhưng mảng ban đầu chỉ có 8 vị trí, nên chỉ giữ 8 phần tử đầu.

---

# 2. Điểm khó của bài toán

Nếu được phép tạo mảng mới, ta có thể làm rất đơn giản:

```text
Duyệt arr
    Nếu gặp 0:
        thêm 0
        thêm 0
    Ngược lại:
        thêm phần tử hiện tại
```

Nhưng bài yêu cầu **in-place**.

Nếu gặp `0` rồi chèn thêm một `0` vào giữa mảng, ta phải dịch toàn bộ phần tử phía sau. Nếu có nhiều `0`, việc dịch lặp lại có thể khiến thuật toán lên tới `O(n²)`.

Vì vậy, ý tưởng tối ưu là:

> **Không chèn phần tử. Hãy xác định trước vị trí cuối cùng của các phần tử sau khi duplicate, rồi điền kết quả từ phải sang trái.**

---

# 3. Tại sao phải xử lý từ phải sang trái?

Ví dụ:

```text
arr = [1,0,2,3,0,4,5,0]
```

Sau khi duplicate về mặt logic:

```text
[1,0,0,2,3,0,0,4,5,0,0]
```

Ta chỉ giữ:

```text
[1,0,0,2,3,0,0,4]
```

Nếu đi từ trái sang phải, khi gặp `0`, việc ghi thêm `0` sẽ đè lên phần tử chưa xử lý.

Ngược lại, nếu đi từ phải sang trái:

- Các phần tử chưa xử lý vẫn nằm ở bên trái.
- Ta chỉ ghi vào vị trí bên phải.
- Vì vậy không làm mất dữ liệu chưa xử lý.

Đây là kỹ thuật rất phổ biến khi cần **mở rộng logic một mảng ngay tại chỗ**.

---

# 4. Tư tưởng đếm số vị trí

Gọi:

```cpp
n = arr.size();
```

Ta tưởng tượng rằng sau khi duplicate, mảng có thể dài hơn `n`.

Mỗi phần tử:

- Khác `0` → chiếm `1` vị trí.
- Bằng `0` → chiếm `2` vị trí.

Ta dùng:

```cpp
int dem = 0;
```

để đếm số vị trí mà mảng kết quả logic cần.

Ví dụ:

```text
arr = [1,0,2,3,0,4,5,0]
```

Ta có:

| Phần tử | Số vị trí sau duplicate | Tổng |
|---|---:|---:|
| `1` | 1 | 1 |
| `0` | 2 | 3 |
| `2` | 1 | 4 |
| `3` | 1 | 5 |
| `0` | 2 | 7 |
| `4` | 1 | 8 |

Khi đạt `8`, ta đã biết rằng chỉ các phần tử từ đầu đến `4` ảnh hưởng đến 8 vị trí thực của mảng.

---

# 5. Trường hợp đặc biệt: `dem > n`

Đây là phần dễ sai nhất.

Ví dụ:

```text
arr = [1,2,3,0]
```

Ta có:

```text
1 → 1
2 → 1
3 → 1
0 → 2
```

Tổng:

```text
5
```

nhưng:

```text
n = 4
```

Mảng logic là:

```text
[1,2,3,0,0]
```

Trong mảng thực chỉ giữ:

```text
[1,2,3,0]
```

Do đó số `0` cuối cùng chỉ được ghi **một lần**.

Ta gọi đây là trường hợp:

```cpp
dem > n
```

---

# 6. Thuật toán hoàn chỉnh

## Bước 1: Đếm số vị trí

```cpp
int dem = 0;
int i = 0;

while (dem < n) {
    if (arr[i] == 0)
        dem += 2;
    else
        dem++;

    i++;
}
```

Sau vòng lặp:

```cpp
i - 1
```

là phần tử cuối cùng có ảnh hưởng đến kết quả.

---

## Bước 2: Đặt con trỏ ghi

Vị trí cuối cùng của mảng thật là:

```cpp
int j = n - 1;
```

Ta bắt đầu xử lý từ:

```cpp
i--;
```

vì vòng lặp trên đã tăng `i` lên một vị trí.

---

## Bước 3: Xử lý trường hợp `dem > n`

Nếu:

```cpp
dem > n
```

thì phần tử cuối cùng đã làm vượt quá kích thước mảng.

Trong trường hợp này, nó bắt buộc phải là `0`.

Ta chỉ ghi một `0`:

```cpp
arr[j] = 0;
j--;
i--;
```

---

## Bước 4: Duyệt từ phải sang trái

Nếu:

```cpp
arr[i] != 0
```

thì chỉ cần:

```cpp
arr[j] = arr[i];
j--;
```

Nếu:

```cpp
arr[i] == 0
```

thì cần hai bản sao:

```cpp
arr[j] = 0;
arr[j - 1] = 0;
j -= 2;
```

Sau đó:

```cpp
i--;
```

tiếp tục.

---

# 7. Code C++ tối ưu

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    void duplicateZeros(vector<int>& arr) {
        int n = arr.size();
        int dem = 0;
        int i = 0;

        while (dem < n) {
            if (arr[i] == 0)
                dem += 2;
            else
                dem++;

            i++;
        }

        int j = n - 1;
        i--;

        if (dem > n) {
            arr[j] = 0;
            j--;
            i--;
        }

        while (i >= 0) {
            if (arr[i] == 0) {
                arr[j] = 0;
                arr[j - 1] = 0;
                j -= 2;
            } else {
                arr[j] = arr[i];
                j--;
            }

            i--;
        }
    }
};
```

---

# 8. Giải thích code từng phần

## Khai báo kích thước

```cpp
int n = arr.size();
```

`n` là kích thước cố định của mảng.

---

## Đếm số vị trí logic

```cpp
int dem = 0;
int i = 0;

while (dem < n) {
    if (arr[i] == 0)
        dem += 2;
    else
        dem++;

    i++;
}
```

Ý nghĩa:

```text
số khác 0 → +1
số 0      → +2
```

Ta dừng khi số vị trí logic đã đạt hoặc vượt `n`.

---

## Con trỏ cuối mảng

```cpp
int j = n - 1;
```

`j` là vị trí mà ta sẽ ghi phần tử tiếp theo trong mảng thực.

---

## Đưa `i` về phần tử cuối cần xử lý

```cpp
i--;
```

Vì sau vòng `while`, `i` đang đứng sau phần tử cuối cần xét.

---

## Xử lý zero bị cắt

```cpp
if (dem > n) {
    arr[j] = 0;
    j--;
    i--;
}
```

Nếu `dem > n`, phần tử cuối cùng đã chiếm 2 vị trí nhưng chỉ còn 1 vị trí thực.

Vì phần tử đó chắc chắn là `0`, ta ghi một `0` rồi bỏ qua phần tử đó khi tiếp tục xử lý.

---

## Sao chép từ phải sang trái

```cpp
while (i >= 0) {
```

Ta xử lý từng phần tử từ cuối về đầu.

### Nếu khác 0

```cpp
arr[j] = arr[i];
j--;
```

Chỉ cần sao chép một lần.

### Nếu bằng 0

```cpp
arr[j] = 0;
arr[j - 1] = 0;
j -= 2;
```

Số `0` được nhân đôi nên cần hai vị trí.

Cuối cùng:

```cpp
i--;
```

chuyển sang phần tử trước đó.

---

# 9. Chạy thử ví dụ 1

```text
arr = [1,0,2,3,0,4,5,0]
```

Ta có:

```text
n = 8
```

Đếm:

```text
1 → dem = 1
0 → dem = 3
2 → dem = 4
3 → dem = 5
0 → dem = 7
4 → dem = 8
```

Dừng tại:

```text
dem = 8
```

Phần tử cuối cần xử lý là:

```text
4
```

Sau đó đi từ phải sang trái:

```text
4
0 → 0 0
3
2
0 → 0 0
1
```

Kết quả:

```text
[1,0,0,2,3,0,0,4]
```

---

# 10. Chạy thử trường hợp `dem > n`

```text
arr = [1,2,3,0]
```

Ta có:

```text
1 → dem = 1
2 → dem = 2
3 → dem = 3
0 → dem = 5
```

Vì:

```text
5 > 4
```

nên `0` cuối chỉ được ghi một lần.

Kết quả:

```text
[1,2,3,0]
```

---

# 11. Chạy thử với nhiều số 0

```text
arr = [0,0,0,0,0,0,0]
```

Mặc dù về mặt logic mỗi `0` muốn được nhân đôi, mảng chỉ có 7 vị trí.

Thuật toán vẫn chỉ ghi đúng 7 phần tử:

```text
[0,0,0,0,0,0,0]
```

Không cần tạo mảng phụ.

---

# 12. Tại sao thuật toán không làm mất dữ liệu?

Đây là ý tưởng cốt lõi.

Ta luôn có hai chỉ số:

```text
i: phần tử đang đọc
j: vị trí đang ghi
```

Ta đi từ:

```text
phải → trái
```

Hình dung:

```text
[ phần chưa xử lý ][ i ][ ........ ][ j ]
```

Các phần tử chưa xử lý nằm bên trái `i`.

Khi ghi vào `j`, ta chỉ tác động lên vùng bên phải.

Vì vậy ta không ghi đè lên phần tử chưa đọc.

Đây là lý do thuật toán có thể hoạt động **in-place**.

---

# 13. Tại sao không làm từ trái sang phải?

Giả sử:

```text
arr = [1,2,0,3,4,5]
```

Nếu gặp `0` ở vị trí 2, ta muốn biến:

```text
[1,2,0,3,4,5]
```

thành:

```text
[1,2,0,0,3,4]
```

Ta phải dịch:

```text
3,4,5
```

sang phải.

Nếu tiếp tục gặp một `0` khác, lại phải dịch tiếp.

Trong trường hợp có nhiều số `0`, tổng số lần dịch có thể rất lớn.

Do đó cách này có thể đạt:

```text
O(n²)
```

Trong khi phương pháp từ phải sang trái chỉ cần:

```text
O(n)
```

---

# 14. Chứng minh tính đúng đắn

Ta chia thành hai phần.

## Phần 1: Xác định phần tử cuối cùng

Mỗi số khác `0` chiếm 1 vị trí.

Mỗi số `0` chiếm 2 vị trí.

Vì vậy biến `dem` chính xác biểu diễn số vị trí mà các phần tử đã xét sẽ chiếm trong mảng sau duplicate.

Khi `dem >= n`, ta biết chính xác phần đầu nào của mảng có ảnh hưởng đến `n` vị trí kết quả.

---

## Phần 2: Điền từ phải sang trái

Với số khác `0`, ta sao chép đúng một lần.

Với số `0`, ta sao chép hai lần.

Nếu `dem > n`, bản sao thứ hai của `0` nằm ngoài mảng nên chỉ ghi một lần.

Do các phần tử được xử lý theo thứ tự từ phải sang trái và luôn ghi vào các vị trí chưa cần cho phần tử chưa xử lý, không có dữ liệu cần thiết nào bị mất.

Vì vậy kết quả cuối cùng chính xác.

---

# 15. Độ phức tạp

## Thời gian

Có hai lần duyệt mảng:

- Lần đầu xác định số vị trí sau duplicate: `O(n)`.
- Lần hai điền kết quả: `O(n)`.

Do đó:

```text
O(n) + O(n) = O(n)
```

### Kết luận

```text
Thời gian: O(n)
```

---

## Bộ nhớ

Ta chỉ sử dụng:

```cpp
n
dem
i
j
```

Không tạo mảng phụ.

Do đó:

```text
Bộ nhớ phụ: O(1)
```

Đây chính là ưu điểm quan trọng của lời giải.

---

# 16. So sánh các phương pháp

| Phương pháp | Thời gian | Bộ nhớ phụ | In-place |
|---|---:|---:|---|
| Tạo mảng mới | `O(n)` | `O(n)` | Không |
| Chèn và dịch từ trái sang phải | Có thể `O(n²)` | `O(1)` | Có |
| **Đếm + điền từ phải sang trái** | **O(n)** | **O(1)** | **Có** |

Vì đề bài yêu cầu `in-place`, phương pháp cuối là lựa chọn tối ưu.

---

# 17. Tư duy tổng quát rút ra

Bài `Duplicate Zeros` là một ví dụ quan trọng của kỹ thuật:

> **Xác định vị trí cuối cùng trước, sau đó xử lý ngược.**

Khi gặp bài toán có dạng:

```text
Một thao tác làm mảng "nở ra"
+
Không được dùng mảng phụ
```

hãy nghĩ đến:

```text
1. Tính kích thước logic sau khi biến đổi.
2. Xác định phần tử cuối cùng còn ảnh hưởng.
3. Dùng con trỏ ở cuối mảng thật.
4. Điền kết quả từ phải sang trái.
```

Đây là kỹ thuật có thể áp dụng cho nhiều bài toán mảng khác.

---

# 18. Tóm tắt thuật toán

```text
arr
 │
 ▼
Duyệt từ trái sang phải
 │
 ├─ khác 0 → +1 vị trí
 └─ bằng 0 → +2 vị trí
 │
 ▼
Tìm phần tử cuối cùng ảnh hưởng
 │
 ▼
Đặt j = n - 1
 │
 ▼
Duyệt từ phải sang trái
 │
 ├─ khác 0 → ghi 1 lần
 │
 └─ bằng 0 → ghi 2 lần
 │
 ▼
Kết quả
```

### Độ phức tạp cuối cùng

```text
Thời gian: O(n)
Bộ nhớ phụ: O(1)
```

Đây là lời giải tối ưu cho yêu cầu `in-place` của bài `Duplicate Zeros`.
