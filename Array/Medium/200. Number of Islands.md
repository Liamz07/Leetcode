# Number of Islands --- LeetCode

> **Bài toán:** [Number of
> Islands](https://leetcode.com/problems/number-of-islands/)\
> **Chủ đề:** Grid, Graph, DFS, BFS, Connected Components, Flood Fill

## 1. Phân tích bài toán

Cho ma trận `grid` gồm `'1'` (đất) và `'0'` (nước). Hai ô đất thuộc cùng
một hòn đảo nếu chúng kết nối theo **4 hướng**: trên, dưới, trái, phải.
Đường chéo không được tính.

Ví dụ:

``` text
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1
```

Có 3 đảo.

------------------------------------------------------------------------

## 2. Nhận diện bản chất: Grid → Graph

Đây thực chất là bài **Connected Components**:

-   Mỗi ô `'1'` là một node.
-   Hai ô `'1'` kề nhau theo 4 hướng là hai node có cạnh nối.
-   Một nhóm các node liên thông là một connected component.
-   Mỗi connected component chính là một hòn đảo.

Vì vậy:

``` text
Number of Islands
        ↓
Connected Components
        ↓
DFS / BFS
```

Đây là một pattern rất quan trọng trong các bài Grid.

------------------------------------------------------------------------

## 3. Ý tưởng cốt lõi

Duyệt toàn bộ ma trận.

Khi gặp:

``` cpp
grid[i][j] == '1'
```

thì ta vừa phát hiện một đảo chưa được khám phá:

``` cpp
ans++;
```

Sau đó dùng DFS hoặc BFS để khám phá **toàn bộ** các ô đất kết nối với ô
đó.

Các ô đã khám phá được đánh dấu để không bị đếm lại.

Một cách đơn giản là đổi trực tiếp:

``` text
'1' → '0'
```

Ví dụ:

``` text
1 1 0
1 0 0
0 0 1
```

Sau khi xử lý đảo đầu tiên:

``` text
0 0 0
0 0 0
0 0 1
```

Nhờ vậy, vòng lặp bên ngoài sẽ không đếm lại đảo đó.

------------------------------------------------------------------------

## 4. Vì sao không cần `visited`?

Ta nhận:

``` cpp
vector<vector<char>>& grid
```

bằng reference nên có thể sửa trực tiếp Grid:

``` cpp
grid[i][j] = '0';
```

Do đó không cần tạo thêm:

``` cpp
vector<vector<bool>> visited;
```

Nếu đề bài không cho phép sửa Grid thì mới cần một cấu trúc `visited`
riêng.

------------------------------------------------------------------------

## 5. DFS --- Depth First Search

DFS sẽ đi sâu từ một ô đất tới các ô đất liên thông.

Hàm DFS cần thực hiện:

1.  Kiểm tra ô có nằm trong Grid không.
2.  Nếu là nước hoặc đã thăm thì dừng.
3.  Đánh dấu ô hiện tại.
4.  Đi tiếp 4 hướng.

``` cpp
void dfs(vector<vector<char>>& grid, int i, int j) {
    int m = grid.size();
    int n = grid[0].size();

    if (i < 0 || i >= m || j < 0 || j >= n) {
        return;
    }

    if (grid[i][j] == '0') {
        return;
    }

    grid[i][j] = '0';

    dfs(grid, i - 1, j);
    dfs(grid, i + 1, j);
    dfs(grid, i, j - 1);
    dfs(grid, i, j + 1);
}
```

### Tại sao đánh dấu trước khi DFS tiếp?

Phải đánh dấu:

``` cpp
grid[i][j] = '0';
```

ngay khi bước vào ô. Nếu đánh dấu quá muộn, DFS có thể quay lại những ô
đã đi và tạo vòng lặp.

Ví dụ:

``` text
1 1
1 1
```

Nếu `(0,0)` chưa được đánh dấu, đường đi có thể quay lại `(0,0)` từ
`(1,0)` hoặc `(0,1)`. Đánh dấu ngay lập tức đảm bảo mỗi ô chỉ được xử lý
một lần.

------------------------------------------------------------------------

## 6. Tại sao `ans` chỉ tăng một lần cho mỗi đảo?

Không tăng `ans` trong DFS.

Ta chỉ tăng khi vòng lặp chính phát hiện một `'1'` chưa được xử lý:

``` cpp
if (grid[i][j] == '1') {
    ans++;
    dfs(grid, i, j);
}
```

Nếu một đảo có 100 ô, `ans` vẫn chỉ tăng **1 lần**. DFS sau đó biến cả
100 ô thành `'0'`.

------------------------------------------------------------------------

## 7. Dry Run

Cho:

``` text
1 1 0
1 0 0
0 0 1
```

Ban đầu:

``` text
ans = 0
```

Gặp `(0,0)` là `'1'`:

``` text
ans = 1
```

DFS xử lý `(0,0)`, `(0,1)`, `(1,0)` và Grid trở thành:

``` text
0 0 0
0 0 0
0 0 1
```

Tiếp tục đến `(2,2)`:

``` text
ans = 2
```

DFS đánh dấu ô cuối cùng.

Kết quả:

``` text
2
```

------------------------------------------------------------------------

## 8. Dùng `dx`, `dy` để biểu diễn 4 hướng

Có thể thay 4 lời gọi riêng bằng:

``` cpp
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
```

Trong đó:

    `k`   `dx[k]`   `dy[k]` Hướng
  ----- --------- --------- -------
      0        -1         0 trên
      1         1         0 dưới
      2         0        -1 trái
      3         0         1 phải

Đây là cách rất tiện để tái sử dụng cho các bài Grid.

------------------------------------------------------------------------

## 9. Lời giải DFS tối ưu

``` cpp
class Solution {
public:
    void dfs(vector<vector<char>>& grid, int i, int j) {
        int m = grid.size();
        int n = grid[0].size();

        // Nằm ngoài Grid
        if (i < 0 || i >= m || j < 0 || j >= n) {
            return;
        }

        // Nước hoặc ô đã thăm
        if (grid[i][j] == '0') {
            return;
        }

        // Đánh dấu ngay khi thăm
        grid[i][j] = '0';

        // 4 hướng
        dfs(grid, i - 1, j);
        dfs(grid, i + 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }

    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    // Phát hiện đảo mới
                    ans++;

                    // Khám phá toàn bộ đảo
                    dfs(grid, i, j);
                }
            }
        }

        return ans;
    }
};
```

------------------------------------------------------------------------

## 10. Phiên bản DFS dùng `dx`, `dy`

``` cpp
class Solution {
public:
    int dx[4] = {-1, 1, 0, 0};
    int dy[4] = {0, 0, -1, 1};

    void dfs(vector<vector<char>>& grid, int i, int j) {
        int m = grid.size();
        int n = grid[0].size();

        grid[i][j] = '0';

        for (int k = 0; k < 4; k++) {
            int ni = i + dx[k];
            int nj = j + dy[k];

            if (ni < 0 || ni >= m || nj < 0 || nj >= n) {
                continue;
            }

            if (grid[ni][nj] == '0') {
                continue;
            }

            dfs(grid, ni, nj);
        }
    }

    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    ans++;
                    dfs(grid, i, j);
                }
            }
        }

        return ans;
    }
};
```

------------------------------------------------------------------------

## 11. BFS cũng giải được

Thay DFS bằng BFS hoàn toàn được.

Khi gặp một đảo mới:

``` cpp
ans++;
```

đưa ô đầu tiên vào `queue`, đồng thời đánh dấu nó:

``` cpp
queue<pair<int, int>> q;
q.push({i, j});
grid[i][j] = '0';
```

Sau đó liên tục:

1.  Lấy một ô khỏi queue.
2.  Kiểm tra 4 hướng.
3.  Nếu gặp `'1'`, đánh dấu thành `'0'` và đưa vào queue.
4.  Lặp tới khi queue rỗng.

``` cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int ans = 0;

        int dx[4] = {-1, 1, 0, 0};
        int dy[4] = {0, 0, -1, 1};

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '0') {
                    continue;
                }

                ans++;

                queue<pair<int, int>> q;
                q.push({i, j});
                grid[i][j] = '0';

                while (!q.empty()) {
                    int x = q.front().first;
                    int y = q.front().second;
                    q.pop();

                    for (int k = 0; k < 4; k++) {
                        int nx = x + dx[k];
                        int ny = y + dy[k];

                        if (nx < 0 || nx >= m || ny < 0 || ny >= n) {
                            continue;
                        }

                        if (grid[nx][ny] == '0') {
                            continue;
                        }

                        grid[nx][ny] = '0';
                        q.push({nx, ny});
                    }
                }
            }
        }

        return ans;
    }
};
```

------------------------------------------------------------------------

## 12. DFS hay BFS?

Cả hai đều có:

``` text
Time = O(m × n)
```

Trong trường hợp xấu nhất:

``` text
Space = O(m × n)
```

### DFS

Ưu điểm:

-   Code ngắn.
-   Tự nhiên với connected component.

Nhược điểm:

-   DFS đệ quy sử dụng call stack.
-   Vùng đất rất lớn có thể làm recursion depth lớn.

### BFS

Ưu điểm:

-   Không phụ thuộc call stack.
-   Dùng `queue` để quản lý các ô cần xử lý.

Nhược điểm:

-   Code dài hơn một chút.

Với bài này, cả DFS và BFS đều là lời giải tối ưu về thời gian.

------------------------------------------------------------------------

## 13. Độ phức tạp

Gọi:

-   `m` = số hàng.
-   `n` = số cột.

Có `m × n` ô.

Mỗi ô chỉ được xử lý một lần và mỗi lần chỉ kiểm tra tối đa 4 hướng.

Do đó:

``` text
Time Complexity = O(m × n)
```

Với DFS đệ quy:

``` text
Space Complexity = O(m × n)
```

trong trường hợp xấu nhất do recursion stack.

Với BFS, queue cũng có thể cần:

``` text
O(m × n)
```

bộ nhớ trong trường hợp xấu nhất.

Không cần thêm `visited` vì đã tận dụng `grid` để đánh dấu.

------------------------------------------------------------------------

## 14. Vì sao `O(m × n)` là tối ưu?

Trong trường hợp xấu nhất, toàn bộ `m × n` ô đều có thể ảnh hưởng đến
đáp án. Vì vậy thuật toán phải có khả năng kiểm tra toàn bộ Grid.

Cận dưới tự nhiên là:

``` text
Ω(m × n)
```

DFS/BFS đạt:

``` text
O(m × n)
```

nên đạt tối ưu về asymptotic time complexity.

------------------------------------------------------------------------

## 15. Những lỗi thường gặp

### Lỗi 1 --- Tăng `ans` trong DFS

Sai:

``` cpp
void dfs(...) {
    ans++;
    ...
}
```

Một đảo có thể có nhiều ô nên sẽ bị đếm nhiều lần.

Đúng:

``` cpp
if (grid[i][j] == '1') {
    ans++;
    dfs(grid, i, j);
}
```

### Lỗi 2 --- Đánh dấu quá muộn

Không nên đợi DFS xong mới đánh dấu. Hãy đánh dấu ngay khi thăm:

``` cpp
grid[i][j] = '0';
```

### Lỗi 3 --- Quên kiểm tra biên

Phải đảm bảo:

``` text
0 <= i < m
0 <= j < n
```

trước khi truy cập `grid[i][j]`.

### Lỗi 4 --- Tính cả đường chéo

Chỉ có 4 hướng hợp lệ:

``` text
↑ ↓ ← →
```

### Lỗi 5 --- Tạo `visited` không cần thiết

Nếu được phép sửa Grid, dùng:

``` cpp
grid[i][j] = '0';
```

sẽ tiết kiệm bộ nhớ hơn so với ma trận `visited` riêng.

------------------------------------------------------------------------

## 16. Pattern quan trọng: Grid → Graph

Khi gặp một bài Grid, hãy thử tự hỏi:

> "Các ô có thể xem như node của một Graph không?"

Nếu có, hãy xác định:

``` text
Node      = ô
Neighbor  = ô kề
Traversal = DFS / BFS
```

Một số pattern liên quan:

  Bài toán                Pattern
  ----------------------- -------------------------------------
  Number of Islands       Connected Components
  Max Area of Island      Connected Component + đếm diện tích
  Flood Fill              DFS/BFS từ source
  Rotting Oranges         Multi-source BFS
  Shortest Path in Grid   BFS
  Surrounded Regions      DFS/BFS từ boundary

------------------------------------------------------------------------

## 17. Tư duy tổng quát

Template:

``` text
Duyệt toàn bộ Grid
        ↓
Gặp ô chưa xử lý và hợp lệ?
        ↓
       Có
        ↓
Phát hiện component mới
        ↓
answer++
        ↓
DFS / BFS toàn bộ component
        ↓
Đánh dấu các ô đã thăm
        ↓
Tiếp tục
```

Với Number of Islands:

``` text
component = island
```

nên mỗi lần gặp `'1'` chưa xử lý:

``` cpp
answer++;
```

------------------------------------------------------------------------

## 18. Chứng minh tính đúng đắn

### Ý 1 --- Mỗi lần tăng `ans` là một đảo mới

Vòng lặp chỉ tăng `ans` khi gặp `'1'`. Những ô của các đảo đã xử lý
trước đó đều đã được đổi thành `'0'`. Vì vậy ô hiện tại thuộc một
component chưa được khám phá.

### Ý 2 --- DFS/BFS khám phá toàn bộ đảo

Từ ô bắt đầu, thuật toán đi qua cả 4 hướng. Bất kỳ ô đất nào liên thông
với nó thông qua một chuỗi các ô kề nhau đều có thể được tiếp cận.

### Ý 3 --- Không đảo nào bị đếm hai lần

Sau khi DFS/BFS hoàn thành, mọi ô thuộc component đã thành `'0'`. Vì vậy
khi vòng lặp gặp lại chúng, điều kiện `grid[i][j] == '1'` không còn
đúng.

Suy ra mỗi đảo được đếm đúng một lần.

------------------------------------------------------------------------

## 19. Final Code --- DFS

``` cpp
class Solution {
public:
    void dfs(vector<vector<char>>& grid, int i, int j) {
        int m = grid.size();
        int n = grid[0].size();

        if (i < 0 || i >= m || j < 0 || j >= n) {
            return;
        }

        if (grid[i][j] == '0') {
            return;
        }

        grid[i][j] = '0';

        dfs(grid, i - 1, j);
        dfs(grid, i + 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }

    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    ans++;
                    dfs(grid, i, j);
                }
            }
        }

        return ans;
    }
};
```

------------------------------------------------------------------------

## 20. Complexity Summary

  Thành phần                        Độ phức tạp
  --------------------- -----------------------
  Duyệt Grid                         `O(m × n)`
  DFS/BFS                            `O(m × n)`
  **Tổng thời gian**               **O(m × n)**
  DFS recursion stack     `O(m × n)` worst case
  BFS queue               `O(m × n)` worst case
  `visited` bổ sung                   Không cần

------------------------------------------------------------------------

## 21. Key Insight cần nhớ

### 1. Grid có thể xem như Graph

``` text
Ô = node
Ô kề nhau = edge
Đảo = connected component
```

### 2. Gặp `'1'` chưa xử lý → tìm thấy một đảo mới

``` cpp
ans++;
```

Sau đó DFS/BFS toàn bộ đảo.

### 3. Đánh dấu ngay khi thăm

``` cpp
grid[i][j] = '0';
```

để một ô không bị xử lý nhiều lần.

------------------------------------------------------------------------

## Tóm tắt một câu

> **Number of Islands là bài toán đếm Connected Components trên Grid:
> duyệt từng ô, mỗi khi gặp một `'1'` chưa thăm thì tăng đáp án và dùng
> DFS/BFS để đánh dấu toàn bộ hòn đảo đó.**
