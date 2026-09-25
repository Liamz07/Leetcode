# Happy Number — Phương hướng tiếp cận và lời giải C++ tối ưu

## 1. Bài toán

**LeetCode 202 — Happy Number**

Cho một số nguyên dương `n`.

Lặp lại phép biến đổi:

> Thay `n` bằng tổng bình phương các chữ số của `n`.

Nếu cuối cùng đạt `1` thì `n` là **Happy Number**. Nếu quá trình rơi vào một vòng lặp không chứa `1`, thì `n` không phải Happy Number.

Ví dụ:

```text
19
→ 1² + 9²
→ 82
→ 8² + 2²
→ 68
→ 6² + 8²
→ 100
→ 1
```

Vì vậy `19` là Happy Number.

---

## 2. Ví dụ không Happy

Xét `2`:

```text
2
→ 4
→ 16
→ 37
→ 58
→ 89
→ 145
→ 42
→ 20
→ 4
→ ...
```

Ta thấy:

```text
4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4
```

được lặp lại.

Cycle này không chứa `1`, nên:

```text
2 không phải Happy Number
```

Điểm quan trọng là nếu chỉ viết:

```cpp
while (n != 1) {
    n = getNext(n);
}
```

thì với số không Happy, chương trình có thể chạy vô hạn.

Vì vậy bài toán thực chất là một bài **Cycle Detection**.

---

# 3. Biến bài toán thành một hàm chuyển trạng thái

Định nghĩa:

```text
f(n) = tổng bình phương các chữ số của n
```

Ví dụ:

```text
f(19) = 1² + 9² = 82
f(82) = 8² + 2² = 68
f(68) = 6² + 8² = 100
f(100) = 1² + 0² + 0² = 1
```

Do đó bài toán tạo ra một dãy:

```text
n
↓
f(n)
↓
f(f(n))
↓
f(f(f(n)))
↓
...
```

Có hai khả năng:

### Trường hợp 1 — Happy

```text
n → ... → 1
```

### Trường hợp 2 — Không Happy

```text
n → ... → x → ... → x
```

Tức là dãy đi vào một cycle không chứa `1`.

---

# 4. Vì sao chắc chắn xuất hiện cycle?

Đây là insight quan trọng.

Giả sử `n` có `d` chữ số.

Tổng bình phương các chữ số lớn nhất là:

```text
d × 9² = 81d
```

Trong khi:

```text
n ≥ 10^(d-1)
```

khi `d` đủ lớn.

Vì vậy sau phép biến đổi, số có thể giảm rất mạnh.

Với constraint của LeetCode:

```text
1 <= n <= 2^31 - 1
```

`n` có tối đa 10 chữ số.

Do đó:

```text
f(n) <= 10 × 9² = 810
```

Sau **một lần biến đổi**, trạng thái đã nằm trong:

```text
[1, 810]
```

Từ đó trở đi chỉ có tối đa 810 trạng thái khác nhau.

Một dãy vô hạn chỉ sử dụng một tập hữu hạn trạng thái chắc chắn phải lặp lại một trạng thái.

Khi một trạng thái lặp lại:

```text
x → ... → x
```

thì từ đó toàn bộ quá trình lặp lại.

Đó chính là cycle.

---

# 5. Cách tiếp cận đầu tiên: Hash Set

Ta có thể lưu mọi trạng thái đã gặp.

Pseudo-code:

```text
seen = {}

while n != 1:
    nếu n đã có trong seen:
        return false

    thêm n vào seen
    n = f(n)

return true
```

C++:

```cpp
class Solution {
public:
    int getNext(int n) {
        int sum = 0;

        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }

        return sum;
    }

    bool isHappy(int n) {
        unordered_set<int> seen;

        while (n != 1 && !seen.count(n)) {
            seen.insert(n);
            n = getNext(n);
        }

        return n == 1;
    }
};
```

Đây là lời giải đúng và rất dễ hiểu.

Nhưng nó dùng thêm bộ nhớ để lưu các trạng thái:

```text
Space = O(k)
```

Trong đó `k` là số trạng thái đi qua trước khi gặp cycle.

Ta có thể tối ưu phần space bằng Floyd.

---

# 6. Cách tiếp cận tối ưu: Floyd's Cycle Detection

Floyd's Cycle Detection còn được gọi là:

> **Tortoise and Hare**

Ta sử dụng hai con trỏ:

```text
slow
fast
```

Quy tắc:

```cpp
slow = f(slow);
fast = f(f(fast));
```

Tức là:

- `slow` đi 1 bước.
- `fast` đi 2 bước.

Trong code:

```cpp
slow = getNext(slow);
fast = getNext(getNext(fast));
```

---

# 7. Tại sao Floyd phát hiện được cycle?

Giả sử dãy có dạng:

```text
A → B → C → D → E
            ↓   ↑
            F → G
```

Sau khi cả hai con trỏ đi vào cycle:

```text
slow → ...
fast → ...
```

`fast` chạy nhanh gấp đôi `slow`.

Khoảng cách giữa hai con trỏ thay đổi tuần hoàn theo độ dài cycle.

Cuối cùng chúng bắt buộc gặp nhau:

```text
slow == fast
```

Đây là nguyên lý của Floyd's Cycle Detection.

Điểm hay là:

> Không cần lưu toàn bộ các trạng thái đã gặp.

Vì vậy extra space chỉ là:

```text
O(1)
```

---

# 8. Áp dụng Floyd vào Happy Number

Ta khởi tạo:

```cpp
int slow = n;
int fast = n;
```

Sau đó:

```cpp
do {
    slow = getNext(slow);
    fast = getNext(getNext(fast));
} while (slow != fast);
```

Khi hai con trỏ gặp nhau, có hai trường hợp.

### Trường hợp 1

```text
slow == fast == 1
```

Nghĩa là cycle là:

```text
1 → 1 → 1 → ...
```

Số là Happy.

### Trường hợp 2

```text
slow == fast == x
```

với:

```text
x != 1
```

Nghĩa là đã rơi vào một cycle khác `1`.

Số không Happy.

Do đó:

```cpp
return slow == 1;
```

---

# 9. Lời giải C++ tối ưu

```cpp
class Solution {
public:
    int getNext(int n) {
        int sum = 0;

        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }

        return sum;
    }

    bool isHappy(int n) {
        int slow = n;
        int fast = n;

        do {
            slow = getNext(slow);
            fast = getNext(getNext(fast));
        } while (slow != fast);

        return slow == 1;
    }
};
```

Đây là lời giải nên ưu tiên khi muốn tối ưu **extra space**.

---

# 10. Giải thích chi tiết `getNext`

Code:

```cpp
int getNext(int n) {
    int sum = 0;

    while (n > 0) {
        int digit = n % 10;
        sum += digit * digit;
        n /= 10;
    }

    return sum;
}
```

Mục tiêu:

```text
getNext(19) = 1² + 9² = 82
```

## Bước 1 — Lấy chữ số cuối

```cpp
int digit = n % 10;
```

Ví dụ:

```text
19 % 10 = 9
```

## Bước 2 — Bình phương và cộng

```cpp
sum += digit * digit;
```

Khi `digit = 9`:

```text
sum += 81
```

## Bước 3 — Xóa chữ số cuối

```cpp
n /= 10;
```

Ví dụ:

```text
19 / 10 = 1
```

## Bước 4 — Lặp lại

```text
n = 19
digit = 9
sum = 81
n = 1

digit = 1
sum = 82
n = 0
```

Kết quả:

```text
getNext(19) = 82
```

---

# 11. Dry Run Floyd với `19`

Ban đầu:

```text
slow = 19
fast = 19
```

### Vòng 1

```text
slow = f(19) = 82

fast = f(f(19))
     = f(82)
     = 68
```

Kết quả:

```text
slow = 82
fast = 68
```

### Vòng 2

```text
slow = f(82) = 68

fast = f(f(68))
     = f(100)
     = 1
```

Kết quả:

```text
slow = 68
fast = 1
```

### Vòng 3

```text
slow = f(68) = 100

fast = f(f(1))
     = 1
```

### Vòng 4

```text
slow = f(100) = 1

fast = f(f(1))
     = 1
```

Bây giờ:

```text
slow == fast == 1
```

Dừng và trả:

```cpp
true
```

---

# 12. Dry Run với `2`

Dãy đầy đủ:

```text
2
→ 4
→ 16
→ 37
→ 58
→ 89
→ 145
→ 42
→ 20
→ 4
→ ...
```

Cycle:

```text
4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4
```

Floyd sẽ đưa `slow` và `fast` vào cycle.

Sau một số bước:

```text
slow == fast
```

nhưng giá trị gặp nhau không phải `1`.

Do đó:

```cpp
return slow == 1;
```

cho:

```text
false
```

---

# 13. Tại sao `do-while`?

Ta khởi tạo:

```cpp
int slow = n;
int fast = n;
```

nên ngay lập tức:

```text
slow == fast
```

Nếu viết:

```cpp
while (slow != fast) {
    ...
}
```

thì vòng lặp sẽ không chạy lần nào.

Do đó dùng:

```cpp
do {
    ...
} while (slow != fast);
```

để hai con trỏ thực hiện ít nhất một bước trước khi so sánh.

Một cách khác cũng đúng:

```cpp
int slow = getNext(n);
int fast = getNext(getNext(n));

while (slow != fast) {
    slow = getNext(slow);
    fast = getNext(getNext(fast));
}
```

Nhưng `do-while` gọn hơn.

---

# 14. Tại sao không cần kiểm tra `fast == 1` riêng?

Một số lời giải viết logic khác và kiểm tra:

```cpp
if (slow == 1 || fast == 1)
```

Nhưng với implementation Floyd ở trên, không cần.

Lý do:

```text
1 → 1 → 1 → ...
```

`1` tự tạo thành một cycle có độ dài 1.

Nếu số là Happy, cả `slow` và `fast` cuối cùng sẽ gặp nhau tại:

```text
1
```

Vì vậy chỉ cần:

```cpp
return slow == 1;
```

---

# 15. Chứng minh tính đúng đắn

Ta chứng minh algorithm trả kết quả chính xác.

## Trường hợp 1: `n` là Happy Number

Theo định nghĩa:

```text
n → ... → 1
```

Và:

```text
f(1) = 1
```

nên `1` tạo thành cycle:

```text
1 → 1 → 1 → ...
```

Floyd sẽ phát hiện cycle và hai con trỏ gặp nhau tại:

```text
1
```

Do đó:

```cpp
slow == 1
```

và thuật toán trả `true`.

---

## Trường hợp 2: `n` không Happy

Dãy không đi tới `1`.

Sau một số hữu hạn bước, trạng thái nằm trong tập hữu hạn:

```text
1..810
```

nên phải có một trạng thái lặp lại.

Do đó dãy đi vào một cycle:

```text
x → ... → x
```

với:

```text
x != 1
```

Floyd chắc chắn phát hiện cycle.

Khi đó:

```text
slow == fast
```

tại một phần tử trong cycle và phần tử đó không phải `1`.

Vì vậy:

```cpp
return slow == 1;
```

trả `false`.

---

## Kết luận

Cả hai trường hợp đều được xử lý chính xác.

Vì vậy thuật toán là đúng.

---

# 16. Độ phức tạp

Gọi:

```text
d = số chữ số của trạng thái hiện tại
```

Mỗi lần gọi:

```cpp
getNext(n)
```

cần duyệt qua các chữ số:

```text
Time = O(d)
```

Floyd thực hiện một số lượng hữu hạn bước trước khi gặp cycle.

Nếu gọi `k` là số bước cần đi qua, ta có thể viết:

```text
Time = O(k × d)
```

Với constraint:

```text
n <= 2^31 - 1
```

thì số chữ số ban đầu tối đa là `10`.

Sau một lần biến đổi:

```text
n <= 810
```

nên các trạng thái sau đó có tối đa 3 chữ số.

Do đó trên thực tế bài chạy cực nhanh.

### Space

Chỉ dùng:

```cpp
slow
fast
sum
digit
```

nên:

```text
Space = O(1)
```

Đây là ưu điểm chính của Floyd.

---

# 17. So sánh Hash Set và Floyd

| Tiêu chí | Hash Set | Floyd |
|---|---|---|
| Cách phát hiện cycle | Lưu trạng thái | Hai con trỏ |
| Time | O(k × d) | O(k × d) |
| Extra Space | O(k) | **O(1)** |
| Dễ hiểu | Rất dễ | Cần hiểu cycle detection |
| Tổng quát | Tốt | Tốt |
| Phù hợp yêu cầu O(1) space | Không | **Có** |

Cả hai đều là lời giải tốt.

Nếu đề chỉ yêu cầu code đúng, Hash Set rất trực quan.

Nếu đề hỏi:

> "Can you solve it using O(1) extra space?"

thì Floyd là lựa chọn chuẩn.

---

# 18. Một cách khác: Hard-code cycle `4`

Ta biết với Happy Number, mọi số không Happy cuối cùng đi vào cycle:

```text
4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4
```

Do đó có thể viết:

```cpp
class Solution {
public:
    int getNext(int n) {
        int sum = 0;

        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }

        return sum;
    }

    bool isHappy(int n) {
        while (n != 1 && n != 4) {
            n = getNext(n);
        }

        return n == 1;
    }
};
```

Đây cũng có:

```text
Space = O(1)
```

và rất ngắn.

Tuy nhiên, đây là một optimization dựa trên kiến thức đặc thù của bài.

Floyd có tính tổng quát hơn vì:

> Không cần biết trước cycle là gì.

---

# 19. Vì sao không nên chỉ hard-code `4` trong tư duy thuật toán?

Code:

```cpp
while (n != 1 && n != 4)
```

rất đẹp.

Nhưng khi học thuật toán, điều quan trọng hơn là nhận ra pattern:

```text
Repeated transformation
        ↓
Possible cycle
        ↓
Cycle detection
```

Nếu gặp một bài khác mà cycle không bắt đầu tại `4`, hard-code sẽ không còn dùng được.

Trong khi Floyd vẫn áp dụng:

```cpp
slow = next(slow);
fast = next(next(fast));
```

Đây là lý do nên hiểu Floyd trước, rồi mới xem hard-code như một trick riêng của bài.

---

# 20. Functional Graph — góc nhìn sâu hơn

Happy Number có thể được mô hình hóa thành một **functional graph**.

Mỗi trạng thái có đúng một trạng thái kế tiếp:

```text
n → f(n)
```

Do mỗi node chỉ có một outgoing edge, một đường đi bất kỳ cuối cùng có dạng:

```text
tail → cycle
```

Ví dụ với `2`:

```text
2
↓
4
↓
16
↓
37
↓
58
↓
89
↓
145
↓
42
↓
20
└────────→ 4
```

Ta có:

```text
Tail:
2

Cycle:
4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4
```

Với `19`:

```text
19 → 82 → 68 → 100 → 1
                       ↑
                       └── 1
```

`1` chính là cycle độ dài `1`.

Nhìn bài theo functional graph giúp giải thích tại sao Cycle Detection là hướng tiếp cận tự nhiên.

---

# 21. Pattern quan trọng cần ghi nhớ

Khi gặp một bài có dạng:

```text
state
  ↓
transform(state)
  ↓
state mới
  ↓
transform(...)
  ↓
...
```

hãy tự hỏi:

> "Liệu state có thể lặp lại không?"

Nếu có, hãy nghĩ tới:

```text
Hash Set
```

hoặc:

```text
Floyd's Cycle Detection
```

Một số bài có pattern tương tự:

- Linked List Cycle
- Find the Duplicate Number
- Happy Number
- Một số bài Functional Graph
- Một số bài mô phỏng trạng thái lặp

---

# 22. Template Floyd tổng quát

Template cơ bản:

```cpp
slow = start;
fast = start;

do {
    slow = next(slow);
    fast = next(next(fast));
} while (slow != fast);
```

Sau khi phát hiện cycle, tùy bài có thể:

- Kiểm tra cycle có chứa một trạng thái đặc biệt.
- Tìm điểm bắt đầu cycle.
- Tìm độ dài cycle.
- Chỉ đơn giản kiểm tra có cycle hay không.

Trong Happy Number:

```cpp
return slow == 1;
```

là đủ.

---

# 23. Những lỗi thường gặp

## Lỗi 1 — Quên phát hiện cycle

Sai:

```cpp
while (n != 1) {
    n = getNext(n);
}
```

Với `2`, chương trình chạy vô hạn.

---

## Lỗi 2 — Cho `slow` và `fast` chạy cùng tốc độ

Sai:

```cpp
slow = getNext(slow);
fast = getNext(fast);
```

Hai con trỏ đi cùng tốc độ nên không phải Floyd.

Đúng:

```cpp
slow = getNext(slow);
fast = getNext(getNext(fast));
```

---

## Lỗi 3 — Dùng `while` ngay khi `slow = fast = n`

Sai:

```cpp
int slow = n;
int fast = n;

while (slow != fast) {
    ...
}
```

Vòng lặp không chạy vì:

```text
slow == fast
```

Ngay từ đầu.

Dùng:

```cpp
do {
    ...
} while (slow != fast);
```

hoặc khởi tạo sau một bước.

---

## Lỗi 4 — Nghĩ cycle chỉ xảy ra khi `f(n) == n`

Không đúng.

Cycle có thể có nhiều phần tử:

```text
4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4
```

Không có phần tử nào ngoài `4` tự trỏ trực tiếp vào chính nó, nhưng toàn bộ chuỗi vẫn là một cycle.

---

# 24. Cách trình bày khi đi phỏng vấn

Có thể giải thích theo 5 bước:

### Bước 1 — Định nghĩa phép biến đổi

```text
f(n) = tổng bình phương các chữ số
```

### Bước 2 — Nhận ra infinite loop

Nếu không tới `1`, quá trình có thể lặp vô hạn.

### Bước 3 — Nhận ra cycle

Sau một số bước, trạng thái nằm trong miền hữu hạn, nên phải có cycle.

### Bước 4 — Chọn thuật toán

Hash Set là cách đơn giản.

Nếu muốn:

```text
O(1) extra space
```

dùng Floyd.

### Bước 5 — Implement

```cpp
slow = getNext(slow);
fast = getNext(getNext(fast));
```

Cuối cùng:

```cpp
return slow == 1;
```

---

# 25. Lời giải hoàn chỉnh nên dùng

```cpp
class Solution {
public:
    int getNext(int n) {
        int sum = 0;

        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }

        return sum;
    }

    bool isHappy(int n) {
        int slow = n;
        int fast = n;

        do {
            slow = getNext(slow);
            fast = getNext(getNext(fast));
        } while (slow != fast);

        return slow == 1;
    }
};
```

---

# 26. Tóm tắt để ghi nhớ nhanh

```text
Happy Number
     ↓
Repeated transformation
     ↓
Có thể rơi vào cycle
     ↓
Cycle Detection
     ↓
Floyd Tortoise & Hare
     ↓
slow đi 1 bước
fast đi 2 bước
     ↓
slow == fast
     ↓
gặp tại 1 → Happy
gặp tại x != 1 → Not Happy
```

Code cốt lõi:

```cpp
int slow = n;
int fast = n;

do {
    slow = getNext(slow);
    fast = getNext(getNext(fast));
} while (slow != fast);

return slow == 1;
```

Và hàm chuyển trạng thái:

```cpp
int getNext(int n) {
    int sum = 0;

    while (n > 0) {
        int digit = n % 10;
        sum += digit * digit;
        n /= 10;
    }

    return sum;
}
```

**Takeaway quan trọng nhất:**

> Khi một bài liên tục biến đổi một trạng thái và có khả năng lặp vô hạn, hãy nghĩ đến **Cycle Detection**. Nếu muốn `O(1)` extra space, hãy nghĩ đến **Floyd's Tortoise and Hare**.
