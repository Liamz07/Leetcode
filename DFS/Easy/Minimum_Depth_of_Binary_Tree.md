# Minimum Depth of Binary Tree

[**Minimum Depth of Binary Tree**](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

## 1. Phân tích bài toán

Cho một cây nhị phân `root`.

Yêu cầu: tìm **độ sâu nhỏ nhất** của cây.

Độ sâu của một đường đi được tính bằng **số lượng node** trên đường đi từ node gốc đến một **node lá** gần nhất.

Một node được gọi là **node lá (leaf)** khi:

- Không có node con trái.
- Không có node con phải.

Ví dụ:

```text
        3
       / \
      9  20
         / \
        15  7
```

Các node lá là:

```text
9, 15, 7
```

Các đường đi từ root đến node lá:

```text
3 -> 9
3 -> 20 -> 15
3 -> 20 -> 7
```

Độ sâu tương ứng:

```text
2
3
3
```

Vì đường đi ngắn nhất có độ dài `2`, kết quả là:

```text
2
```

---

## 2. Điểm quan trọng cần chú ý

Bài toán này rất dễ nhầm với việc chỉ lấy độ sâu nhỏ nhất giữa cây con trái và cây con phải.

Ta không thể đơn giản viết:

```text
1 + min(depth(left), depth(right))
```

vì một trong hai cây con có thể là `NULL`.

Ví dụ:

```text
    1
     \
      2
```

Ở đây:

- `left = NULL`
- `right = 2`

Node `1` không phải node lá.

Nếu áp dụng máy móc:

```text
1 + min(depth(left), depth(right))
```

thì có thể chọn nhánh `NULL` và cho ra kết quả sai.

Đường đi hợp lệ duy nhất là:

```text
1 -> 2
```

nên minimum depth phải bằng:

```text
2
```

Vì vậy, khi xử lý bài toán này cần đặc biệt chú ý trường hợp node chỉ có **một node con**.

---

# 3. Phương hướng tiếp cận

Có hai hướng phổ biến:

1. DFS (Depth-First Search)
2. BFS (Breadth-First Search)

Trong bài toán này, **BFS là cách tiếp cận rất tự nhiên và tối ưu về mặt ý tưởng**.

Lý do là ta đang cần tìm:

> Node lá gần root nhất.

BFS duyệt cây theo từng tầng:

```text
Tầng 1: root
Tầng 2: các node con của root
Tầng 3: các node ở xa hơn
...
```

Do đó, **node lá đầu tiên mà BFS gặp chính là node lá có khoảng cách nhỏ nhất đến root**.

Ngay khi gặp node lá, ta có thể trả về độ sâu hiện tại và không cần duyệt tiếp.

---

# 4. Vì sao BFS phù hợp với bài toán?

Hãy xét cây:

```text
          1
        /   \
       2     3
      / \     \
     4   5     6
```

BFS sẽ duyệt theo thứ tự:

```text
1
2, 3
4, 5, 6
```

Ta kiểm tra từng tầng.

### Tầng 1

```text
1
```

`1` không phải node lá vì có node con.

### Tầng 2

```text
2, 3
```

`2` có hai node con.

`3` có node con phải là `6`.

Vì vậy chưa có node lá.

### Tầng 3

```text
4, 5, 6
```

`4` là node lá.

Ngay khi gặp `4`, ta biết rằng đây là node lá gần root nhất.

Vậy:

```text
minimum depth = 3
```

Không cần kiểm tra tiếp `5` và `6`.

---

# 5. Cấu trúc dữ liệu cần sử dụng

Để thực hiện BFS, ta sử dụng:

```cpp
queue
```

Mỗi phần tử trong queue là một node của cây.

Ta cần biết thêm node hiện tại đang ở độ sâu nào.

Có thể lưu cả:

```text
(node, depth)
```

Ví dụ:

```text
(root, 1)
```

Khi lấy một node ra khỏi queue:

- Nếu node là lá -> trả về `depth`.
- Nếu có con trái -> đưa `(left, depth + 1)` vào queue.
- Nếu có con phải -> đưa `(right, depth + 1)` vào queue.

---

# 6. Điều kiện xác định node lá

Một node là node lá khi:

```cpp
root->left == nullptr && root->right == nullptr
```

Có nghĩa là node đó không có bất kỳ node con nào.

Đây là điều kiện quan trọng nhất của bài toán.

Không được kiểm tra chỉ:

```cpp
root->left == nullptr
```

vì node có thể không có con trái nhưng vẫn có con phải.

Ví dụ:

```text
1
 \
  2
```

Node `1` không phải node lá.

---

# 7. Xử lý cây rỗng

Nếu:

```cpp
root == nullptr
```

thì cây không có node nào.

Minimum depth được quy ước là:

```text
0
```

Do đó ta xử lý ngay đầu hàm:

```cpp
if (root == nullptr)
    return 0;
```

---

# 8. Thuật toán BFS từng bước

Giả sử:

```text
        3
       / \
      9  20
         / \
        15  7
```

### Bước 1

Đưa root vào queue:

```text
queue:
(3, 1)
```

### Bước 2

Lấy:

```text
(3, 1)
```

`3` không phải node lá.

Đưa hai node con vào queue:

```text
queue:
(9, 2)
(20, 2)
```

### Bước 3

Lấy:

```text
(9, 2)
```

`9` không có con trái và không có con phải.

Vậy `9` là node lá.

Ta trả về:

```text
2
```

Không cần duyệt tiếp node `20`.

---

# 9. Vì sao node lá đầu tiên tìm được là đáp án?

Đây là điểm quan trọng nhất của BFS.

BFS luôn duyệt các node theo thứ tự khoảng cách từ root tăng dần.

Ví dụ:

```text
Tầng 1: khoảng cách 0 từ root
Tầng 2: khoảng cách 1 từ root
Tầng 3: khoảng cách 2 từ root
...
```

Nếu một node lá xuất hiện ở tầng `d`, thì:

- Không thể có node lá nào ở tầng nhỏ hơn `d`, vì BFS đã duyệt hết các tầng trước đó.
- Vì vậy `d` chính là minimum depth.

Nói cách khác:

> BFS đảm bảo rằng lần đầu tiên gặp một node lá, đó chính là node lá gần root nhất.

Đây cũng là lý do ta có thể `return` ngay khi tìm thấy node lá.

---

# 10. Cách cài đặt queue

Ta có thể sử dụng:

```cpp
queue<pair<TreeNode*, int>> q;
```

Trong đó:

```text
TreeNode*
```

là node hiện tại.

```text
int
```

là độ sâu của node đó.

Ban đầu:

```cpp
q.push({root, 1});
```

Có nghĩa là root có độ sâu `1`.

---

# 11. Vòng lặp BFS

Ta tiếp tục xử lý khi queue còn phần tử:

```cpp
while (!q.empty())
```

Lấy phần tử đầu queue:

```cpp
auto [node, depth] = q.front();
q.pop();
```

Sau đó kiểm tra:

```cpp
if (node->left == nullptr && node->right == nullptr)
    return depth;
```

Nếu chưa phải node lá, thêm các node con vào queue.

---

# 12. Thêm node con trái

Nếu có con trái:

```cpp
if (node->left != nullptr)
    q.push({node->left, depth + 1});
```

Node con trái nằm ở tầng tiếp theo nên độ sâu là:

```text
depth + 1
```

---

# 13. Thêm node con phải

Tương tự:

```cpp
if (node->right != nullptr)
    q.push({node->right, depth + 1});
```

Ta chỉ thêm node tồn tại vào queue.

Điều này cũng giúp xử lý chính xác trường hợp node chỉ có một con.

---

# 14. Trường hợp đặc biệt: node chỉ có một con

Xét cây:

```text
    1
     \
      2
       \
        3
```

BFS:

```text
(1, 1)
(2, 2)
(3, 3)
```

Node `1` không phải lá.

Node `2` cũng không phải lá.

Node `3` là lá.

Kết quả:

```text
3
```

Thuật toán không bị nhầm `nullptr` với một node lá.

Đây là ưu điểm lớn của cách tiếp cận BFS trực tiếp.

---

# 15. Trường hợp cây chỉ có root

Ví dụ:

```text
1
```

Root vừa là:

- node gốc
- node lá

Queue ban đầu:

```text
(1, 1)
```

Lấy `1` ra.

Vì:

```cpp
node->left == nullptr
node->right == nullptr
```

nên trả về:

```text
1
```

Kết quả chính xác.

---

# 16. Trường hợp cây rỗng

Ví dụ:

```text
root = nullptr
```

Ngay lập tức:

```cpp
if (root == nullptr)
    return 0;
```

Kết quả:

```text
0
```

---

# 17. Độ phức tạp

## Time Complexity

Trong trường hợp xấu nhất, BFS có thể phải duyệt toàn bộ `n` node.

Do đó:

```text
O(n)
```

Trong trường hợp tốt, nếu root hoặc một node ở tầng rất gần root là node lá, thuật toán có thể dừng sớm.

Tuy nhiên độ phức tạp trong trường hợp xấu nhất vẫn là:

```text
O(n)
```

---

## Space Complexity

Queue có thể chứa nhiều node cùng một tầng.

Với cây nhị phân, số node lớn nhất có thể xuất hiện ở một tầng là O(n).

Do đó:

```text
O(n)
```

Trong trường hợp cây cân bằng, queue thường có kích thước tương ứng với số node ở tầng rộng nhất.

---

# 18. Có thể dùng DFS không?

Có.

DFS cũng giải được bài toán với độ phức tạp:

```text
Time: O(n)
Space: O(h)
```

với `h` là chiều cao của cây.

Tuy nhiên, DFS cần xử lý cẩn thận trường hợp một cây con là `nullptr`.

Ví dụ:

```text
    1
     \
      2
```

Không thể đơn giản lấy:

```text
1 + min(leftDepth, rightDepth)
```

mà phải xử lý riêng:

- Nếu không có con trái -> lấy độ sâu cây con phải.
- Nếu không có con phải -> lấy độ sâu cây con trái.
- Nếu có cả hai -> lấy minimum của hai cây con.

BFS tránh được cách xử lý này và phù hợp trực tiếp với bản chất "tìm node lá gần nhất".

---

# 19. So sánh BFS và DFS

| Tiêu chí | BFS | DFS |
|---|---|---|
| Ý tưởng | Duyệt theo từng tầng | Đi sâu từng nhánh |
| Tìm node lá gần nhất | Rất tự nhiên | Cần xử lý thêm |
| Có thể dừng sớm | Có, khi gặp lá đầu tiên | Không đơn giản như BFS |
| Time | O(n) | O(n) |
| Space | O(n) | O(h) |
| Độ dễ cài đặt | Dễ | Dễ nhưng dễ sai ở case một con |

Với bài toán này, BFS là một lựa chọn rất phù hợp vì mục tiêu chính là tìm **node lá gần root nhất**.

---

# 20. Lời giải C++ tối ưu

```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr)
            return 0;

        queue<pair<TreeNode*, int>> q;
        q.push({root, 1});

        while (!q.empty()) {
            auto [node, depth] = q.front();
            q.pop();

            // Neu node khong co con, day la node la
            if (node->left == nullptr && node->right == nullptr)
                return depth;

            // Them node con trai neu ton tai
            if (node->left != nullptr)
                q.push({node->left, depth + 1});

            // Them node con phai neu ton tai
            if (node->right != nullptr)
                q.push({node->right, depth + 1});
        }

        return 0;
    }
};
```

---

# 21. Giải thích code

### Kiểm tra cây rỗng

```cpp
if (root == nullptr)
    return 0;
```

Nếu cây không có node nào thì minimum depth bằng `0`.

---

### Khởi tạo queue

```cpp
queue<pair<TreeNode*, int>> q;
```

Mỗi phần tử gồm:

```text
TreeNode*
```

và:

```text
depth
```

Sau đó đưa root vào:

```cpp
q.push({root, 1});
```

Root có độ sâu bằng `1`.

---

### Duyệt BFS

```cpp
while (!q.empty())
```

Tiếp tục cho đến khi tìm được node lá.

---

### Lấy node đầu queue

```cpp
auto [node, depth] = q.front();
q.pop();
```

Ta lấy ra:

- `node`: node hiện tại.
- `depth`: độ sâu của node đó.

---

### Kiểm tra node lá

```cpp
if (node->left == nullptr && node->right == nullptr)
    return depth;
```

Nếu node không có con nào, đây là node lá.

Do BFS duyệt theo từng tầng nên đây là node lá gần root nhất.

Ta có thể trả kết quả ngay.

---

### Thêm con trái

```cpp
if (node->left != nullptr)
    q.push({node->left, depth + 1});
```

Nếu có con trái, đưa nó vào queue với độ sâu tăng thêm `1`.

---

### Thêm con phải

```cpp
if (node->right != nullptr)
    q.push({node->right, depth + 1});
```

Tương tự với con phải.

---

# 22. Ví dụ mô phỏng

Cho cây:

```text
        1
       / \
      2   3
     /
    4
```

Node lá:

```text
4, 3
```

Các đường đi:

```text
1 -> 2 -> 4
1 -> 3
```

Độ sâu:

```text
3
2
```

Đáp án:

```text
2
```

### Quá trình BFS

Ban đầu:

```text
queue = [(1, 1)]
```

Lấy `1`:

```text
queue = [(2, 2), (3, 2)]
```

`1` không phải lá.

Lấy `2`:

```text
queue = [(3, 2), (4, 3)]
```

`2` không phải lá.

Lấy `3`:

`3` là node lá.

Trả về:

```text
2
```

Ta không cần duyệt node `4`.

---

# 23. Tại sao không cần duyệt toàn bộ cây?

Đây là điểm giúp BFS đặc biệt phù hợp với bài này.

Nếu mục tiêu là tìm **minimum depth**, thì sau khi tìm thấy node lá đầu tiên:

```text
không có node lá nào ở tầng thấp hơn
```

vì tất cả các tầng thấp hơn đã được BFS duyệt qua.

Do đó:

```cpp
return depth;
```

ngay lập tức là hoàn toàn chính xác.

---

# 24. Một cách viết BFS khác

Ta cũng có thể không lưu `depth` cùng với từng node.

Thay vào đó, xử lý từng tầng:

```cpp
int depth = 1;

while (!q.empty()) {
    int soLuong = q.size();

    while (soLuong--) {
        TreeNode* node = q.front();
        q.pop();

        if (node->left == nullptr && node->right == nullptr)
            return depth;

        if (node->left != nullptr)
            q.push(node->left);

        if (node->right != nullptr)
            q.push(node->right);
    }

    depth++;
}
```

Cách này cũng có độ phức tạp:

```text
Time: O(n)
Space: O(n)
```

Tuy nhiên, cách lưu trực tiếp:

```cpp
pair<TreeNode*, int>
```

dễ theo dõi độ sâu của từng node hơn.

---

# 25. Kết luận

Đối với bài **Minimum Depth of Binary Tree**, ý tưởng quan trọng nhất là:

1. Minimum depth là khoảng cách từ root đến **node lá gần nhất**.
2. BFS duyệt cây theo từng tầng từ gần đến xa.
3. Vì vậy, node lá đầu tiên BFS gặp chính là node lá gần root nhất.
4. Khi gặp node lá, trả về độ sâu ngay.
5. Phải đặc biệt chú ý node chỉ có một con.
6. Không được xem `nullptr` là một node lá.
7. Cây rỗng có minimum depth bằng `0`.

Lời giải sử dụng BFS có:

```text
Time Complexity: O(n)
Space Complexity: O(n)
```

và là một cách tiếp cận trực tiếp, rõ ràng cho bài toán này.
