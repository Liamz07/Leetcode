# Teemo Attacking — LeetCode

## 1. Tổng quan bài toán

Bài toán **Teemo Attacking** yêu cầu tính tổng thời gian mà một mục tiêu bị **đầu độc** sau một chuỗi các lần Teemo tấn công.

Ta được cho:

```cpp
vector<int> timeSeries
```

Trong đó:

```text
timeSeries[i]
```

là thời điểm Teemo thực hiện đòn đánh thứ `i`.

Mỗi đòn đánh gây hiệu ứng độc trong:

```text
duration
```

đơn vị thời gian.

Nếu một đòn đánh xảy ra khi mục tiêu đang bị độc, hiệu ứng độc sẽ được **reset / gia hạn**.

Mục tiêu của bài toán:

> Tính tổng số đơn vị thời gian mà mục tiêu thực sự bị poisoned.

---

# 2. Ví dụ trực quan

Giả sử:

```text
timeSeries = [1, 4]
duration = 2
```

Đòn đánh tại thời điểm `1` gây độc:

```text
[1, 3)
```

Đòn đánh tiếp theo tại thời điểm `4` gây độc:

```text
[4, 6)
```

Hai khoảng không giao nhau.

Vì vậy tổng thời gian bị độc là:

```text
2 + 2 = 4
```

---

## Ví dụ có chồng lấn

```text
timeSeries = [1, 2]
duration = 2
```

Đòn đánh tại `1`:

```text
[1, 3)
```

Đòn đánh tại `2`:

```text
[2, 4)
```

Hai khoảng này chồng lên nhau.

Không thể cộng:

```text
2 + 2 = 4
```

vì khoảng:

```text
[2, 3)
```

bị tính hai lần.

Khoảng poisoned thực tế là:

```text
[1, 4)
```

nên tổng thời gian là:

```text
3
```

Đây chính là điểm quan trọng nhất của bài toán.

---

# 3. Mô hình hóa bài toán

Mỗi lần tấn công tại thời điểm:

```text
t
```

sẽ tạo ra một khoảng poisoned:

```text
[t, t + duration)
```

Do đó bài toán có thể được nhìn dưới dạng:

> Tính tổng độ dài của hợp các khoảng thời gian.

Ví dụ:

```text
timeSeries = [1, 2, 5]
duration = 3
```

Các khoảng:

```text
Attack 1: [1, 4)
Attack 2: [2, 5)
Attack 3: [5, 8)
```

Hợp của chúng là:

```text
[1, 8)
```

Tổng thời gian:

```text
7
```

---

# 4. Quan sát quan trọng nhất

Giả sử ta đang xét hai lần tấn công liên tiếp:

```text
timeSeries[i]
timeSeries[i + 1]
```

Đặt:

```text
gap = timeSeries[i + 1] - timeSeries[i]
```

Có hai trường hợp.

---

## Trường hợp 1: Hai lần đánh cách nhau ít hơn `duration`

Nếu:

```text
gap < duration
```

thì đòn đánh tiếp theo xảy ra khi hiệu ứng độc cũ vẫn còn.

Ví dụ:

```text
timeSeries[i]     = 1
timeSeries[i + 1] = 3
duration          = 5
```

Khoảng đầu tiên:

```text
[1, 6)
```

Khoảng thứ hai:

```text
[3, 8)
```

Phần thời gian mới được bổ sung chỉ là:

```text
[6, 8)
```

có độ dài:

```text
2
```

Chính bằng:

```text
timeSeries[i + 1] - timeSeries[i]
```

Do đó khi:

```text
gap < duration
```

ta cộng:

```text
gap
```

---

## Trường hợp 2: Hai lần đánh cách nhau ít nhất `duration`

Nếu:

```text
gap >= duration
```

thì hiệu ứng độc đầu tiên đã kết thúc trước khi đòn đánh tiếp theo xảy ra.

Ví dụ:

```text
timeSeries[i]     = 1
timeSeries[i + 1] = 7
duration          = 5
```

Khoảng đầu tiên:

```text
[1, 6)
```

Khoảng thứ hai:

```text
[7, 12)
```

Không có phần giao nhau.

Do đó ta có thể cộng toàn bộ:

```text
duration
```

---

# 5. Công thức quan trọng

Với hai lần tấn công liên tiếp:

```text
current = timeSeries[i]
next    = timeSeries[i + 1]
```

Thời gian poisoned mới được tạo ra bởi đòn đánh tại `current` là:

```text
min(duration, next - current)
```

Đây chính là công thức cốt lõi của bài toán.

Sau khi xử lý tất cả các cặp liên tiếp, cần cộng thêm `duration` cho đòn đánh cuối cùng.

Công thức tổng quát:

```text
answer =
    Σ min(duration, timeSeries[i + 1] - timeSeries[i])
    + duration
```

với:

```text
i = 0 ... n - 2
```

---

# 6. Vì sao công thức `min()` đúng?

Đây là phần cần hiểu thật chắc.

Giả sử một đòn đánh xảy ra tại:

```text
t
```

và hiệu ứng kéo dài:

```text
duration
```

Nếu không có đòn đánh tiếp theo, đòn đánh này đóng góp:

```text
duration
```

đơn vị thời gian.

Nhưng nếu đòn tiếp theo xảy ra sau:

```text
gap
```

đơn vị thời gian, thì trước khi đòn tiếp theo xuất hiện, ta chỉ cần tính phần thời gian:

```text
gap
```

Sau đó đòn tiếp theo sẽ tiếp tục / reset hiệu ứng.

Vì vậy phần đóng góp của đòn đánh hiện tại không thể vượt quá `duration`:

```text
min(gap, duration)
```

Có thể hiểu như sau:

```text
Nếu gap nhỏ:
    đóng góp = gap

Nếu gap lớn:
    đóng góp = duration
```

Hay viết:

```text
min(gap, duration)
```

---

# 7. Ví dụ chi tiết

Xét:

```text
timeSeries = [1, 2, 7]
duration = 5
```

Ta xử lý từng cặp.

---

## Cặp 1 → 2

```text
gap = 2 - 1 = 1
```

So sánh:

```text
1 < 5
```

nên:

```text
contribution = min(5, 1)
              = 1
```

Tổng hiện tại:

```text
answer = 1
```

---

## Cặp 2 → 7

```text
gap = 7 - 2 = 5
```

Ta có:

```text
gap >= duration
```

nên:

```text
contribution = min(5, 5)
              = 5
```

Tổng:

```text
answer = 1 + 5
       = 6
```

---

## Đòn đánh cuối cùng

Đòn đánh cuối cùng xảy ra tại:

```text
7
```

Nó luôn tạo ra:

```text
duration = 5
```

đơn vị poisoned.

Do đó:

```text
answer = 6 + 5
       = 11
```

---

# 8. Kiểm tra bằng cách vẽ timeline

Với:

```text
timeSeries = [1, 2, 7]
duration = 5
```

Ta có:

```text
Attack at 1:
1 ---------------- 6
██████████████████

Attack at 2:
  2 ---------------- 7
  ██████████████████

Attack at 7:
                    7 ---------------- 12
                    ██████████████████
```

Khoảng poisoned thực tế:

```text
[1, 7) + [7, 12)
```

Tổng:

```text
6 + 5 = 11
```

Công thức cho kết quả đúng:

```text
min(5, 2 - 1)
+ min(5, 7 - 2)
+ 5

= 1 + 5 + 5
= 11
```

---

# 9. Vì sao không cần mô phỏng từng đơn vị thời gian?

Một cách rất trực tiếp nhưng không tốt là mô phỏng:

```text
time = 1
time = 2
time = 3
...
```

hoặc đánh dấu từng thời điểm bị poisoned.

Cách này không cần thiết.

Ta không quan tâm từng thời điểm riêng lẻ.

Ta chỉ cần biết:

> Hai lần tấn công cách nhau bao nhiêu?

Nếu khoảng cách là `gap`, đóng góp chỉ đơn giản là:

```text
min(gap, duration)
```

Do đó có thể giải bài toán bằng một lần duyệt mảng.

---

# 10. Các phương pháp có thể nghĩ tới

## Cách 1 — Mô phỏng từng khoảng thời gian

Ta có thể tạo một tập hợp các thời điểm bị poisoned.

Ví dụ:

```text
timeSeries = [1, 2]
duration = 3
```

Đòn đánh tại `1`:

```text
1, 2, 3
```

Đòn đánh tại `2`:

```text
2, 3, 4
```

Sau đó dùng `set` để loại duplicate.

Cách này có thể cho đáp án đúng, nhưng không cần thiết và có thể tốn rất nhiều bộ nhớ nếu `duration` lớn.

---

## Cách 2 — Tạo các interval rồi merge

Ta có thể tạo:

```text
[t, t + duration)
```

cho mỗi lần đánh, sau đó merge các khoảng giao nhau.

Đây là một cách giải tổng quát cho bài toán union of intervals.

Nhưng với bài Teemo Attacking, các thời điểm đã được cho theo thứ tự tăng dần và tất cả các khoảng đều có cùng độ dài `duration`.

Do đó ta không cần triển khai đầy đủ thuật toán interval merging.

Ta chỉ cần xét hai lần tấn công liên tiếp.

---

## Cách 3 — One Pass với `min()`

Đây là cách đơn giản và hiệu quả nhất.

Với mỗi:

```text
i
```

ta tính:

```text
min(duration, timeSeries[i + 1] - timeSeries[i])
```

Cuối cùng cộng:

```text
duration
```

cho đòn đánh cuối.

Đây là hướng tiếp cận nên sử dụng.

---

# 11. Thuật toán

Giả sử:

```text
n = timeSeries.size()
```

### Bước 1

Khởi tạo:

```cpp
int total = 0;
```

### Bước 2

Duyệt:

```cpp
i = 0 → n - 2
```

### Bước 3

Tính khoảng cách giữa hai lần đánh:

```cpp
int gap = timeSeries[i + 1] - timeSeries[i];
```

### Bước 4

Cộng phần thời gian poisoned thực sự:

```cpp
total += min(duration, gap);
```

### Bước 5

Sau vòng lặp, cộng thời gian của đòn đánh cuối:

```cpp
total += duration;
```

### Bước 6

Trả về `total`.

---

# 12. C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findPoisonedDuration(vector<int>& timeSeries, int duration) {
        int total = 0;
        int n = timeSeries.size();

        for (int i = 0; i < n - 1; ++i) {
            int gap = timeSeries[i + 1] - timeSeries[i];

            total += min(duration, gap);
        }

        total += duration;

        return total;
    }
};
```

---

# 13. Giải thích từng dòng code

## Khai báo hàm

```cpp
int findPoisonedDuration(vector<int>& timeSeries, int duration)
```

Hàm nhận:

- `timeSeries`: các thời điểm tấn công.
- `duration`: thời gian hiệu ứng độc sau mỗi đòn đánh.

Trả về:

```text
tổng thời gian bị poisoned
```

---

## Khởi tạo đáp án

```cpp
int total = 0;
```

Biến `total` lưu tổng thời gian poisoned đã tính được.

---

## Lấy số lượng lần tấn công

```cpp
int n = timeSeries.size();
```

---

## Duyệt các cặp liên tiếp

```cpp
for (int i = 0; i < n - 1; ++i)
```

Tại mỗi `i`, ta xét:

```text
timeSeries[i]
```

và:

```text
timeSeries[i + 1]
```

Không cần xử lý đòn cuối trong vòng lặp vì đòn cuối không có đòn đánh tiếp theo để so sánh.

---

## Tính khoảng cách

```cpp
int gap = timeSeries[i + 1] - timeSeries[i];
```

Ví dụ:

```text
timeSeries = [1, 4, 8]
```

thì:

```text
i = 0 → gap = 4 - 1 = 3
i = 1 → gap = 8 - 4 = 4
```

---

## Cộng phần đóng góp

```cpp
total += min(duration, gap);
```

Đây là dòng quan trọng nhất.

Nếu:

```text
gap < duration
```

thì:

```text
min(duration, gap) = gap
```

Nếu:

```text
gap >= duration
```

thì:

```text
min(duration, gap) = duration
```

---

## Xử lý đòn đánh cuối

```cpp
total += duration;
```

Đòn cuối không có đòn tiếp theo để cắt ngắn khoảng poisoned.

Do đó nó luôn đóng góp đầy đủ:

```text
duration
```

---

# 14. Dry Run chi tiết

Xét:

```text
timeSeries = [1, 4, 5]
duration = 3
```

Ban đầu:

```text
total = 0
```

---

## i = 0

```text
current = 1
next = 4

gap = 4 - 1 = 3
```

Do:

```text
min(3, 3) = 3
```

nên:

```text
total = 3
```

---

## i = 1

```text
current = 4
next = 5

gap = 5 - 4 = 1
```

Do:

```text
min(3, 1) = 1
```

nên:

```text
total = 4
```

---

## Sau vòng lặp

Cộng đòn cuối:

```text
total += 3
```

Kết quả:

```text
total = 7
```

---

# 15. Kiểm tra bằng timeline

Các khoảng:

```text
Attack at 1 → [1, 4)
Attack at 4 → [4, 7)
Attack at 5 → [5, 8)
```

Hai khoảng đầu nối tiếp nhau:

```text
[1, 4) + [4, 7)
```

Khoảng thứ ba chồng lên khoảng thứ hai.

Hợp của tất cả các khoảng là:

```text
[1, 8)
```

Độ dài:

```text
8 - 1 = 7
```

Khớp với thuật toán.

---

# 16. Edge Cases

## Case 1 — Chỉ có một lần tấn công

Ví dụ:

```text
timeSeries = [5]
duration = 10
```

Không có cặp nào để xét.

Vòng lặp không chạy.

Sau đó:

```cpp
total += duration;
```

Kết quả:

```text
10
```

Hoàn toàn đúng.

---

## Case 2 — Hai lần tấn công rất gần nhau

```text
timeSeries = [1, 2]
duration = 100
```

Ta có:

```text
gap = 1
```

Nên:

```text
answer = 1 + 100
       = 101
```

Không phải:

```text
200
```

vì hai hiệu ứng độc chồng lên nhau gần như hoàn toàn.

---

## Case 3 — Hai lần tấn công cách xa nhau

```text
timeSeries = [1, 100]
duration = 5
```

Ta có:

```text
gap = 99
```

Vì:

```text
99 > 5
```

nên:

```text
answer = 5 + 5
       = 10
```

---

## Case 4 — Khoảng cách đúng bằng `duration`

```text
timeSeries = [1, 6]
duration = 5
```

Các khoảng:

```text
[1, 6)
[6, 11)
```

Chúng chỉ chạm nhau tại điểm `6`, nhưng không có phần thời gian chung.

Do đó:

```text
answer = 5 + 5 = 10
```

Công thức:

```cpp
min(5, 6 - 1)
```

cho:

```text
5
```

nên hoàn toàn chính xác.

---

# 17. Một chi tiết toán học quan trọng: khoảng `[start, end)`

Ta có thể xem hiệu ứng độc là khoảng:

```text
[start, start + duration)
```

Dùng interval nửa kín giúp tránh nhầm lẫn ở trường hợp:

```text
next - current == duration
```

Ví dụ:

```text
duration = 5

Attack 1: [1, 6)
Attack 2: [6, 11)
```

Điểm `6` không đại diện cho một khoảng thời gian có độ dài dương.

Hai interval không overlap.

Vì vậy tổng là:

```text
5 + 5 = 10
```

Đây cũng là lý do công thức:

```cpp
min(duration, gap)
```

hoạt động chính xác.

---

# 18. Chứng minh tính đúng đắn

Ta chứng minh thuật toán bằng cách xét từng đòn đánh, ngoại trừ đòn cuối.

Giả sử đòn đánh thứ `i` xảy ra tại:

```text
t_i
```

và đòn tiếp theo xảy ra tại:

```text
t_{i+1}
```

Đòn đánh thứ `i` có thể tạo hiệu ứng tối đa:

```text
duration
```

đơn vị thời gian.

Tuy nhiên đòn đánh tiếp theo xuất hiện sau:

```text
t_{i+1} - t_i
```

đơn vị thời gian và tại thời điểm đó hiệu ứng được reset.

Do đó đóng góp thực tế của đòn đánh `i` là:

```text
min(duration, t_{i+1} - t_i)
```

Thuật toán cộng chính xác giá trị này cho mọi đòn đánh từ `0` đến `n - 2`.

Đối với đòn đánh cuối cùng, không có đòn đánh nào phía sau để reset hiệu ứng, nên nó luôn đóng góp đầy đủ:

```text
duration
```

Vì vậy tổng mà thuật toán tính được chính xác bằng tổng thời gian poisoned.

---

# 19. Độ phức tạp

Gọi:

```text
n = timeSeries.size()
```

## Time Complexity

Ta duyệt mảng đúng một lần:

```text
O(n)
```

Mỗi bước chỉ thực hiện:

- phép trừ;
- `min`;
- phép cộng.

Tất cả đều là:

```text
O(1)
```

Vì vậy:

```text
Time Complexity = O(n)
```

---

## Space Complexity

Ngoài mảng đầu vào, ta chỉ sử dụng một vài biến:

```cpp
total
n
gap
i
```

Do đó:

```text
Space Complexity = O(1)
```

Đây là thuật toán rất tối ưu:

```text
Time  = O(n)
Space = O(1)
```

---

# 20. Có thể viết ngắn hơn

Nếu muốn code ngắn gọn:

```cpp
class Solution {
public:
    int findPoisonedDuration(vector<int>& timeSeries, int duration) {
        int ans = duration;

        for (int i = 1; i < timeSeries.size(); ++i) {
            ans += min(duration, timeSeries[i] - timeSeries[i - 1]);
        }

        return ans;
    }
};
```

Ý tưởng hoàn toàn giống nhau.

Ta khởi tạo:

```cpp
ans = duration;
```

vì đòn đánh cuối cùng chắc chắn đóng góp `duration`.

Sau đó với mỗi cặp liên tiếp, cộng:

```cpp
min(duration, timeSeries[i] - timeSeries[i - 1]);
```

Cách này khá đẹp khi đã hiểu rõ công thức.

---

# 21. So sánh hai cách viết

## Cách 1 — Dễ đọc

```cpp
int total = 0;

for (int i = 0; i < n - 1; ++i) {
    int gap = timeSeries[i + 1] - timeSeries[i];
    total += min(duration, gap);
}

total += duration;
```

Ưu điểm:

- Dễ debug.
- Dễ giải thích.
- Tách rõ trường hợp đòn cuối.

---

## Cách 2 — Gọn hơn

```cpp
int ans = duration;

for (int i = 1; i < n; ++i) {
    ans += min(duration, timeSeries[i] - timeSeries[i - 1]);
}
```

Ưu điểm:

- Code ngắn.
- Trực tiếp thể hiện công thức.
- Vẫn `O(n)` và `O(1)`.

Khi làm bài LeetCode, cả hai đều tốt.

---

# 22. Những lỗi thường gặp

## Lỗi 1 — Luôn cộng `duration`

Một lời giải sai phổ biến:

```cpp
for (int i = 0; i < n; ++i) {
    total += duration;
}
```

Điều này tương đương với giả định rằng mọi hiệu ứng độc đều độc lập.

Ví dụ:

```text
timeSeries = [1, 2]
duration = 5
```

Code trên cho:

```text
10
```

nhưng đáp án đúng là:

```text
6
```

vì hai hiệu ứng overlap.

---

## Lỗi 2 — Luôn cộng khoảng cách

Một lỗi khác:

```cpp
total += timeSeries[i + 1] - timeSeries[i];
```

Cách này cũng sai khi khoảng cách lớn hơn `duration`.

Ví dụ:

```text
timeSeries = [1, 100]
duration = 5
```

Nếu cộng khoảng cách:

```text
99
```

nhưng đòn đầu chỉ kéo dài:

```text
5
```

Do đó phải dùng:

```cpp
min(duration, gap)
```

---

## Lỗi 3 — Quên đòn cuối

Nếu chỉ viết:

```cpp
for (int i = 0; i < n - 1; ++i) {
    total += min(duration,
                 timeSeries[i + 1] - timeSeries[i]);
}
```

thì ta chưa tính thời gian poisoned của đòn đánh cuối.

Phải thêm:

```cpp
total += duration;
```

---

## Lỗi 4 — Dùng `i + 1` vượt quá mảng

Nếu viết:

```cpp
for (int i = 0; i < n; ++i) {
    timeSeries[i + 1]
}
```

thì khi:

```text
i = n - 1
```

ta truy cập:

```text
timeSeries[n]
```

nằm ngoài mảng.

Do đó nếu xét cặp:

```text
i
i + 1
```

thì vòng lặp phải kết thúc ở:

```text
n - 2
```

hoặc viết dạng:

```cpp
for (int i = 1; i < n; ++i)
```

và so sánh với:

```text
i - 1
```

---

# 23. Pattern quan trọng cần ghi nhớ

Bài **Teemo Attacking** là một ví dụ rất tốt của pattern:

> **Tính tổng độ dài các đoạn có khả năng overlap bằng cách xét khoảng cách giữa các sự kiện liên tiếp.**

Khi gặp một bài có dạng:

```text
Có nhiều sự kiện xảy ra tại các thời điểm t1, t2, t3...
Mỗi sự kiện tạo hiệu ứng trong duration.
Các hiệu ứng có thể overlap.
Cần tính tổng thời gian hiệu ứng thực sự tồn tại.
```

Hãy nghĩ ngay đến:

```cpp
min(duration, nextTime - currentTime)
```

Đây là insight quan trọng nhất.

---

# 24. Tư duy tổng quát

Thay vì suy nghĩ:

> "Mỗi attack gây poison `duration` giây."

Hãy suy nghĩ:

> "Đòn attack hiện tại sẽ đóng góp bao nhiêu thời gian trước khi đòn attack tiếp theo xảy ra?"

Câu hỏi thứ hai dẫn trực tiếp tới:

```text
min(duration, gap)
```

Đây là cách chuyển từ tư duy mô phỏng sang tư duy theo **đoạn thời gian**.

---

# 25. Công thức cần ghi nhớ

Với:

```text
timeSeries = [t0, t1, ..., t(n-1)]
```

và:

```text
duration = D
```

đáp án là:

```text
D
+
min(D, t1 - t0)
+
min(D, t2 - t1)
+
...
+
min(D, t(n-1) - t(n-2))
```

Hay viết gọn:

```text
answer = D + Σ min(D, t[i] - t[i-1])
```

với:

```text
i = 1 ... n - 1
```

---

# 26. Template C++ có thể ghi nhớ

```cpp
class Solution {
public:
    int findPoisonedDuration(vector<int>& timeSeries, int duration) {
        int ans = duration;

        for (int i = 1; i < timeSeries.size(); ++i) {
            ans += min(duration,
                       timeSeries[i] - timeSeries[i - 1]);
        }

        return ans;
    }
};
```

Pattern:

```text
answer = contribution_of_last_event

for each pair of consecutive events:
    answer += min(effect_duration, time_gap)
```

---

# 27. Kết luận

Lời giải tối ưu của **Teemo Attacking** không cần mô phỏng từng giây và cũng không cần tạo danh sách tất cả các khoảng thời gian bị poison.

Chỉ cần quan sát khoảng cách giữa hai lần tấn công liên tiếp:

```text
gap = timeSeries[i] - timeSeries[i - 1]
```

Đóng góp của đòn trước là:

```text
min(duration, gap)
```

Cuối cùng cộng đầy đủ:

```text
duration
```

cho đòn đánh cuối.

Độ phức tạp:

```text
Time Complexity:  O(n)
Space Complexity: O(1)
```

Đây là lời giải vừa tối ưu vừa thể hiện đúng bản chất của bài toán: **tính độ dài của hợp các khoảng thời gian bằng cách xử lý phần overlap thông qua khoảng cách giữa các sự kiện liên tiếp**.
