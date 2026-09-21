# Last Stone Weight — LeetCode

## 1. Thông tin bài toán

**Bài toán:** [Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)

Cho một mảng `stones`, trong đó:

```text
stones[i]
```

là khối lượng của viên đá thứ `i`.

Mỗi lần thực hiện:

1. Chọn **hai viên đá có khối lượng lớn nhất**.
2. Gọi khối lượng của chúng lần lượt là `x` và `y`, với:
   ```text
   x <= y
   ```
3. Đập hai viên đá vào nhau:
   - Nếu `x == y`: cả hai viên đá bị phá hủy.
   - Nếu `x != y`: viên đá có khối lượng `x` bị phá hủy, viên còn lại có khối lượng:
     ```text
     y - x
     ```
4. Tiếp tục cho đến khi còn nhiều nhất một viên đá.

Trả về khối lượng viên đá cuối cùng. Nếu không còn viên đá nào thì trả về `0`.

---

# 2. Ví dụ

### Ví dụ 1

```text
Input:
stones = [2,7,4,1,8,1]

Output:
1
```

Diễn biến:

```text
[2,7,4,1,8,1]

Chọn 8 và 7:
8 - 7 = 1

[2,4,1,1,1]

Chọn 4 và 2:
4 - 2 = 2

[2,1,1,1]

Chọn 2 và 1:
2 - 1 = 1

[1,1,1]

Chọn 1 và 1:
cả hai biến mất

[1]

Kết quả:
1
```

---

# 3. Phân tích bản chất bài toán

Điểm quan trọng nhất của bài này là:

> **Mỗi lần chúng ta luôn cần lấy hai phần tử lớn nhất.**

Đây là dấu hiệu rất mạnh cho việc sử dụng:

```text
Priority Queue / Heap
```

Cụ thể, chúng ta cần một:

```text
Max Heap
```

để luôn lấy được viên đá lớn nhất một cách nhanh chóng.

Trong C++, cấu trúc phù hợp là:

```cpp
priority_queue<int>
```

Mặc định:

```cpp
priority_queue<int>
```

là **max heap**.

---

# 4. Tại sao dùng Max Heap?

Giả sử:

```text
stones = [2,7,4,1,8,1]
```

Nếu dùng mảng thông thường, để lấy hai phần tử lớn nhất, ta có thể phải:

- Duyệt toàn bộ mảng để tìm phần tử lớn nhất.
- Sau đó lại tìm phần tử lớn thứ hai.
- Xóa chúng.
- Thêm phần tử mới nếu cần.

Việc này khiến mỗi vòng lặp có thể tốn:

```text
O(n)
```

Trong khi bài toán yêu cầu thao tác lấy phần tử lớn nhất **liên tục**.

Max Heap sinh ra chính xác để giải quyết vấn đề này.

---

# 5. Priority Queue hoạt động như thế nào?

Khi viết:

```cpp
priority_queue<int> pq;
```

ta có một cấu trúc dữ liệu mà phần tử lớn nhất luôn nằm ở đầu.

Ví dụ:

```cpp
pq.push(2);
pq.push(7);
pq.push(4);
pq.push(1);
pq.push(8);
pq.push(1);
```

Ta có thể hình dung:

```text
           8
         /   \
        7     4
       / \   /
      1   2  1
```

Phần tử lớn nhất:

```text
8
```

luôn có thể lấy bằng:

```cpp
pq.top()
```

---

# 6. Các thao tác quan trọng của Priority Queue

## `push()`

Thêm một phần tử:

```cpp
pq.push(x);
```

Độ phức tạp:

```text
O(log n)
```

---

## `top()`

Lấy phần tử lớn nhất:

```cpp
pq.top();
```

Độ phức tạp:

```text
O(1)
```

Lưu ý:

```cpp
pq.top()
```

**chỉ xem**, chưa xóa phần tử.

---

## `pop()`

Xóa phần tử lớn nhất:

```cpp
pq.pop();
```

Độ phức tạp:

```text
O(log n)
```

---

# 7. Phương hướng giải quyết

Ta xây dựng thuật toán theo các bước:

```text
Bước 1:
Đưa tất cả stones vào Max Heap.

Bước 2:
Trong khi heap còn ít nhất 2 viên đá:

    - Lấy viên lớn nhất x.
    - Xóa x.

    - Lấy viên lớn thứ hai y.
    - Xóa y.

    - Nếu x == y:
          Không thêm gì.

    - Nếu x != y:
          Thêm x - y vào heap.

Bước 3:
Nếu heap rỗng:
    return 0

Nếu heap còn một phần tử:
    return phần tử đó.
```

---

# 8. Tại sao lấy `x - y`?

Vì ta luôn lấy:

```text
x = viên lớn nhất
y = viên lớn thứ hai
```

nên:

```text
x >= y
```

Sau khi đập:

```text
x - y
```

là khối lượng viên đá còn lại.

Ví dụ:

```text
x = 8
y = 7
```

thì:

```text
8 - 7 = 1
```

Còn lại một viên đá khối lượng `1`.

---

# 9. Tại sao không cần dùng `abs()`?

Ta biết:

```text
x >= y
```

vì `x` là viên lớn nhất và `y` là viên lớn thứ hai.

Do đó:

```cpp
x - y
```

luôn không âm.

Không cần:

```cpp
abs(x - y)
```

Có thể viết:

```cpp
int con_lai = x - y;
```

---

# 10. Mô phỏng thuật toán

Cho:

```text
stones = [2,7,4,1,8,1]
```

Sau khi đưa vào Max Heap:

```text
[8,7,4,2,1,1]
```

### Lần 1

Lấy:

```text
8
7
```

Ta có:

```text
8 - 7 = 1
```

Thêm `1`.

Heap còn:

```text
[4,2,1,1,1]
```

---

### Lần 2

Lấy:

```text
4
2
```

Ta có:

```text
4 - 2 = 2
```

Thêm `2`.

Heap:

```text
[2,1,1,1]
```

---

### Lần 3

Lấy:

```text
2
1
```

Ta có:

```text
2 - 1 = 1
```

Thêm `1`.

Heap:

```text
[1,1,1]
```

---

### Lần 4

Lấy:

```text
1
1
```

Vì:

```text
1 == 1
```

cả hai biến mất.

Heap:

```text
[1]
```

---

### Kết thúc

Còn một viên đá:

```text
1
```

Kết quả:

```text
1
```

---

# 11. Code C++ tối ưu

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int lastStoneWeight(vector<int>& stones) {
        priority_queue<int> pq;

        for (int x : stones) {
            pq.push(x);
        }

        while (pq.size() >= 2) {
            int x = pq.top();
            pq.pop();

            int y = pq.top();
            pq.pop();

            if (x != y) {
                pq.push(x - y);
            }
        }

        if (pq.empty()) {
            return 0;
        }

        return pq.top();
    }
};
```

---

# 12. Giải thích code từng phần

## Khởi tạo Max Heap

```cpp
priority_queue<int> pq;
```

`pq` sẽ lưu các viên đá.

Do mặc định là Max Heap nên:

```cpp
pq.top()
```

luôn là viên đá có khối lượng lớn nhất.

---

## Đưa tất cả viên đá vào Heap

```cpp
for (int x : stones) {
    pq.push(x);
}
```

Ví dụ:

```text
stones = [2,7,4,1,8,1]
```

thì toàn bộ các giá trị được đưa vào `pq`.

---

## Điều kiện vòng lặp

```cpp
while (pq.size() >= 2)
```

Chúng ta cần ít nhất hai viên đá để thực hiện một lần đập.

Nếu:

```text
pq.size() == 1
```

thì không thể đập tiếp.

Nếu:

```text
pq.size() == 0
```

thì không còn viên đá nào.

---

## Lấy viên lớn nhất

```cpp
int x = pq.top();
pq.pop();
```

`top()` lấy viên đá lớn nhất.

`pop()` xóa viên đó khỏi heap.

---

## Lấy viên lớn thứ hai

```cpp
int y = pq.top();
pq.pop();
```

Sau khi viên lớn nhất đã bị xóa, `top()` lúc này chính là viên lớn thứ hai ban đầu.

---

## Trường hợp hai viên bằng nhau

```cpp
if (x != y)
```

Nếu:

```text
x == y
```

thì cả hai bị phá hủy.

Không cần thêm gì vào heap.

Ví dụ:

```text
x = 5
y = 5
```

Sau khi đập:

```text
5 + 5 → 0 viên
```

Do đó không có:

```cpp
pq.push(...)
```

---

## Trường hợp hai viên khác nhau

Nếu:

```text
x != y
```

thì còn lại:

```text
x - y
```

nên:

```cpp
pq.push(x - y);
```

---

# 13. Độ phức tạp

Gọi:

```text
n = stones.size()
```

## Xây dựng Heap

Ta có thể đưa từng phần tử vào:

```cpp
pq.push(x);
```

Mỗi lần:

```text
O(log n)
```

Có `n` phần tử:

```text
O(n log n)
```

---

## Vòng lặp

Mỗi lần lặp:

```text
pop()
pop()
```

và có thể có:

```text
push()
```

Mỗi thao tác có:

```text
O(log n)
```

Số lần đập tối đa là khoảng:

```text
n / 2
```

Do đó:

```text
O(n log n)
```

---

## Tổng

```text
Time Complexity:
O(n log n)
```

---

## Space Complexity

Max Heap chứa tối đa `n` phần tử:

```text
O(n)
```

Do đó:

```text
Space Complexity:
O(n)
```

---

# 14. Có thể xây Heap nhanh hơn không?

Có.

Thay vì:

```cpp
priority_queue<int> pq;

for (int x : stones) {
    pq.push(x);
}
```

ta có thể khởi tạo trực tiếp:

```cpp
priority_queue<int> pq(stones.begin(), stones.end());
```

Code:

```cpp
int lastStoneWeight(vector<int>& stones) {
    priority_queue<int> pq(stones.begin(), stones.end());

    while (pq.size() >= 2) {
        int x = pq.top();
        pq.pop();

        int y = pq.top();
        pq.pop();

        if (x != y) {
            pq.push(x - y);
        }
    }

    return pq.empty() ? 0 : pq.top();
}
```

Cách này ngắn gọn hơn.

Việc xây dựng heap từ một dãy có thể thực hiện trong:

```text
O(n)
```

thay vì `n` lần `push()` với tổng `O(n log n)`.

Sau đó các thao tác đập đá vẫn mất:

```text
O(n log n)
```

nên độ phức tạp tổng thể vẫn là:

```text
O(n log n)
```

---

# 15. Phiên bản code nên ghi nhớ

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int lastStoneWeight(vector<int>& stones) {
        priority_queue<int> pq(stones.begin(), stones.end());

        while (pq.size() >= 2) {
            int x = pq.top();
            pq.pop();

            int y = pq.top();
            pq.pop();

            if (x != y) {
                pq.push(x - y);
            }
        }

        return pq.empty() ? 0 : pq.top();
    }
};
```

Đây là phiên bản ngắn gọn và phù hợp để sử dụng khi làm LeetCode.

---

# 16. Tại sao đây là bài Heap điển hình?

Bài này có đặc điểm:

```text
Mỗi vòng:
    lấy phần tử lớn nhất
    lấy phần tử lớn thứ hai
    xử lý
    đưa kết quả trở lại
```

Đây chính là pattern của Priority Queue.

Có thể nhận diện bằng các dấu hiệu:

- "largest"
- "smallest"
- "top K"
- "minimum"
- "maximum"
- "repeatedly choose the largest/smallest"
- Sau khi xử lý lại có thể sinh ra một phần tử mới.

Khi gặp những dạng này, hãy nghĩ ngay đến:

```text
Heap / Priority Queue
```

---

# 17. So sánh với cách dùng `sort`

Một cách khác là:

```text
Sort mảng
↓
Lấy hai phần tử cuối
↓
Xóa chúng
↓
Thêm hiệu
↓
Sort lại
↓
Lặp
```

Vấn đề là sau mỗi lần thêm viên đá mới, ta có thể phải sắp xếp lại.

Nếu cứ:

```cpp
sort(...)
```

sau mỗi lần đập thì rất tốn thời gian.

Ví dụ:

```text
n phần tử
↓
sort O(n log n)
↓
đập
↓
sort O(n log n)
↓
đập
↓
...
```

Heap phù hợp hơn vì nó được thiết kế để duy trì phần tử lớn nhất/nhỏ nhất sau các thao tác thêm/xóa.

---

# 18. Max Heap và Min Heap

Trong C++:

## Max Heap

```cpp
priority_queue<int> pq;
```

Phần tử lớn nhất ở `top()`.

Ví dụ:

```text
[2,7,4,1]

top() = 7
```

---

## Min Heap

Muốn phần tử nhỏ nhất ở `top()`:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

Ví dụ:

```text
[2,7,4,1]

top() = 1
```

---

# 19. Pattern quan trọng của Priority Queue

## Pattern 1 — Lấy phần tử lớn nhất

```cpp
priority_queue<int> pq;
```

---

## Pattern 2 — Lấy phần tử nhỏ nhất

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

---

## Pattern 3 — Lấy rồi xóa

```cpp
int x = pq.top();
pq.pop();
```

---

## Pattern 4 — Xử lý rồi đưa kết quả trở lại

```cpp
int x = pq.top();
pq.pop();

int y = pq.top();
pq.pop();

int z = x - y;

if (z > 0) {
    pq.push(z);
}
```

Đây chính là pattern của **Last Stone Weight**.

---

# 20. Những lỗi thường gặp

## Lỗi 1: Chỉ lấy một viên đá

Sai:

```cpp
int x = pq.top();
pq.pop();

pq.push(...);
```

Bài yêu cầu mỗi lần phải lấy:

```text
2 viên lớn nhất
```

---

## Lỗi 2: Không `pop()` viên đá đã lấy

Sai:

```cpp
int x = pq.top();
int y = pq.top();
```

Ở đây:

```text
x == y
```

vì bạn đang lấy cùng một phần tử.

Đúng phải là:

```cpp
int x = pq.top();
pq.pop();

int y = pq.top();
pq.pop();
```

---

## Lỗi 3: Dùng sai phép trừ

Vì:

```text
x >= y
```

nên:

```cpp
x - y
```

là đúng.

---

## Lỗi 4: Khi `x == y` vẫn thêm `0`

Ví dụ:

```cpp
if (x != y) {
    pq.push(x - y);
}
```

là tốt hơn.

Không cần:

```cpp
pq.push(0);
```

vì `0` không phải là một viên đá còn lại.

---

## Lỗi 5: Không kiểm tra Heap trước khi lấy

Không được viết:

```cpp
while (true) {
    int x = pq.top();
    ...
}
```

Vì có thể Heap chỉ còn một hoặc không còn phần tử.

Nên dùng:

```cpp
while (pq.size() >= 2)
```

---

# 21. Edge Cases

## Trường hợp chỉ có một viên đá

```text
stones = [5]
```

Không có gì để đập.

Kết quả:

```text
5
```

---

## Hai viên bằng nhau

```text
stones = [5,5]
```

Đập:

```text
5 - 5 = 0
```

Cả hai biến mất.

Kết quả:

```text
0
```

---

## Hai viên khác nhau

```text
stones = [8,3]
```

Đập:

```text
8 - 3 = 5
```

Kết quả:

```text
5
```

---

## Tất cả viên đá bằng nhau

```text
stones = [4,4,4,4]
```

Đập:

```text
4 và 4 → mất
4 và 4 → mất
```

Kết quả:

```text
0
```

---

# 22. Test Cases

### Test 1

```text
Input:
[2,7,4,1,8,1]

Output:
1
```

### Test 2

```text
Input:
[1]

Output:
1
```

### Test 3

```text
Input:
[5,5]

Output:
0
```

### Test 4

```text
Input:
[8,3]

Output:
5
```

### Test 5

```text
Input:
[4,4,4,4]

Output:
0
```

---

# 23. Tư duy giải bài cần ghi nhớ

Khi đọc đề, đừng lập tức nghĩ đến code.

Hãy tách thành:

```text
Bài toán yêu cầu làm gì?
        ↓
Mỗi lần cần lấy phần tử nào?
        ↓
Có phải lấy max/min liên tục không?
        ↓
Có phần tử mới được sinh ra không?
        ↓
Có cần lấy lại max/min sau khi thêm không?
```

Với bài này:

```text
Cần lấy 2 phần tử lớn nhất
        ↓
Max Heap
        ↓
Lấy 2 phần tử
        ↓
Tính hiệu
        ↓
Nếu còn đá thì push lại
        ↓
Lặp
```

---

# 24. Công thức tư duy ngắn gọn

Có thể ghi nhớ bài bằng một câu:

> **Repeatedly take the two largest elements → use Max Heap.**

Hay:

```text
2 largest
    ↓
Max Heap
    ↓
pop
pop
    ↓
x - y
    ↓
push nếu còn
```

---

# 25. Kết luận

**Last Stone Weight** là một bài toán kinh điển để luyện:

- `priority_queue`
- Max Heap
- `top()`
- `pop()`
- `push()`
- Tư duy chọn phần tử lớn nhất lặp đi lặp lại.

Phương án tối ưu phù hợp là:

```cpp
priority_queue<int> pq(stones.begin(), stones.end());
```

Sau đó liên tục:

```cpp
x = pq.top();
pq.pop();

y = pq.top();
pq.pop();

if (x != y)
    pq.push(x - y);
```

Cho đến khi còn tối đa một viên đá.

Độ phức tạp:

```text
Time Complexity:  O(n log n)
Space Complexity: O(n)
```

Code hoàn chỉnh:

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int lastStoneWeight(vector<int>& stones) {
        priority_queue<int> pq(stones.begin(), stones.end());

        while (pq.size() >= 2) {
            int x = pq.top();
            pq.pop();

            int y = pq.top();
            pq.pop();

            if (x != y) {
                pq.push(x - y);
            }
        }

        return pq.empty() ? 0 : pq.top();
    }
};
```
