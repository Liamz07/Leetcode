# Lemonade Change — Phương hướng tiếp cận tối ưu

## 1. Tổng quan bài toán

Bài toán **Lemonade Change** (LeetCode 860) mô phỏng một quầy bán nước chanh:

- Mỗi cốc nước có giá **$5**.
- Khách hàng lần lượt đưa một trong ba loại tiền: **$5, $10, $20**.
- Với mỗi khách, ta phải trả lại đúng tiền thừa nếu có.
- Ban đầu quầy **không có tiền**.
- Sau khi phục vụ xong một khách, số tiền nhận được từ khách được thêm vào quầy.
- Cần xác định liệu có thể phục vụ **tất cả khách hàng theo đúng thứ tự** hay không.

Ví dụ:

```text
Input:  [5, 5, 5, 10, 20]
Output: true
```

Diễn biến:

```text
Khách 1: đưa $5  -> không cần trả
Khách 2: đưa $5  -> không cần trả
Khách 3: đưa $5  -> không cần trả
Khách 4: đưa $10 -> trả $5
Khách 5: đưa $20 -> trả $15 = $10 + $5
```

---

## 2. Điều quan trọng nhất: ta thực sự cần quản lý những gì?

Có ba mệnh giá tiền:

```text
$5
$10
$20
```

Nhưng cần quan sát rằng:

### Khi khách đưa $5

Không cần trả lại tiền.

Ta chỉ cần:

```text
five++
```

### Khi khách đưa $10

Cần trả:

```text
$10 - $5 = $5
```

Do đó bắt buộc phải có một tờ $5:

```text
five > 0
```

Sau đó:

```text
five--
ten++
```

### Khi khách đưa $20

Cần trả:

```text
$20 - $5 = $15
```

Có hai cách tạo ra $15:

```text
$10 + $5
```

hoặc

```text
$5 + $5 + $5
```

Vì vậy, ta chỉ cần biết số lượng:

```text
five = số tờ $5
ten  = số tờ $10
```

Không cần lưu số tờ $20.

---

# 3. Vì sao không cần lưu tiền $20?

Đây là một quan sát quan trọng.

Nếu khách đưa $20, ta nhận được tờ $20 nhưng **tờ $20 không thể dùng để trả tiền thừa cho bất kỳ khách nào tiếp theo**, bởi số tiền thừa tối đa là:

```text
$20 - $5 = $15
```

Một tờ $20 không thể được sử dụng để tạo ra $5 hoặc $15 tiền thừa.

Do đó:

```text
$20 chỉ được nhận vào
$20 không bao giờ được dùng làm tiền thừa
```

Vì thế, trạng thái cần quản lý chỉ là:

```text
five
ten
```

Đây là một cách đơn giản hóa bài toán rất quan trọng.

---

# 4. Tìm chiến lược tham lam (Greedy)

Điểm khó nhất nằm ở trường hợp khách đưa **$20**.

Ta cần trả $15.

Có hai lựa chọn:

### Cách 1

```text
$10 + $5
```

### Cách 2

```text
$5 + $5 + $5
```

Vậy nên chọn cách nào?

## Chọn $10 + $5 trước

Nếu có:

```text
ten >= 1
five >= 1
```

thì ta nên trả:

```text
$10 + $5
```

thay vì:

```text
$5 + $5 + $5
```

### Tại sao?

Tờ $10 chỉ có thể hữu ích cho một tình huống:

```text
Khách đưa $20
```

Tức là để tạo $15 tiền thừa.

Trong khi đó, tờ $5 có thể được dùng trong nhiều tình huống hơn:

```text
Khách đưa $10 -> cần $5
Khách đưa $20 -> có thể cần ba tờ $5
Khách đưa $20 -> có thể cần $10 + $5
```

Nói cách khác:

> **$5 có tính linh hoạt cao hơn $10.**

Vì vậy, khi trả $15 bằng:

```text
$10 + $5
```

ta giữ lại nhiều tờ $5 nhất có thể.

Đây chính là ý tưởng greedy.

---

# 5. Chứng minh tại sao greedy là đúng

Ta xét trường hợp khách đưa $20.

Ta có hai cách trả:

### Phương án A

```text
$10 + $5
```

Sau giao dịch:

```text
five  = five - 1
ten   = ten - 1
```

### Phương án B

```text
$5 + $5 + $5
```

Sau giao dịch:

```text
five = five - 3
ten  = ten
```

So sánh hai phương án:

```text
                A             B
-------------------------------------
five          -1            -3
ten           -1             0
```

Phương án A giữ lại nhiều tiền $5 hơn.

Tại sao điều này tốt?

Bởi vì $5 là loại tiền duy nhất có thể trực tiếp dùng để trả lại cho khách đưa $10.

Ví dụ:

```text
[5, 5, 10, 20, 10]
```

Nếu ở khách $20 đầu tiên ta dùng:

```text
5 + 5 + 5
```

ta sẽ tiêu hết tiền $5.

Sau đó khi khách tiếp theo đưa $10, ta không còn $5 để trả.

Trong khi nếu dùng:

```text
10 + 5
```

ta vẫn giữ được nhiều tờ $5 hơn và còn có khả năng phục vụ các khách tiếp theo.

Vì vậy, khi có thể trả:

```text
$10 + $5
```

thì đó là lựa chọn an toàn hơn.

---

# 6. Quy tắc xử lý đầy đủ

Duyệt từng phần tử `bill` trong mảng.

## Trường hợp 1: `bill == 5`

Không cần trả tiền.

```cpp
five++;
```

---

## Trường hợp 2: `bill == 10`

Cần trả $5.

Nếu:

```cpp
five == 0
```

thì không thể phục vụ.

Ngược lại:

```cpp
five--;
ten++;
```

---

## Trường hợp 3: `bill == 20`

Cần trả $15.

Ưu tiên:

```text
$10 + $5
```

nếu có thể.

Tức là:

```cpp
if (ten >= 1 && five >= 1) {
    ten--;
    five--;
}
```

Nếu không thể, thử:

```text
$5 + $5 + $5
```

tức:

```cpp
else if (five >= 3) {
    five -= 3;
}
```

Nếu cả hai cách đều không thể:

```cpp
return false;
```

Sau khi trả tiền thừa thành công, ta không cần lưu tờ $20.

---

# 7. Thuật toán

Pseudo-code:

```text
five = 0
ten = 0

for mỗi bill trong bills:

    nếu bill == 5:
        five++

    nếu bill == 10:
        nếu five == 0:
            return false

        five--
        ten++

    nếu bill == 20:
        nếu ten >= 1 và five >= 1:
            ten--
            five--
        else nếu five >= 3:
            five -= 3
        else:
            return false

return true
```

---

# 8. Ví dụ chạy từng bước

Xét:

```text
bills = [5, 5, 5, 10, 20]
```

Ban đầu:

```text
five = 0
ten = 0
```

### Khách 1: $5

```text
five = 1
ten = 0
```

### Khách 2: $5

```text
five = 2
ten = 0
```

### Khách 3: $5

```text
five = 3
ten = 0
```

### Khách 4: $10

Cần $5.

Có $5 nên:

```text
five = 2
ten = 1
```

### Khách 5: $20

Cần $15.

Có:

```text
ten = 1
five = 2
```

Ta ưu tiên:

```text
$10 + $5
```

Sau đó:

```text
five = 1
ten = 0
```

Tất cả khách đều được phục vụ.

Kết quả:

```text
true
```

---

# 9. Ví dụ thất bại

Xét:

```text
bills = [10]
```

Ban đầu:

```text
five = 0
ten = 0
```

Khách đưa $10.

Cần trả:

```text
$5
```

Nhưng:

```text
five == 0
```

Không thể trả.

Kết quả:

```text
false
```

---

## Ví dụ khác

```text
bills = [5, 10, 20, 20]
```

### Khách 1: $5

```text
five = 1
ten = 0
```

### Khách 2: $10

Trả $5:

```text
five = 0
ten = 1
```

### Khách 3: $20

Cần $15.

Có $10 nhưng không có $5.

Không thể dùng:

```text
$10 + $5
```

Cũng không đủ ba tờ $5.

Vì vậy:

```text
false
```

---

# 10. Tại sao không nên dùng brute force?

Một cách suy nghĩ ban đầu có thể là:

> Với mỗi khách $20, thử tất cả các cách chọn tiền để trả.

Nhưng bài toán không yêu cầu ta lưu toàn bộ các tờ tiền và thử mọi tổ hợp.

Các mệnh giá bị giới hạn rất mạnh:

```text
5, 10, 20
```

và tiền thừa luôn chỉ có thể là:

```text
0
5
15
```

Do đó, việc duy trì hai biến:

```cpp
five
ten
```

là đủ.

Không cần:

- `vector` chứa từng tờ tiền.
- tìm kiếm tổ hợp.
- backtracking.
- dynamic programming.
- priority queue.
- sorting.

---

# 11. Tại sao không cần Dynamic Programming?

DP thường cần khi:

- Có nhiều lựa chọn.
- Quyết định hiện tại ảnh hưởng đến tương lai.
- Một lựa chọn greedy có thể dẫn đến nghiệm không tối ưu.

Ở đây, tuy lựa chọn trả $15 có hai cách, nhưng ta có thể chứng minh rõ ràng rằng:

```text
$10 + $5
```

luôn không tệ hơn:

```text
$5 + $5 + $5
```

vì nó giữ lại thêm hai tờ $5.

Do đó không cần lưu nhiều trạng thái để thử lại các quyết định trước.

Greedy giải quyết được trực tiếp.

---

# 12. Tại sao không cần sorting?

Thứ tự khách hàng là cố định và rất quan trọng.

Ví dụ:

```text
[10, 5]
```

thất bại ngay ở khách đầu tiên.

Nhưng nếu sort thành:

```text
[5, 10]
```

thì lại thành công.

Vì vậy, **không được sắp xếp mảng**.

Ta phải xử lý đúng thứ tự ban đầu.

---

# 13. Độ phức tạp

Gọi:

```text
n = số lượng khách hàng
```

Ta duyệt mảng đúng một lần.

Mỗi khách chỉ thực hiện một số phép toán `O(1)`.

Do đó:

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(1)
```

Đây là tối ưu về mặt độ phức tạp cho bài toán vì ít nhất ta phải đọc toàn bộ `n` phần tử trong trường hợp tất cả khách đều có thể được phục vụ.

---

# 14. Các lỗi thường gặp

## Lỗi 1: Quên rằng khách $10 cần $5

Sai:

```cpp
if (bill == 10) {
    ten++;
}
```

Đúng:

```cpp
if (bill == 10) {
    if (five == 0)
        return false;

    five--;
    ten++;
}
```

---

## Lỗi 2: Với $20 luôn dùng ba tờ $5

Ví dụ:

```cpp
if (bill == 20) {
    if (five >= 3)
        five -= 3;
    else if (five >= 1 && ten >= 1) {
        five--;
        ten--;
    }
}
```

Cách này có thể khiến thuật toán thất bại dù thực tế vẫn có thể phục vụ.

Phải ưu tiên:

```text
$10 + $5
```

trước.

---

## Lỗi 3: Nghĩ rằng $20 có thể dùng làm tiền thừa

Không thể.

Ví dụ khách đưa $10, ta cần trả $5.

Một tờ $20 không giúp ích gì.

---

## Lỗi 4: Không kiểm tra khả năng trả tiền trước khi cập nhật trạng thái

Ví dụ với $10:

```cpp
five--;
ten++;
```

nếu `five == 0` thì trạng thái đã bị sai.

Phải kiểm tra trước:

```cpp
if (five == 0)
    return false;

five--;
ten++;
```

---

## Lỗi 5: Dùng `else` sai cách

Nên viết rõ ràng:

```cpp
if (bill == 5) {
    ...
} else if (bill == 10) {
    ...
} else {
    ...
}
```

vì chỉ có ba loại mệnh giá.

---

# 15. C++ — Lời giải tối ưu

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        int five = 0;
        int ten = 0;

        for (int bill : bills) {
            if (bill == 5) {
                // Không cần trả tiền thừa.
                ++five;
            }
            else if (bill == 10) {
                // Cần trả lại $5.
                if (five == 0) {
                    return false;
                }

                --five;
                ++ten;
            }
            else { // bill == 20
                // Cần trả lại $15.
                // Ưu tiên dùng $10 + $5 để giữ lại nhiều $5 nhất.
                if (ten > 0 && five > 0) {
                    --ten;
                    --five;
                }
                // Nếu không có $10 + $5,
                // thử dùng ba tờ $5.
                else if (five >= 3) {
                    five -= 3;
                }
                else {
                    return false;
                }
            }
        }

        return true;
    }
};
```

---

# 16. Phiên bản C++ ngắn gọn hơn

Nếu đã hiểu ý tưởng và muốn code súc tích:

```cpp
class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        int five = 0, ten = 0;

        for (int bill : bills) {
            if (bill == 5) {
                ++five;
            }
            else if (bill == 10) {
                if (five == 0) return false;
                --five;
                ++ten;
            }
            else {
                if (ten > 0 && five > 0) {
                    --ten;
                    --five;
                }
                else if (five >= 3) {
                    five -= 3;
                }
                else {
                    return false;
                }
            }
        }

        return true;
    }
};
```

---

# 17. Cách nhận diện bài toán Greedy tương tự

Lemonade Change là một ví dụ rất tốt để học cách nhận diện greedy.

Khi gặp bài toán tương tự, có thể tự hỏi:

### Câu hỏi 1

> Ở mỗi bước có nhiều lựa chọn không?

Có.

Khi khách đưa $20:

```text
10 + 5
```

hoặc:

```text
5 + 5 + 5
```

### Câu hỏi 2

> Có một lựa chọn nào giúp bảo toàn tài nguyên quan trọng hơn cho tương lai không?

Có.

Tờ $5 quan trọng hơn vì nó có thể dùng để trả khách đưa $10.

### Câu hỏi 3

> Có thể chứng minh lựa chọn đó không làm mất khả năng tạo nghiệm về sau không?

Có.

Dùng:

```text
10 + 5
```

thay vì:

```text
5 + 5 + 5
```

giữ lại thêm hai tờ $5.

Vì vậy đây là một greedy choice hợp lý và có thể chứng minh.

---

# 18. Một cách nhìn sâu hơn: “giữ lại tài nguyên linh hoạt”

Có thể hiểu toàn bộ bài toán bằng một nguyên tắc:

> **Khi phải tiêu tài nguyên, hãy ưu tiên tiêu tài nguyên ít linh hoạt hơn và giữ lại tài nguyên linh hoạt hơn.**

Trong bài này:

```text
$10: ít linh hoạt hơn
$5 : linh hoạt hơn
```

Vì:

```text
$10 -> chủ yếu dùng trong trường hợp khách đưa $20
$5  -> dùng được cho cả khách $10 và khách $20
```

Do đó:

```text
$20:
    ưu tiên dùng $10 + $5
```

Đây là insight quan trọng hơn cả việc nhớ code.

---

# 19. Dry run với một ví dụ phức tạp

Xét:

```text
[5, 5, 10, 5, 20, 5, 10, 20]
```

Ta lập bảng:

| Khách | Tiền khách đưa | $5 trước | $10 trước | Hành động | $5 sau | $10 sau |
|---:|---:|---:|---:|---|---:|---:|
| 1 | 5  | 0 | 0 | nhận $5 | 1 | 0 |
| 2 | 5  | 1 | 0 | nhận $5 | 2 | 0 |
| 3 | 10 | 2 | 0 | trả $5 | 1 | 1 |
| 4 | 5  | 1 | 1 | nhận $5 | 2 | 1 |
| 5 | 20 | 2 | 1 | trả $10+$5 | 1 | 0 |
| 6 | 5  | 1 | 0 | nhận $5 | 2 | 0 |
| 7 | 10 | 2 | 0 | trả $5 | 1 | 1 |
| 8 | 20 | 1 | 1 | trả $10+$5 | 0 | 0 |

Kết thúc:

```text
true
```

---

# 20. Checklist khi đi thi / phỏng vấn

Khi gặp Lemonade Change, chỉ cần nhớ:

```text
1. Giá = $5.
2. Chỉ cần lưu số $5 và $10.
3. $5 -> nhận thêm một tờ $5.
4. $10 -> phải có $5 để trả.
5. $20 -> cần trả $15.
6. Với $20:
      ưu tiên $10 + $5
      nếu không được thì $5 + $5 + $5
7. Không đủ tiền thừa -> false.
8. Duyệt đúng thứ tự.
9. O(n) time, O(1) space.
```

---

# 21. Kết luận

Lời giải tối ưu của Lemonade Change dựa trên ba ý tưởng:

1. **Chỉ cần theo dõi `$5` và `$10`**, vì `$20` không bao giờ được dùng để trả tiền thừa.
2. Khi khách đưa `$20`, **ưu tiên trả `$10 + $5`** thay vì `$5 + $5 + $5`, vì `$5` linh hoạt hơn và cần được bảo toàn.
3. Duyệt khách hàng đúng thứ tự trong một vòng lặp, từ đó đạt:

```text
Time:  O(n)
Space: O(1)
```

Đây là một bài toán greedy kinh điển: thay vì thử mọi khả năng, ta tìm ra **lựa chọn cục bộ tốt nhất**, chứng minh rằng lựa chọn đó bảo toàn tài nguyên quan trọng cho các bước sau, rồi xử lý toàn bộ dãy trong một lần duyệt.
