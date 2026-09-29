# Island Perimeter — LeetCode

## 1. Tổng quan bài toán

Bài toán **Island Perimeter** yêu cầu tính chu vi của một hòn đảo trên một lưới 2 chiều.

Mỗi ô trong lưới có hai trạng thái:

- `1`: ô đất thuộc đảo.
- `0`: ô nước.

Hai ô đất được xem là kề nhau nếu chúng có chung một cạnh:

- trên
- dưới
- trái
- phải

Không tính kề nhau theo đường chéo.

Mục tiêu là tính **tổng số cạnh của các ô đất tiếp xúc với nước hoặc nằm ở biên của lưới**.

Ví dụ:

```text
[
  [0,1,0,0],
  [1,1,1,0],
  [0,1,0,0],
  [1,1,0,0]
]
```

Kết quả là:

```text
16
```

---

# 2. Điều quan trọng nhất cần nhận ra

Một cách nghĩ tự nhiên là:

> Mỗi ô đất là một hình vuông có 4 cạnh, vậy cứ đếm tất cả các cạnh rồi xử lý các cạnh chung giữa hai ô đất.

Đây chính là chìa khóa của bài toán.

Nếu có `N` ô đất thì ban đầu ta có:

```text
4 * N
```

cạnh.

Tuy nhiên, nếu hai ô đất nằm cạnh nhau, cạnh chung giữa chúng **không thuộc chu vi bên ngoài**.

Ví dụ:

```text
1 1
```

Mỗi ô có 4 cạnh:

```text
4 + 4 = 8
```

Nhưng hai ô dùng chung một cạnh.

Cạnh chung này bị tính hai lần:

- một lần từ ô bên trái
- một lần từ ô bên phải

Vì vậy cần trừ `2`.

Chu vi thực tế:

```text
8 - 2 = 6
```

Do đó, một công thức quan trọng là:

```text
perimeter = 4 * số_ô_đất - 2 * số_cặp_ô_đất_kề_nhau
```

Đây là một cách giải rất đẹp về mặt tư duy.

---

# 3. Cách tiếp cận 1: Đếm tất cả cạnh rồi trừ cạnh chung

## Ý tưởng

Ta duyệt toàn bộ grid.

Mỗi khi gặp một ô đất:

```text
1
```

ta cộng `4` vào đáp án.

Sau đó kiểm tra các ô đất nằm cạnh nó.

Nếu hai ô đất kề nhau thì có một cạnh chung, và cạnh này không tạo thành chu vi bên ngoài.

Vì cạnh chung đã được tính ở cả hai ô, ta phải trừ `2`.

---

## Ví dụ đơn giản

Xét:

```text
1 1
```

Bắt đầu:

```text
2 ô đất
```

Tổng số cạnh:

```text
2 * 4 = 8
```

Có một cặp ô đất kề nhau:

```text
1 1
```

Có một cạnh chung.

Trừ:

```text
2
```

Kết quả:

```text
8 - 2 = 6
```

---

## Nhưng có một vấn đề khi cài đặt

Nếu với mỗi ô ta kiểm tra cả 4 hướng:

```text
up
down
left
right
```

thì cùng một cạnh chung có thể bị phát hiện hai lần.

Ví dụ:

```text
1 1
```

Khi xử lý ô thứ nhất:

```text
ô thứ nhất -> ô thứ hai
```

ta phát hiện cạnh chung.

Sau đó khi xử lý ô thứ hai:

```text
ô thứ hai -> ô thứ nhất
```

ta lại phát hiện đúng cạnh chung đó.

Nếu mỗi lần chỉ trừ `1`, ta có thể xử lý vấn đề bằng cách quản lý rất cẩn thận.

Tuy nhiên có một cách đơn giản hơn.

---

# 4. Cách tiếp cận 2: Mỗi ô đóng góp từng cạnh trực tiếp vào chu vi

Đây thường là cách dễ hiểu và ít lỗi nhất.

Thay vì:

```text
4 * số_ô_đất - 2 * số_cạnh_chung
```

ta xét trực tiếp từng cạnh của từng ô đất.

Với một ô đất tại `(r, c)`, nó có 4 cạnh.

Một cạnh đóng góp vào chu vi nếu:

1. Cạnh đó nằm ngoài grid.
2. Ô bên cạnh là nước.

Nếu ô bên cạnh cũng là đất thì cạnh đó không đóng góp vào chu vi.

---

# 5. Quy tắc kiểm tra một ô đất

Giả sử đang xét:

```text
grid[r][c] == 1
```

Ta kiểm tra 4 hướng:

```text
up
down
left
right
```

Với mỗi hướng:

- Nếu vị trí bên cạnh nằm ngoài grid -> `+1`
- Nếu vị trí bên cạnh là `0` -> `+1`
- Nếu vị trí bên cạnh là `1` -> `+0`

Đây chính là toàn bộ thuật toán.

---

# 6. Ví dụ chi tiết

Xét:

```text
0 1 0
1 1 1
0 1 0
```

Có 5 ô đất.

Nếu tính tất cả các cạnh:

```text
5 * 4 = 20
```

Có 4 cạnh chung:

```text
      1
      |
1 -- 1 -- 1
      |
      1
```

Mỗi cạnh chung làm giảm 2 cạnh trong tổng ban đầu.

Do đó:

```text
20 - 4 * 2 = 12
```

Chu vi là:

```text
12
```

---

# 7. Tại sao kiểm tra 4 hướng là đủ?

Mỗi ô vuông chỉ có đúng 4 cạnh.

Chu vi của đảo chỉ được tạo bởi các cạnh của những ô đất.

Do đó, đối với mỗi ô đất, chỉ cần trả lời 4 câu hỏi:

```text
Cạnh trên có tiếp xúc với bên ngoài không?
Cạnh dưới có tiếp xúc với bên ngoài không?
Cạnh trái có tiếp xúc với bên ngoài không?
Cạnh phải có tiếp xúc với bên ngoài không?
```

Không cần:

- DFS
- BFS
- Union-Find
- Graph traversal
- Dynamic Programming

Bài toán này thực chất là một bài toán **duyệt lưới và kiểm tra local neighborhood**.

---

# 8. Một cách nhìn khác: Chu vi là số cạnh "lộ ra"

Đây là cách nhìn rất hữu ích để giải nhiều bài toán grid khác.

Hãy tưởng tượng mỗi ô đất là một miếng gạch vuông.

Ví dụ:

```text
1 1
1 1
```

Có 4 ô vuông.

Nếu tách rời:

```text
4 * 4 = 16 cạnh
```

Nhưng các cạnh nằm bên trong hình vuông lớn không được tính vào chu vi.

Có 4 cạnh nội bộ:

```text
giữa trái-phải ở hàng trên
giữa trái-phải ở hàng dưới
giữa trên-dưới ở cột trái
giữa trên-dưới ở cột phải
```

Mỗi cạnh nội bộ loại bỏ 2 cạnh khỏi chu vi.

Vậy:

```text
16 - 4 * 2 = 8
```

Đúng với chu vi của hình vuông `2 x 2`.

---

# 9. Công thức tổng quát

Có thể mô hình hóa bài toán bằng:

```text
perimeter = 4 * landCells - 2 * sharedEdges
```

Trong đó:

- `landCells`: số ô có giá trị `1`
- `sharedEdges`: số cặp ô đất có chung một cạnh

Tại sao là `2 * sharedEdges`?

Vì một cạnh chung:

```text
A | B
```

được tính một lần như cạnh của `A` và một lần như cạnh của `B`.

Nhưng nó không nằm trên đường biên ngoài.

Vì vậy phải loại bỏ cả hai lần đếm.

---

# 10. Cách đếm cạnh chung mà không bị đếm hai lần

Nếu muốn sử dụng công thức trên, có một mẹo rất quan trọng.

Không cần kiểm tra cả 4 hướng.

Chỉ cần kiểm tra:

- ô bên phải
- ô bên dưới

Vì mọi cặp ô kề nhau đều có thể được biểu diễn duy nhất theo một trong hai dạng:

```text
ô hiện tại <-> ô bên phải
```

hoặc:

```text
ô hiện tại
     ^
     |
ô bên dưới
```

Ví dụ:

```text
1 1
1 1
```

Các cạnh chung là:

```text
(0,0) -- (0,1)
(1,0) -- (1,1)

(0,0)
  |
(1,0)

(0,1)
  |
(1,1)
```

Nếu chỉ kiểm tra phải và dưới:

- `(0,0)` kiểm tra phải -> phát hiện cạnh thứ nhất
- `(0,0)` kiểm tra dưới -> phát hiện cạnh thứ hai
- `(0,1)` kiểm tra dưới -> phát hiện cạnh thứ ba
- `(1,0)` kiểm tra phải -> phát hiện cạnh thứ tư

Mỗi cạnh chỉ xuất hiện đúng một lần.

---

# 11. So sánh hai cách tiếp cận

## Cách A — Đếm cạnh lộ ra

Với mỗi ô đất:

```text
for 4 directions:
    nếu cạnh lộ ra:
        perimeter++
```

Ưu điểm:

- Dễ hiểu.
- Dễ code.
- Ít khả năng đếm trùng.
- Trực tiếp bám sát định nghĩa chu vi.
- Không cần công thức trừ cạnh chung.

Đây là cách nên ưu tiên khi làm bài phỏng vấn hoặc LeetCode.

---

## Cách B — Tổng cạnh trừ cạnh chung

Công thức:

```text
perimeter = 4 * landCells - 2 * sharedEdges
```

Ưu điểm:

- Rất ngắn.
- Thể hiện insight tốt.
- Có tính chất toán học rõ ràng.
- Dễ mở rộng sang các biến thể đếm số cạnh chung.

Nhược điểm:

- Khi triển khai phải cẩn thận để không đếm một cạnh chung hai lần.

---

# 12. Lựa chọn lời giải tối ưu

Trong bài toán này, "tối ưu" chủ yếu có nghĩa là:

- Thời gian tuyến tính theo số ô.
- Không dùng bộ nhớ phụ không cần thiết.
- Không thực hiện DFS/BFS khi không cần.

Vì phải đọc các ô trong grid để biết trạng thái của chúng, về bản chất ta cần xem xét toàn bộ grid trong trường hợp tổng quát.

Vì vậy:

```text
Time:  O(rows * cols)
Space: O(1)
```

Nếu không tính bộ nhớ mà input grid đã chiếm.

Đây là mức tối ưu về độ phức tạp cho cách giải thông thường.

---

# 13. Tại sao không cần DFS/BFS?

Một sai lầm phổ biến là nhìn thấy từ "island" và lập tức nghĩ:

```text
DFS
BFS
connected components
```

Nhưng bài toán không hỏi:

- đảo có liên thông không?
- có bao nhiêu đảo?
- diện tích đảo là bao nhiêu?
- tìm đường đi trong đảo.

Bài toán chỉ hỏi:

> Có bao nhiêu cạnh của các ô đất không tiếp xúc với một ô đất khác?

Đây là thông tin hoàn toàn local.

Để xác định một cạnh có thuộc chu vi hay không, chỉ cần biết ô ngay bên cạnh.

Không cần biết toàn bộ component.

---

# 14. Có cần kiểm tra đường chéo không?

Không.

Ví dụ:

```text
1 0
0 1
```

Hai ô đất chạm nhau tại một điểm.

Nhưng chúng **không có cạnh chung**.

Vì chu vi được tính theo cạnh, không phải theo điểm, nên chúng không ảnh hưởng lẫn nhau.

Mỗi ô vẫn có:

```text
4 cạnh
```

Tổng chu vi:

```text
8
```

---

# 15. Boundary của grid

Một trong những điểm quan trọng khi code là xử lý biên.

Ví dụ:

```text
1
```

Một ô đất duy nhất.

Không có ô nào ở:

```text
up
down
left
right
```

Do đó cả 4 cạnh đều nằm ở biên ngoài.

Kết quả:

```text
4
```

Nếu code kiểm tra hàng xóm, cần phân biệt:

```text
nr < 0
nr >= rows
nc < 0
nc >= cols
```

với:

```text
grid[nr][nc] == 0
```

Cả hai trường hợp đều đóng góp `1` vào chu vi.

---

# 16. Mẫu tư duy "4 directions"

Đây là một pattern rất quan trọng trong các bài toán Grid của LeetCode.

Ta thường định nghĩa:

```cpp
int dr[] = {-1, 1, 0, 0};
int dc[] = {0, 0, -1, 1};
```

Tương ứng:

```text
(-1, 0) -> trên
( 1, 0) -> dưới
( 0,-1) -> trái
( 0, 1) -> phải
```

Sau đó:

```cpp
for (int k = 0; k < 4; ++k) {
    int nr = r + dr[k];
    int nc = c + dc[k];
}
```

Đây là kỹ thuật có thể tái sử dụng trong rất nhiều bài:

- Number of Islands
- Flood Fill
- Rotting Oranges
- Walls and Gates
- Shortest Path in Binary Matrix
- Pacific Atlantic Water Flow
- Surrounded Regions

---

# 17. Cài đặt C++ tối ưu — cách 1

Đây là lời giải trực tiếp nhất.

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        static const int dr[4] = {-1, 1, 0, 0};
        static const int dc[4] = {0, 0, -1, 1};

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                for (int k = 0; k < 4; ++k) {
                    const int nr = r + dr[k];
                    const int nc = c + dc[k];

                    if (nr < 0 || nr >= rows ||
                        nc < 0 || nc >= cols ||
                        grid[nr][nc] == 0) {
                        ++perimeter;
                    }
                }
            }
        }

        return perimeter;
    }
};
```

---

# 18. Giải thích từng phần code

## Lấy kích thước grid

```cpp
const int rows = grid.size();
const int cols = grid[0].size();
```

`rows` là số hàng.

`cols` là số cột.

---

## Biến lưu đáp án

```cpp
int perimeter = 0;
```

Mỗi cạnh hợp lệ sẽ làm:

```cpp
++perimeter;
```

---

## Bốn hướng

```cpp
static const int dr[4] = {-1, 1, 0, 0};
static const int dc[4] = {0, 0, -1, 1};
```

Có thể hình dung:

```text
        (-1, 0)
           ↑

(0, -1) ← cell → (0, 1)

           ↓
        (1, 0)
```

---

## Duyệt toàn bộ grid

```cpp
for (int r = 0; r < rows; ++r) {
    for (int c = 0; c < cols; ++c) {
```

Mỗi lần xử lý một ô.

---

## Bỏ qua ô nước

```cpp
if (grid[r][c] == 0) {
    continue;
}
```

Ô nước không có cạnh thuộc đảo.

---

## Kiểm tra bốn hàng xóm

```cpp
for (int k = 0; k < 4; ++k) {
```

Tương ứng với 4 cạnh của ô đất.

---

## Tọa độ hàng xóm

```cpp
const int nr = r + dr[k];
const int nc = c + dc[k];
```

---

## Khi nào cạnh được tính?

```cpp
if (nr < 0 || nr >= rows ||
    nc < 0 || nc >= cols ||
    grid[nr][nc] == 0) {
    ++perimeter;
}
```

Có hai nhóm trường hợp.

### Trường hợp 1: Ra ngoài grid

Ví dụ:

```text
1
```

Khi xét cạnh trên:

```text
r - 1 = -1
```

Đây là biên ngoài.

Cạnh đó thuộc chu vi.

---

### Trường hợp 2: Bên cạnh là nước

Ví dụ:

```text
1 0
```

Cạnh bên phải của ô đất tiếp xúc với nước.

Do đó cạnh đó thuộc chu vi.

---

### Trường hợp không cộng

Nếu:

```cpp
grid[nr][nc] == 1
```

thì cạnh giữa hai ô đất nằm bên trong đảo.

Không thuộc chu vi.

---

# 19. Chứng minh tính đúng đắn

Ta cần chứng minh thuật toán đếm đúng chu vi.

Xét một ô đất bất kỳ.

Ô đất này có đúng 4 cạnh.

Với mỗi cạnh, thuật toán kiểm tra ô nằm ở phía đối diện cạnh đó.

Có đúng ba khả năng về mặt hình học:

1. Phía bên kia nằm ngoài grid.
2. Phía bên kia là nước.
3. Phía bên kia là đất.

Trong hai trường hợp đầu:

```text
ngoài grid
hoặc
nước
```

cạnh đó nằm trên đường biên của đảo.

Thuật toán cộng `1`.

Trong trường hợp thứ ba:

```text
ô bên cạnh là đất
```

cạnh đó nằm bên trong đảo, vì nó là cạnh chung giữa hai ô đất.

Thuật toán không cộng.

Do thuật toán xét cả 4 cạnh của mọi ô đất, mọi cạnh thuộc chu vi được đếm đúng một lần và mọi cạnh bên trong không được đếm.

Vì vậy kết quả trả về chính xác là chu vi của đảo.

---

# 20. Độ phức tạp

Giả sử grid có:

```text
R hàng
C cột
```

Có tổng cộng:

```text
R * C
```

ô.

Mỗi ô được xử lý một lần.

Với mỗi ô đất, ta kiểm tra tối đa 4 hướng.

`4` là hằng số.

Do đó:

```text
Time Complexity: O(R * C)
```

Về bộ nhớ:

```text
Space Complexity: O(1)
```

Không sử dụng:

- queue
- stack
- visited matrix
- graph
- recursion

---

# 21. Có thể tối ưu hơn O(R * C) không?

Thông thường không cần và cũng không có ý nghĩa thực tế.

Để tính chu vi chính xác, ta cần biết trạng thái của các ô trong grid.

Nếu một ô chưa được kiểm tra, về mặt thông tin ta chưa biết nó là đất hay nước.

Vì vậy thuật toán tuyến tính theo kích thước input là mức tự nhiên.

Nói cách khác:

```text
O(R * C)
```

là lời giải tối ưu về mặt asymptotic cho input tổng quát.

---

# 22. Một lời giải C++ còn ngắn hơn

Do bài toán chỉ cần đếm cạnh lộ ra, có thể viết rất ngắn:

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        int ans = 0;

        for (int r = 0; r < grid.size(); ++r) {
            for (int c = 0; c < grid[0].size(); ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                ans += 4;

                if (r > 0 && grid[r - 1][c] == 1) {
                    --ans;
                }

                if (r + 1 < grid.size() && grid[r + 1][c] == 1) {
                    --ans;
                }

                if (c > 0 && grid[r][c - 1] == 1) {
                    --ans;
                }

                if (c + 1 < grid[0].size() &&
                    grid[r][c + 1] == 1) {
                    --ans;
                }
            }
        }

        return ans;
    }
};
```

Cách này cũng đúng.

Tuy nhiên có một điểm cần hiểu rõ:

Mỗi khi gặp hàng xóm là đất, ta `--ans`.

Tại sao chỉ trừ `1` thay vì `2`?

Bởi vì trong cách triển khai này, ban đầu mỗi ô đã đóng góp `4`.

Khi ô A nhìn thấy ô B, ta loại cạnh của A.

Sau đó khi xử lý B, B cũng nhìn thấy A và loại cạnh của B.

Tổng cộng cạnh chung bị loại:

```text
1 + 1 = 2
```

Đúng với công thức:

```text
4 * landCells - 2 * sharedEdges
```

---

# 23. Một cách viết còn tối ưu về mặt số lần kiểm tra

Ta có thể dùng công thức:

```text
perimeter = 4 * landCells - 2 * sharedEdges
```

và chỉ kiểm tra:

- bên phải
- bên dưới

Code:

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                perimeter += 4;

                if (r + 1 < rows && grid[r + 1][c] == 1) {
                    perimeter -= 2;
                }

                if (c + 1 < cols && grid[r][c + 1] == 1) {
                    perimeter -= 2;
                }
            }
        }

        return perimeter;
    }
};
```

Đây là một implementation rất đẹp nếu đã hiểu rõ invariant.

---

# 24. Tại sao chỉ kiểm tra phải và dưới?

Giả sử có hai ô đất kề nhau.

Nếu chúng nằm cạnh nhau theo chiều ngang:

```text
1 1
```

thì khi xử lý ô bên trái, ta kiểm tra:

```text
right
```

và phát hiện chúng kề nhau.

Không cần kiểm tra từ ô bên phải sang trái.

Nếu chúng nằm cạnh nhau theo chiều dọc:

```text
1
1
```

thì khi xử lý ô phía trên, ta kiểm tra:

```text
down
```

Vì vậy mỗi cạnh chung được phát hiện đúng một lần.

---

# 25. Hai implementation nên nhớ

## Implementation A — dễ hiểu nhất

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        static const int dr[4] = {-1, 1, 0, 0};
        static const int dc[4] = {0, 0, -1, 1};

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                for (int k = 0; k < 4; ++k) {
                    const int nr = r + dr[k];
                    const int nc = c + dc[k];

                    if (nr < 0 || nr >= rows ||
                        nc < 0 || nc >= cols ||
                        grid[nr][nc] == 0) {
                        ++perimeter;
                    }
                }
            }
        }

        return perimeter;
    }
};
```

Đây là implementation nên ưu tiên khi muốn:

- dễ đọc
- dễ giải thích
- khó bug
- tổng quát hóa pattern 4 directions

---

## Implementation B — dựa trên invariant

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                perimeter += 4;

                if (r + 1 < rows && grid[r + 1][c] == 1) {
                    perimeter -= 2;
                }

                if (c + 1 < cols && grid[r][c + 1] == 1) {
                    perimeter -= 2;
                }
            }
        }

        return perimeter;
    }
};
```

Implementation B ngắn và đẹp hơn nếu bạn đã nắm chắc ý tưởng:

```text
mỗi ô đất = +4
mỗi cạnh chung = -2
```

---

# 26. Dry Run

Xét:

```text
[
  [0,1,0],
  [1,1,1],
  [0,1,0]
]
```

Ta có 5 ô đất.

Khởi đầu:

```text
perimeter = 0
```

### Ô trung tâm phía trên

```text
0 1 0
```

Cộng:

```text
+4
```

Ô này có một hàng xóm là ô trung tâm.

Nếu dùng cách đếm cạnh lộ ra:

- trên -> nước -> `+1`
- dưới -> đất -> `+0`
- trái -> nước -> `+1`
- phải -> nước -> `+1`

Đóng góp:

```text
3
```

---

### Ô trung tâm

```text
1 1 1
  ^
```

Nó có bốn hàng xóm đều là đất hoặc nước tùy hướng.

Với hình dấu cộng, ô trung tâm có:

- trên: đất
- dưới: đất
- trái: đất
- phải: đất

Do đó đóng góp:

```text
0
```

---

### Các ô còn lại

Mỗi ô ngoài trung tâm có 3 cạnh ngoài và 1 cạnh chung với trung tâm.

Mỗi ô đóng góp:

```text
3
```

Có 4 ô như vậy:

```text
4 * 3 = 12
```

Tổng:

```text
3 + 0 + 3 + 3 + 3 + 3
```

Nếu tính đúng theo từng ô, kết quả là:

```text
12
```

---

# 27. Edge cases cần nhớ

## Case 1: Chỉ có một ô đất

```text
[1]
```

Kết quả:

```text
4
```

---

## Case 2: Một hàng

```text
[1,1,1]
```

Ba ô vuông liên tiếp.

Tổng cạnh ban đầu:

```text
3 * 4 = 12
```

Có hai cạnh chung:

```text
2 * 2 = 4
```

Chu vi:

```text
12 - 4 = 8
```

---

## Case 3: Một cột

```text
[
  [1],
  [1],
  [1]
]
```

Kết quả cũng là:

```text
8
```

---

## Case 4: Hình vuông 2 x 2

```text
[
  [1,1],
  [1,1]
]
```

Có 4 ô đất và 4 cạnh chung.

```text
4 * 4 - 4 * 2 = 8
```

---

## Case 5: Hai ô chỉ chạm góc

```text
[
  [1,0],
  [0,1]
]
```

Không có cạnh chung.

Kết quả:

```text
8
```

---

# 28. Các lỗi thường gặp

## Lỗi 1: Trừ 1 thay vì 2 trong công thức cạnh chung

Nếu dùng:

```text
4 * landCells - sharedEdges
```

thì sai.

Ví dụ:

```text
1 1
```

Ta có:

```text
8 - 1 = 7
```

Nhưng đáp án đúng là:

```text
6
```

Phải là:

```text
8 - 2 * 1 = 6
```

---

## Lỗi 2: Đếm một cạnh chung hai lần nhưng chỉ trừ một lần

Nếu dùng 4 hướng:

```cpp
up
down
left
right
```

và mỗi khi gặp ô đất bên cạnh lại:

```cpp
--perimeter;
```

thì vẫn đúng nếu ban đầu mỗi ô cộng `4`, vì cạnh chung sẽ bị trừ hai lần qua hai ô.

Nhưng nếu bạn đang dùng công thức đếm `sharedEdges` thì không được nhầm lẫn hai mô hình.

---

## Lỗi 3: Quên xử lý biên

Ví dụ:

```text
1
```

Nếu chỉ kiểm tra:

```cpp
grid[nr][nc] == 0
```

mà không kiểm tra `nr`, `nc` có hợp lệ hay không, code có thể truy cập ngoài mảng.

Phải kiểm tra boundary trước.

---

## Lỗi 4: Coi đường chéo là hàng xóm

Không được xem:

```text
1 0
0 1
```

là hai ô kề nhau.

Chỉ có 4 hướng:

```text
up
down
left
right
```

Không có:

```text
up-left
up-right
down-left
down-right
```

---

## Lỗi 5: Dùng DFS/BFS không cần thiết

DFS/BFS không sai về mặt khả năng giải bài, nhưng nó giải một bài toán phức tạp hơn mức cần thiết.

Ở đây không cần tìm component.

Chỉ cần local information.

---

# 29. Tư duy phỏng vấn

Nếu gặp bài này trong interview, có thể trình bày theo thứ tự sau.

### Bước 1

Nói:

> Mỗi ô đất là một hình vuông có 4 cạnh.

### Bước 2

Nói:

> Một cạnh chỉ thuộc chu vi nếu phía bên kia của cạnh là nước hoặc nằm ngoài grid.

### Bước 3

Nói:

> Vì mỗi ô có 4 cạnh nên với mỗi ô đất, tôi kiểm tra 4 hướng.

### Bước 4

Nói:

> Nếu hàng xóm nằm ngoài grid hoặc là nước, tôi cộng 1 vào perimeter.

### Bước 5

Nêu complexity:

```text
O(R * C) time
O(1) extra space
```

Đây là một lời giải ngắn gọn nhưng thể hiện đúng insight.

---

# 30. Nếu muốn trình bày bằng công thức

Một cách giải thích khác:

> Ban đầu mỗi ô đất đóng góp 4 cạnh. Nếu hai ô đất kề nhau, cạnh chung được tính hai lần nhưng không thuộc chu vi, vì vậy phải trừ 2.

Do đó:

```text
perimeter = 4 * landCells - 2 * sharedEdges
```

Sau đó nói:

> Để tránh đếm cùng một cạnh chung hai lần, với mỗi ô tôi chỉ kiểm tra hàng xóm bên phải và bên dưới.

Implementation:

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                perimeter += 4;

                if (r + 1 < rows && grid[r + 1][c] == 1) {
                    perimeter -= 2;
                }

                if (c + 1 < cols && grid[r][c + 1] == 1) {
                    perimeter -= 2;
                }
            }
        }

        return perimeter;
    }
};
```

---

# 31. Tại sao đây là một bài "invariant"

Một invariant quan trọng là:

> Sau khi xử lý một tập các ô đất, `perimeter` luôn bằng số cạnh đang được xem là nằm trên biên của tập ô đó.

Trong implementation `+4/-2`:

- Khi thêm một ô đất mới, ta tưởng tượng nó độc lập -> `+4`.
- Mỗi cạnh của nó tiếp xúc với một ô đất đã tồn tại làm mất đi 2 đơn vị chu vi:
  - một cạnh của ô mới
  - một cạnh của ô cũ

Do đó:

```text
+4
-2 cho mỗi cạnh chung
```

luôn duy trì đúng giá trị chu vi.

Đây là cách suy nghĩ rất hữu ích khi thiết kế thuật toán.

---

# 32. Tổng quát hóa insight

Pattern này không chỉ dùng cho Island Perimeter.

Nó có thể tổng quát thành:

> Khi một cấu trúc được ghép từ nhiều phần tử có biên riêng, hãy tính tổng biên của từng phần tử rồi loại bỏ các biên nội bộ xuất hiện do sự tiếp xúc giữa các phần tử.

Ví dụ:

- các ô vuông tạo thành hình dạng
- các ô lục giác
- các voxel trong không gian 3D
- các block trong ma trận
- các vùng được ghép từ nhiều cell

Trong 3D, ý tưởng tương tự có thể dẫn đến:

```text
surface area = tổng diện tích mặt của voxel
               - diện tích các mặt tiếp xúc
```

Với Island Perimeter, phiên bản 2D đơn giản hơn:

```text
perimeter = 4 * số ô
            - 2 * số cạnh chung
```

---

# 33. Có cần sửa grid không?

Không.

Ta chỉ đọc:

```cpp
grid[r][c]
```

và tính toán.

Không cần:

```cpp
grid[r][c] = ...
```

Do đó input được giữ nguyên.

Đây cũng là lý do space complexity là:

```text
O(1)
```

nếu không tính bộ nhớ của input.

---

# 34. Có thể dùng recursion không?

Có thể viết DFS để duyệt đảo, nhưng không cần.

Ví dụ nếu DFS từ một ô đất:

- nếu đi ra ngoài grid -> có thể cộng 1
- nếu gặp nước -> cộng 1
- nếu gặp đất chưa thăm -> tiếp tục DFS
- nếu gặp đất đã thăm -> không cộng

Cách này có thể cho kết quả đúng, nhưng cần:

```text
visited
```

hoặc sửa grid.

Ngoài ra recursion có thể tạo vấn đề stack nếu grid lớn.

Do đó với bài này, cách duyệt trực tiếp toàn grid tốt hơn về sự đơn giản và bộ nhớ.

---

# 35. Có thể chỉ đếm ô nước không?

Có thể suy luận chu vi thông qua các vùng nước xung quanh, nhưng không cần thiết.

Cách trực tiếp hơn là:

```text
land cell -> kiểm tra 4 cạnh
```

Đây là một nguyên tắc quan trọng:

> Khi output được định nghĩa trực tiếp trên các phần tử input, hãy ưu tiên tính trực tiếp output từ các phần tử đó thay vì tạo thêm một mô hình trung gian.

---

# 36. Cách nhớ bài trong 10 giây

Khi gặp:

```text
Island Perimeter
```

hãy nhớ:

```text
Mỗi land cell có 4 cạnh.
```

Sau đó:

```text
Cạnh gặp nước -> +1
Cạnh ra ngoài grid -> +1
Cạnh gặp land -> +0
```

Hoặc:

```text
answer = 4 * land
       - 2 * shared edges
```

Chỉ cần nhớ một trong hai cách là đủ.

---

# 37. Template C++ nên ghi nhớ

Template tổng quát:

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int ans = 0;

        static const int dr[4] = {-1, 1, 0, 0};
        static const int dc[4] = {0, 0, -1, 1};

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                for (int k = 0; k < 4; ++k) {
                    const int nr = r + dr[k];
                    const int nc = c + dc[k];

                    if (nr < 0 || nr >= rows ||
                        nc < 0 || nc >= cols ||
                        grid[nr][nc] == 0) {
                        ++ans;
                    }
                }
            }
        }

        return ans;
    }
};
```

---

# 38. Checklist trước khi submit

Trước khi submit, kiểm tra:

- [x] Chỉ ô `1` mới được xử lý.
- [x] Mỗi ô đất có 4 cạnh.
- [x] Có kiểm tra cả 4 hướng.
- [x] Có xử lý biên grid.
- [x] Không tính đường chéo.
- [x] Ô đất kề ô đất không cộng chu vi.
- [x] Không cần DFS/BFS.
- [x] Time complexity là `O(R * C)`.
- [x] Extra space là `O(1)`.

---

# 39. Kết luận

Island Perimeter là một bài grid rất cơ bản nhưng quan trọng vì nó dạy một pattern có tính tái sử dụng cao.

Insight cốt lõi là:

```text
Mỗi ô đất có 4 cạnh.
```

Một cạnh trở thành chu vi nếu:

```text
- nó nằm ngoài grid
hoặc
- ô phía bên kia là nước
```

Do đó có thể duyệt toàn bộ grid và kiểm tra 4 hướng.

Lời giải trực tiếp:

```text
Time  = O(R * C)
Space = O(1)
```

Nếu muốn nhìn bài toán theo hướng toán học hơn:

```text
perimeter = 4 * landCells - 2 * sharedEdges
```

Hai cách đều có cùng độ phức tạp. Trong thực tế, cách kiểm tra 4 hướng thường dễ đọc và khó sai hơn; cách `+4/-2` thể hiện invariant rất đẹp và là một insight đáng ghi nhớ.

## Lời giải C++ đề xuất

```cpp
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        const int rows = grid.size();
        const int cols = grid[0].size();

        int perimeter = 0;

        static const int dr[4] = {-1, 1, 0, 0};
        static const int dc[4] = {0, 0, -1, 1};

        for (int r = 0; r < rows; ++r) {
            for (int c = 0; c < cols; ++c) {
                if (grid[r][c] == 0) {
                    continue;
                }

                for (int k = 0; k < 4; ++k) {
                    const int nr = r + dr[k];
                    const int nc = c + dc[k];

                    if (nr < 0 || nr >= rows ||
                        nc < 0 || nc >= cols ||
                        grid[nr][nc] == 0) {
                        ++perimeter;
                    }
                }
            }
        }

        return perimeter;
    }
};
```

Đây là lời giải tuyến tính theo kích thước input và sử dụng `O(1)` bộ nhớ phụ.
