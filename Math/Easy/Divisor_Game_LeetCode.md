# Divisor Game - LeetCode

## 1. Đề bài

Cho một số nguyên `n`.

Alice và Bob lần lượt thực hiện nước đi:

- Ở lượt của mình, người chơi chọn một số nguyên `x` sao cho:
  - `x` là ước của `n`.
  - `0 < x < n`.
- Sau đó thay `n` bằng `n - x`.
- Nếu một người **không thể thực hiện nước đi hợp lệ**, người đó thua.

Cần xác định Alice có thể thắng nếu cả hai đều chơi tối ưu hay không.

---

## 2. Điều quan trọng nhất của bài

Bài này nhìn ban đầu giống một bài DP vì:

- Sau mỗi nước đi, giá trị `n` giảm xuống.
- Có nhiều lựa chọn `x`.
- Ta có thể nghĩ đến việc tính kết quả thắng/thua cho từng giá trị `n`.

Tuy nhiên, nếu suy xét kỹ các nước đi, bài toán có một quy luật rất đơn giản:

> Alice thắng khi và chỉ khi `n` là số chẵn.

Đây mới là ý tưởng quan trọng nhất của bài.

Không cần xây dựng bảng DP.
Không cần thử tất cả các ước.
Không cần tìm ước bằng vòng lặp.

Chỉ cần kiểm tra `n` chẵn hay lẻ.

---

# 3. Phân tích các nước đi

Trước tiên hãy xem một vài giá trị nhỏ.

## n = 1

Không tồn tại `x` thỏa mãn:

- `x > 0`
- `x < 1`
- `x` là ước của `1`

Vì vậy Alice không thể đi.

=> Alice thua.

---

## n = 2

Các ước dương nhỏ hơn `2` chỉ có:

```text
x = 1
```

Alice đi:

```text
2 -> 1
```

Bây giờ Bob gặp `n = 1`.

Bob không thể đi.

=> Alice thắng.

---

## n = 3

Các ước dương nhỏ hơn `3`:

```text
x = 1
```

Alice buộc phải đi:

```text
3 -> 2
```

Bob gặp `2`, nên Bob đi:

```text
2 -> 1
```

Alice gặp `1` và không thể đi.

=> Alice thua.

---

## n = 4

Các ước dương nhỏ hơn `4`:

```text
x = 1
x = 2
```

Alice có thể chọn `x = 1`:

```text
4 -> 3
```

Nhưng sau đó Bob ở `3`, Bob sẽ đi:

```text
3 -> 2
```

và Alice lại ở `2`, có thể đi:

```text
2 -> 1
```

Alice thắng.

Một cách nhìn trực tiếp hơn:

```text
4
|
| chọn x = 2
v
2
|
| chọn x = 1
v
1
```

Bob là người gặp `1`, nên Bob thua.

=> Alice thắng.

---

# 4. Quan sát quy luật

Xét hai loại trạng thái:

- `n` chẵn
- `n` lẻ

Điểm mấu chốt nằm ở tính chất của các ước.

## Khi n là số chẵn

Nếu `n > 1` và `n` chẵn thì `1` luôn là một ước hợp lệ.

Quan trọng hơn:

> Vì `n` chẵn, Alice có thể chọn `x = 1`.

Khi đó:

```text
n -> n - 1
```

Mà:

```text
chẵn - 1 = lẻ
```

Tức là từ một số chẵn, ta có thể chủ động đưa đối thủ về một số lẻ.

Nhưng chỉ nói "chẵn có thể chuyển thành lẻ" vẫn chưa đủ.

Ta cần chứng minh rằng số lẻ thực sự là trạng thái bất lợi.

---

# 5. Vì sao số lẻ là trạng thái thua?

Giả sử `n` là số lẻ.

Ta cần xem tất cả các nước đi có thể xảy ra.

Một ước của một số lẻ luôn là số lẻ.

Do đó, với mọi nước đi hợp lệ:

```text
x là số lẻ
```

Ta có:

```text
n - x = lẻ - lẻ = chẵn
```

Vậy:

> Từ một số lẻ, bất kỳ nước đi nào cũng đưa đối thủ về một số chẵn.

Đây là điểm cực kỳ quan trọng.

Ví dụ:

```text
9
```

Các ước hợp lệ:

```text
1, 3
```

Các nước đi:

```text
9 -> 8
9 -> 6
```

Cả `8` và `6` đều là số chẵn.

Hay:

```text
15
```

Các ước hợp lệ:

```text
1, 3, 5
```

Các nước đi:

```text
15 -> 14
15 -> 12
15 -> 10
```

Tất cả đều là số chẵn.

Vì vậy khi đang ở một số lẻ:

> Người chơi hiện tại không thể đưa đối thủ về một số lẻ. Họ buộc phải đưa đối thủ về số chẵn.

---

# 6. Vì sao điều này tạo thành chiến thuật thắng?

Ta có hai quy luật:

### Nếu n lẻ

Mọi nước đi đều biến:

```text
lẻ -> chẵn
```

### Nếu n chẵn

Ta có thể chọn:

```text
x = 1
```

để biến:

```text
chẵn -> lẻ
```

Do đó người chơi muốn chiến thắng sẽ cố gắng:

> Đưa đối thủ về số lẻ.

Nếu Alice bắt đầu với số chẵn, Alice có thể chọn `x = 1`:

```text
chẵn -> lẻ
```

Sau đó Bob phải chơi từ số lẻ.

Bob sẽ bắt buộc đưa số đó về chẵn.

Alice lại đang ở số chẵn.

Alice tiếp tục chọn `x = 1`.

Quá trình cứ tiếp tục:

```text
Alice: chẵn -> lẻ
Bob:   lẻ -> chẵn
Alice: chẵn -> lẻ
Bob:   lẻ -> chẵn
...
```

Cuối cùng sẽ xuất hiện:

```text
2 -> 1
```

Người thực hiện:

```text
2 -> 1
```

là người chơi ở trạng thái chẵn.

Người còn lại gặp:

```text
n = 1
```

và không thể đi.

Vì Alice luôn có thể giữ chiến lược này khi ban đầu `n` chẵn, Alice thắng.

---

# 7. Tại sao Alice thua khi n lẻ?

Giả sử ban đầu `n` là số lẻ.

Alice phải thực hiện một nước đi.

Nhưng như đã chứng minh:

```text
lẻ -> chẵn
```

bất kể Alice chọn ước nào.

Vì vậy Bob sẽ nhận được một số chẵn.

Từ số chẵn, Bob có thể chọn:

```text
x = 1
```

để đưa Alice trở lại số lẻ.

Sau đó Alice lại buộc phải đưa Bob về số chẵn.

Ta có:

```text
Alice: lẻ -> chẵn
Bob:   chẵn -> lẻ
Alice: lẻ -> chẵn
Bob:   chẵn -> lẻ
...
```

Cuối cùng Alice sẽ là người gặp:

```text
n = 1
```

và không thể thực hiện nước đi.

=> Alice thua.

---

# 8. Một cách nhìn rất dễ nhớ

Có thể coi:

```text
Số lẻ = trạng thái thua
Số chẵn = trạng thái thắng
```

Vì:

```text
lẻ
 |
 | mọi nước đi
 v
chẵn
```

Còn:

```text
chẵn
 |
 | chọn x = 1
 v
lẻ
```

Tức là:

- Từ trạng thái thua, mọi nước đi đều đưa đối thủ đến trạng thái thắng.
- Từ trạng thái thắng, tồn tại ít nhất một nước đi đưa đối thủ đến trạng thái thua.

Đây chính là bản chất của bài toán trò chơi.

---

# 9. Không cần tìm tất cả các ước

Một điểm rất dễ khiến bài này bị làm phức tạp không cần thiết là nghĩ rằng phải:

1. Tìm tất cả các ước của `n`.
2. Thử từng ước.
3. Xét xem sau khi trừ ước đó thì thắng hay thua.

Cách đó có thể dẫn đến DP hoặc đệ quy.

Nhưng bài này có một quy luật mạnh hơn:

> Khi `n` lẻ, tất cả các ước của `n` đều lẻ.

Vì thế ta biết ngay tất cả các nước đi từ `n` lẻ đều dẫn đến số chẵn.

Còn khi `n` chẵn, ta không cần tìm một ước bất kỳ nào khác.

Chỉ cần biết:

```text
1 là ước của mọi số nguyên dương
```

nên luôn tồn tại nước đi:

```text
n -> n - 1
```

Do đó việc tìm ước hoàn toàn không cần thiết.

---

# 10. Phân tích theo các giá trị nhỏ

Ta có thể lập bảng để thấy quy luật rõ hơn:

| n | Trạng thái | Kết quả |
|---|---|---|
| 1 | Lẻ | Thua |
| 2 | Chẵn | Thắng |
| 3 | Lẻ | Thua |
| 4 | Chẵn | Thắng |
| 5 | Lẻ | Thua |
| 6 | Chẵn | Thắng |
| 7 | Lẻ | Thua |
| 8 | Chẵn | Thắng |
| 9 | Lẻ | Thua |
| 10 | Chẵn | Thắng |

Quy luật:

```text
n = 1  -> thua
n = 2  -> thắng
n = 3  -> thua
n = 4  -> thắng
n = 5  -> thua
n = 6  -> thắng
...
```

Vì vậy:

```text
n lẻ  -> false
n chẵn -> true
```

---

# 11. Vì sao chọn x = 1 là chiến thuật quan trọng?

Điểm đặc biệt của `x = 1`:

- `1` luôn là ước của `n`.
- `1 < n` khi `n > 1`.
- Trừ `1` làm thay đổi tính chẵn lẻ.

Cụ thể:

```text
chẵn - 1 = lẻ
lẻ   - 1 = chẵn
```

Khi đang ở số chẵn, chọn `1` giúp người chơi kiểm soát tính chẵn lẻ của trạng thái tiếp theo.

Đây là chiến thuật đơn giản nhưng rất mạnh.

Ví dụ với:

```text
n = 10
```

Alice có thể chơi:

```text
10 -> 9
```

Bob ở `9`.

Bob có thể chọn `1` hoặc `3`:

```text
9 -> 8
```

hoặc:

```text
9 -> 6
```

Trong cả hai trường hợp Alice lại nhận một số chẵn.

Nếu Bob chọn:

```text
9 -> 6
```

Alice tiếp tục:

```text
6 -> 5
```

Bob buộc phải đưa `5` về số chẵn:

```text
5 -> 4
```

hoặc:

```text
5 -> 2
```

Alice lại nhận số chẵn.

Điều quan trọng không phải là Alice phải đoán Bob sẽ chọn ước nào.

> Bất kể Bob chọn ước nào, nếu Bob đang ở số lẻ thì Alice vẫn nhận được số chẵn.

Đó chính là lý do chiến thuật này chắc chắn.

---

# 12. Chứng minh ngắn gọn

Ta có thể chứng minh bằng quy nạp trên `n`.

## Trường hợp cơ sở

```text
n = 1
```

Không có nước đi.

=> `1` là trạng thái thua.

## Giả sử quy luật đúng với các giá trị nhỏ hơn n

### n lẻ

Mọi ước `x` của `n` đều lẻ.

Do đó:

```text
n - x = lẻ - lẻ = chẵn
```

Mọi nước đi đều đưa đối thủ về trạng thái chẵn.

Theo giả thiết quy nạp, trạng thái chẵn là trạng thái thắng cho người tiếp theo.

=> `n` lẻ là trạng thái thua.

### n chẵn

Chọn:

```text
x = 1
```

Ta có:

```text
n - 1 = lẻ
```

Theo kết quả trên, số lẻ là trạng thái thua.

=> `n` chẵn là trạng thái thắng.

Vậy:

```text
Alice thắng <=> n chẵn
```

---

# 13. Thuật toán tối ưu

Thuật toán chỉ gồm một bước:

```text
Nếu n % 2 == 0:
    trả về true
Ngược lại:
    trả về false
```

Không cần:

- DP.
- Đệ quy.
- Tìm ước.
- Mảng.
- `sqrt()`.
- Vòng lặp.

---

# 14. Độ phức tạp

Thời gian:

```text
O(1)
```

Chỉ thực hiện một phép kiểm tra chẵn/lẻ.

Bộ nhớ:

```text
O(1)
```

Không sử dụng thêm cấu trúc dữ liệu.

Đây là lời giải tối ưu theo cả thời gian và bộ nhớ.

---

# 15. Lời giải C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool divisorGame(int n) {
        return n % 2 == 0;
    }
};
```

---

# 16. Luồng xử lý của code

Dòng:

```cpp
return n % 2 == 0;
```

kiểm tra phần dư khi chia `n` cho `2`.

Nếu:

```text
n % 2 == 0
```

thì `n` là số chẵn.

Hàm trả về:

```text
true
```

tức Alice thắng.

Nếu:

```text
n % 2 != 0
```

thì `n` là số lẻ.

Hàm trả về:

```text
false
```

tức Alice thua.

Có thể viết tương đương:

```cpp
if (n % 2 == 0)
    return true;
return false;
```

Nhưng:

```cpp
return n % 2 == 0;
```

ngắn gọn hơn mà vẫn thể hiện chính xác quy luật của bài.

---

# 17. Ví dụ mô phỏng

## Ví dụ 1

```text
n = 2
```

Alice:

```text
2 -> 1
```

Bob không đi được.

Kết quả:

```text
true
```

---

## Ví dụ 2

```text
n = 3
```

Alice chỉ có thể:

```text
3 -> 2
```

Bob:

```text
2 -> 1
```

Alice không đi được.

Kết quả:

```text
false
```

---

## Ví dụ 3

```text
n = 6
```

Alice chọn:

```text
6 -> 5
```

Bob ở `5`.

Bob có thể chọn:

```text
5 -> 4
```

Alice:

```text
4 -> 3
```

Bob:

```text
3 -> 2
```

Alice:

```text
2 -> 1
```

Bob không đi được.

Kết quả:

```text
true
```

Điểm quan trọng là Alice không cần biết Bob sẽ chọn gì.

Từ số lẻ, Bob luôn phải đưa trạng thái về số chẵn.

---

# 18. Những điều cần nhớ khi gặp bài này

Nếu gặp lại Divisor Game, hãy nhớ chuỗi suy luận:

```text
1. Xét tính chẵn/lẻ của n.
        |
        v
2. Nếu n lẻ:
   mọi ước của n đều lẻ.
        |
        v
3. lẻ - lẻ = chẵn
        |
        v
4. Người đang ở số lẻ luôn đưa đối thủ về số chẵn.
        |
        v
5. Nếu n chẵn:
   chọn x = 1.
        |
        v
6. chẵn - 1 = lẻ
        |
        v
7. Số chẵn là trạng thái thắng.
   Số lẻ là trạng thái thua.
        |
        v
8. Kết quả chỉ cần kiểm tra n % 2.
```

## Kết luận

Quy luật tối ưu của Divisor Game là:

```text
n chẵn -> Alice thắng
n lẻ   -> Alice thua
```

Vì vậy lời giải cuối cùng chỉ cần:

```cpp
return n % 2 == 0;
```

Đây là một ví dụ điển hình cho việc **không nên vội dùng DP khi bài toán có thể được giải bằng cách tìm ra quy luật của trạng thái thắng/thua**.
