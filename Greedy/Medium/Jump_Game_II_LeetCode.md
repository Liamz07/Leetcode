# LeetCode 45 — Jump Game II

## 1. Đề bài

Cho một mảng số nguyên `nums`.

Tại vị trí `i`, `nums[i]` cho biết số bước nhảy tối đa có thể thực hiện từ vị trí đó.

Ví dụ nếu:

```text
nums[i] = 3
```

thì từ `i` có thể nhảy tới:

```text
i + 1
i + 2
i + 3
```

Mục tiêu:

> Tìm số lần nhảy ít nhất để đi từ vị trí `0` đến vị trí `n - 1`.

Đề bài đảm bảo luôn có thể tới vị trí cuối cùng.

---

# 2. Ví dụ

## Ví dụ 1

```text
nums = [2,3,1,1,4]
```

Một cách tối ưu:

```text
0 -> 1 -> 4
```

Số jump:

```text
2
```

Đáp án:

```text
2
```

## Ví dụ 2

```text
nums = [2,3,0,1,4]
```

Một cách tối ưu:

```text
0 -> 1 -> 4
```

Đáp án:

```text
2
```

---

# 3. Khác gì so với Jump Game I?

Jump Game I hỏi:

```text
Có thể tới vị trí cuối hay không?
```

Jump Game II hỏi:

```text
Ít nhất cần bao nhiêu jump?
```

Đây là khác biệt rất quan trọng.

Ở Jump Game I, ta chỉ cần theo dõi:

```text
maxReach
```

Ở Jump Game II, ta phải theo dõi thêm:

```text
currentEnd
farthest
jumps
```

---

# 4. Cách nghĩ đầu tiên: Dynamic Programming

Ta có thể định nghĩa:

```text
dp[i] = số jump ít nhất để đi từ 0 tới i
```

Ban đầu:

```text
dp[0] = 0
```

Nếu từ `i` có thể đi tới `j`, ta có:

```text
dp[j] = min(dp[j], dp[i] + 1)
```

Cách này đúng nhưng nếu duyệt tất cả vị trí có thể nhảy tới từ mỗi `i`, ta có thể mất:

```text
O(n^2)
```

thời gian.

Ta có thể làm tốt hơn bằng Greedy.

---

# 5. Insight quan trọng: Nhìn bài như BFS

Hãy tưởng tượng mỗi vị trí là một node.

Nếu từ `i` có thể nhảy tới `j`, ta có một cạnh:

```text
i -> j
```

Mỗi jump có cost:

```text
1
```

Do đó bài toán giống việc tìm shortest path trong graph không trọng số.

BFS sẽ chia các node thành các level:

```text
Level 0: các vị trí đạt được sau 0 jump
Level 1: các vị trí đạt được sau 1 jump
Level 2: các vị trí đạt được sau 2 jump
...
```

Điểm đặc biệt của Jump Game II là các vị trí trong một level có thể được xử lý như một khoảng liên tục.

Vì vậy ta không cần thực sự dùng queue.

Ta có thể mô phỏng BFS bằng Greedy.

---

# 6. Hai biến quan trọng

## currentEnd

```text
currentEnd = vị trí xa nhất có thể đạt được với số jump hiện tại
```

Nó là **biên phải của level hiện tại**.

## farthest

```text
farthest = vị trí xa nhất mà ta có thể mở rộng tới từ toàn bộ level hiện tại
```

Nó là **biên phải tốt nhất của level tiếp theo**.

Ngoài ra:

```text
jumps = số jump đã sử dụng
```

---

# 7. Ví dụ trực quan

Với:

```text
nums = [2,3,1,1,4]
```

Ban đầu:

```text
jumps = 0
currentEnd = 0
farthest = 0
```

Sau 0 jump, ta đang ở:

```text
[0]
```

Từ vị trí `0` có thể đi xa tới:

```text
0 + nums[0] = 2
```

nên:

```text
farthest = 2
```

Khi:

```text
i == currentEnd
```

nghĩa là ta đã xét hết phạm vi của level hiện tại.

Do đó phải sử dụng thêm một jump:

```text
jumps = 1
```

và phạm vi mới trở thành:

```text
currentEnd = farthest = 2
```

Bây giờ sau 1 jump, ta có thể xét:

```text
1, 2
```

---

# 8. Xét tiếp vùng [1, 2]

### i = 1

```text
i + nums[i]
= 1 + 3
= 4
```

nên:

```text
farthest = 4
```

### i = 2

```text
i + nums[i]
= 2 + 1
= 3
```

nhưng:

```text
farthest = max(4, 3)
         = 4
```

Khi:

```text
i == currentEnd
```

ta đã xét xong toàn bộ level hiện tại.

Tăng:

```text
jumps = 2
currentEnd = 4
```

Mà:

```text
4 = n - 1
```

nên đã tới đích.

Đáp án:

```text
2
```

---

# 9. Tại sao phải duyệt cả range trước?

Một sai lầm phổ biến là nghĩ:

> "Ở mỗi vị trí, mình phải quyết định ngay nên nhảy tới đâu."

Không cần.

Giả sử sau `k` jump, ta có thể đứng ở:

```text
[left, currentEnd]
```

Ta chỉ cần xét toàn bộ các vị trí trong range này.

Với mỗi vị trí:

```text
i
```

ta tính:

```text
i + nums[i]
```

và giữ giá trị lớn nhất:

```text
farthest = max(farthest, i + nums[i])
```

Sau khi xét xong cả range, `farthest` cho biết:

> Nếu dùng thêm đúng 1 jump, ta có thể mở rộng phạm vi tới đâu.

Đây chính là phần Greedy.

---

# 10. Tại sao chọn farthest là tối ưu?

Giả sử trong range hiện tại có:

```text
i = 1 -> reach 5
i = 2 -> reach 7
i = 3 -> reach 6
i = 4 -> reach 9
```

Nếu mục tiêu là tối đa hóa phạm vi sau jump tiếp theo thì ta muốn:

```text
farthest = 9
```

Không có lợi ích gì khi chọn phạm vi chỉ tới `5`, `6` hoặc `7`, bởi vì:

```text
[... tới 5]
```

đã nằm trong:

```text
[... tới 9]
```

và phạm vi tới `9` còn mở rộng hơn.

Điều quan trọng là:

> Tất cả các vị trí trong cùng một level đều có cùng "chi phí" là số jump hiện tại.

Vì vậy ta có thể tối đa hóa phạm vi của level tiếp theo mà không làm tăng số jump.

---

# 11. Vì sao chỉ tăng jumps khi i == currentEnd?

Đây là insight quan trọng nhất của code.

Giả sử:

```text
currentEnd = 5
```

Điều đó có nghĩa:

> Với số jump hiện tại, ta có thể xét tất cả vị trí tới `5`.

Khi:

```text
i < currentEnd
```

ta vẫn đang ở trong cùng một level.

Do đó không được tăng:

```text
jumps
```

Khi:

```text
i == currentEnd
```

ta đã xét xong toàn bộ level hiện tại.

Nếu muốn đi xa hơn, bắt buộc phải dùng thêm một jump:

```cpp
++jumps;
```

Sau đó:

```cpp
currentEnd = farthest;
```

---

# 12. Tại sao không tăng jumps mỗi lần farthest thay đổi?

Ví dụ:

```text
farthest = 2
```

sau đó:

```text
farthest = 4
```

sau đó:

```text
farthest = 7
```

Không có nghĩa là ta đã dùng 3 jump.

Đó chỉ là:

```text
nhiều vị trí trong cùng một level
```

đang cung cấp những khả năng mở rộng khác nhau.

Chỉ khi đã xét hết level:

```text
i == currentEnd
```

ta mới chuyển sang level tiếp theo.

---

# 13. Vì sao không chọn nums[i] lớn nhất?

Cũng là một lỗi dễ mắc.

Ta không cần tối đa hóa:

```text
nums[i]
```

mà cần tối đa hóa:

```text
i + nums[i]
```

Ví dụ:

```text
i = 2
nums[i] = 5
reach = 7
```

Trong khi:

```text
i = 5
nums[i] = 4
reach = 9
```

Mặc dù:

```text
4 < 5
```

nhưng vị trí thứ hai lại giúp ta đi xa hơn.

Do đó phải dùng:

```cpp
farthest = max(farthest, i + nums[i]);
```

---

# 14. Code Greedy tối ưu

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int jumps = 0;
        int currentEnd = 0;
        int farthest = 0;

        for (int i = 0; i < nums.size() - 1; ++i) {
            farthest = max(farthest, i + nums[i]);

            if (i == currentEnd) {
                ++jumps;
                currentEnd = farthest;
            }
        }

        return jumps;
    }
};
```

---

# 15. Giải thích từng dòng

```cpp
int jumps = 0;
```

Số jump đã sử dụng.

Ban đầu chưa nhảy:

```text
0
```

---

```cpp
int currentEnd = 0;
```

Sau 0 jump, ta chỉ đang ở:

```text
0
```

Do đó biên hiện tại là:

```text
0
```

---

```cpp
int farthest = 0;
```

Vị trí xa nhất mà level hiện tại có thể mở rộng tới.

---

```cpp
for (int i = 0; i < nums.size() - 1; ++i)
```

Ta chỉ cần duyệt tới:

```text
n - 2
```

Không cần xử lý vị trí cuối cùng vì khi đã tới đó thì bài toán kết thúc.

---

```cpp
farthest = max(farthest, i + nums[i]);
```

Từ `i`, ta có thể đi xa nhất tới:

```text
i + nums[i]
```

Ta lấy max trên toàn bộ current range.

---

```cpp
if (i == currentEnd)
```

Ta đã xét xong toàn bộ các vị trí có thể đạt được với số jump hiện tại.

---

```cpp
++jumps;
```

Muốn đi xa hơn thì bắt buộc phải dùng thêm một jump.

---

```cpp
currentEnd = farthest;
```

Chuyển sang biên của level tiếp theo.

---

# 16. Dry Run đầy đủ

Input:

```text
nums = [2,3,1,1,4]
```

Ban đầu:

```text
jumps = 0
currentEnd = 0
farthest = 0
```

### i = 0

```text
farthest = max(0, 0 + 2)
         = 2
```

Vì:

```text
i == currentEnd
0 == 0
```

nên:

```text
jumps = 1
currentEnd = 2
```

---

### i = 1

```text
farthest = max(2, 1 + 3)
         = 4
```

Chưa tới biên:

```text
1 != 2
```

Không tăng jump.

---

### i = 2

```text
farthest = max(4, 2 + 1)
         = 4
```

Bây giờ:

```text
i == currentEnd
2 == 2
```

nên:

```text
jumps = 2
currentEnd = 4
```

Đã tới vị trí cuối.

Kết quả:

```text
2
```

---

# 17. Proof trực giác

Ta chia các vị trí thành các level:

```text
Level 0:
có thể đạt được sau 0 jump

Level 1:
có thể đạt được sau 1 jump

Level 2:
có thể đạt được sau 2 jump

...
```

`currentEnd` là biên phải của level hiện tại.

Trong level đó, ta xét tất cả vị trí và tính `farthest`.

`farthest` là biên phải xa nhất mà level hiện tại có thể tạo ra ở level tiếp theo.

Không có cách nào với cùng số jump có thể vượt qua `farthest`, vì `farthest` đã là max của tất cả các khả năng trong level.

Khi tới:

```text
i == currentEnd
```

level hiện tại đã được xét hết.

Muốn vượt qua nó bắt buộc phải thêm một jump.

Vì vậy:

```text
jumps++
currentEnd = farthest
```

là bước chuyển chính xác sang level tiếp theo.

Khi `currentEnd` chứa vị trí cuối, số level đã đi qua chính là số jump nhỏ nhất.

---

# 18. Một cách hiểu khác: Greedy đang mô phỏng BFS

BFS thông thường sẽ cần:

```text
queue
visited
level
```

Nhưng bài này có cấu trúc đặc biệt.

Các node của một level có thể được biểu diễn bằng một đoạn:

```text
[currentStart, currentEnd]
```

Do đó thay vì lưu tất cả node, ta chỉ cần:

```text
currentEnd
```

Và trong quá trình duyệt range, ta tìm:

```text
farthest
```

Sau đó chuyển sang level mới.

Vì vậy:

```text
BFS theo level
        ↓
Nhận ra level là một range
        ↓
Không cần queue
        ↓
Greedy với currentEnd + farthest
```

---

# 19. Vì sao thuật toán đạt O(n)?

Mỗi vị trí chỉ được duyệt một lần.

Trong mỗi lần duyệt chỉ có:

```text
max
+
so sánh
+
gán
```

tất cả đều là:

```text
O(1)
```

Do đó:

```text
Time Complexity = O(n)
```

Ta chỉ dùng ba biến:

```text
jumps
currentEnd
farthest
```

nên:

```text
Space Complexity = O(1)
```

---

# 20. So sánh các approach

| Approach | Time | Space | Nhận xét |
|---|---:|---:|---|
| Brute Force / DFS | Rất lớn | O(n) | Nhiều nhánh, tính lặp |
| DP | O(n^2) | O(n) | Đúng nhưng chưa tối ưu |
| BFS | Có thể O(n^2) | O(n) | Mô hình shortest path |
| Greedy | **O(n)** | **O(1)** | **Tối ưu** |

---

# 21. Những lỗi thường gặp

## Lỗi 1: Tăng jumps ở mỗi vòng lặp

Sai:

```cpp
for (...) {
    ++jumps;
}
```

Vì nhiều vị trí có thể thuộc cùng một level.

Đúng là chỉ tăng khi:

```cpp
i == currentEnd
```

---

## Lỗi 2: Chọn nums[i] lớn nhất

Không đúng.

Phải chọn:

```text
i + nums[i]
```

lớn nhất.

---

## Lỗi 3: Chọn ngay một đường đi cụ thể

Không cần.

Ta chỉ cần biết:

```text
farthest
```

của toàn bộ current range.

---

## Lỗi 4: Nhầm currentEnd và farthest

Nhớ:

```text
currentEnd = biên của level hiện tại
farthest    = biên tốt nhất của level kế tiếp
```

---

## Lỗi 5: Duyệt tới n

Không cần xét:

```text
i = n - 1
```

vì đó đã là đích.

Nên:

```cpp
i < nums.size() - 1
```

---

# 22. Một cách nhớ cực kỳ ngắn

Nếu quên code, hãy nhớ ba câu:

```text
1. Trong range hiện tại, tìm farthest.
2. Khi i chạm currentEnd, tăng jumps.
3. Đặt currentEnd = farthest.
```

Tức là:

```cpp
farthest = max(farthest, i + nums[i]);

if (i == currentEnd) {
    ++jumps;
    currentEnd = farthest;
}
```

---

# 23. Tư duy tổng quát có thể áp dụng cho bài khác

Đây là một pattern Greedy rất đáng nhớ:

> Khi tất cả lựa chọn trong cùng một "level" có cùng cost, hãy xử lý toàn bộ level và tìm lựa chọn mở rộng phạm vi tốt nhất cho level tiếp theo.

Ở bài này:

```text
cost của mỗi jump = 1
```

nên:

```text
current range
    ↓
tìm farthest
    ↓
thêm 1 jump
    ↓
mở rộng range
```

---

# 24. Jump Game I vs Jump Game II

## Jump Game I

```text
maxReach
```

Mục tiêu:

```text
có tới được cuối không?
```

Pattern:

```cpp
maxReach = max(maxReach, i + nums[i]);

if (i > maxReach) {
    return false;
}
```

## Jump Game II

```text
currentEnd
farthest
jumps
```

Mục tiêu:

```text
ít nhất bao nhiêu jump?
```

Pattern:

```cpp
farthest = max(farthest, i + nums[i]);

if (i == currentEnd) {
    ++jumps;
    currentEnd = farthest;
}
```

---

# 25. Lời giải cuối cùng

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int jumps = 0;
        int currentEnd = 0;
        int farthest = 0;

        for (int i = 0; i < nums.size() - 1; ++i) {
            farthest = max(farthest, i + nums[i]);

            if (i == currentEnd) {
                ++jumps;
                currentEnd = farthest;
            }
        }

        return jumps;
    }
};
```

Complexity:

```text
Time Complexity: O(n)
Space Complexity: O(1)
```

## Insight quan trọng nhất

Đừng nghĩ:

```text
"Ở vị trí i, tôi nên nhảy tới vị trí nào?"
```

Hãy nghĩ:

```text
"Với số jump hiện tại, tôi có thể xét tới đâu?"

"Trong toàn bộ vùng đó, jump tiếp theo có thể mở rộng xa nhất tới đâu?"
```

Hai biến:

```text
currentEnd
farthest
```

chính là toàn bộ chìa khóa của lời giải Greedy cho Jump Game II.
