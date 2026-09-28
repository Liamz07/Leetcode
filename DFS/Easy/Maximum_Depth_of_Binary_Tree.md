# Maximum Depth of Binary Tree

[LeetCode - Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

## 1. Mô tả bài toán

Cho một cây nhị phân `root`, hãy trả về **độ sâu lớn nhất** của cây.

Độ sâu của cây được tính bằng số lượng node trên đường đi dài nhất từ node gốc `root` xuống một node lá.

Ví dụ:

```text
        3
       / \
      9  20
         / \
        15   7
```

Đường đi dài nhất có thể là:

```text
3 -> 20 -> 15
```

hoặc:

```text
3 -> 20 -> 7
```

Có 3 node trên đường đi, vì vậy:

```text
Maximum Depth = 3
```

Nếu cây rỗng:

```text
root = null
```

thì độ sâu bằng:

```text
0
```

---

## 2. Ý tưởng quan trọng nhất

Với mỗi node, độ sâu lớn nhất của cây con bắt đầu tại node đó phụ thuộc vào hai cây con bên trái và bên phải.

Giả sử node hiện tại là `root`.

Ta có:

```text
Depth(root)
    = 1 + max(Depth(root->left), Depth(root->right))
```

Tại sao lại có `+ 1`?

Bởi vì độ sâu của cây không chỉ bao gồm các node bên dưới mà còn phải tính chính node hiện tại.

Ví dụ:

```text
        3
       / \
      9  20
         / \
        15   7
```

Xét node `20`:

```text
        20
       /  \
      15   7
```

Cả hai node `15` và `7` đều là node lá nên:

```text
Depth(15) = 1
Depth(7)  = 1
```

Do đó:

```text
Depth(20)
= 1 + max(Depth(15), Depth(7))
= 1 + max(1, 1)
= 2
```

Sau đó xét node `3`:

```text
Depth(9)  = 1
Depth(20) = 2

Depth(3)
= 1 + max(1, 2)
= 3
```

Kết quả là `3`.

---

## 3. Phương pháp tối ưu: DFS đệ quy

Đây là cách tiếp cận tự nhiên và ngắn gọn nhất cho bài toán này.

Ta định nghĩa hàm:

```text
maxDepth(root)
```

có nhiệm vụ trả về độ sâu lớn nhất của cây con có gốc là `root`.

### Trường hợp 1: Cây rỗng

Nếu:

```text
root == nullptr
```

thì không có node nào.

Vì vậy:

```text
maxDepth(root) = 0
```

### Trường hợp 2: Node hiện tại không rỗng

Nếu `root` tồn tại, ta cần tính:

```text
maxDepth(root->left)
```

và:

```text
maxDepth(root->right)
```

Sau đó chọn cây con có độ sâu lớn hơn:

```text
max(maxDepth(root->left), maxDepth(root->right))
```

Cuối cùng cộng thêm `1` để tính node hiện tại:

```text
1 + max(
    maxDepth(root->left),
    maxDepth(root->right)
)
```

---

## 4. Công thức truy hồi

Ta có công thức:

```text
maxDepth(root) = 0
```

nếu:

```text
root == nullptr
```

Ngược lại:

```text
maxDepth(root)
    = 1 + max(
        maxDepth(root->left),
        maxDepth(root->right)
      )
```

Đây chính là cấu trúc đệ quy của bài toán.

Ta có thể hiểu công thức này theo cách đơn giản:

```text
Độ sâu cây hiện tại
=
1 node hiện tại
+
độ sâu lớn hơn của 2 cây con
```

---

## 5. Ví dụ quá trình đệ quy

Xét cây:

```text
        3
       / \
      9  20
         / \
        15   7
```

Ta bắt đầu:

```text
maxDepth(3)
```

Node `3` có hai cây con nên cần tính:

```text
maxDepth(9)
maxDepth(20)
```

### Xét node 9

Node `9` không có con:

```text
    9
   / \
null null
```

Do đó:

```text
maxDepth(9)
= 1 + max(0, 0)
= 1
```

### Xét node 20

Ta tiếp tục:

```text
maxDepth(20)
```

Cần tính:

```text
maxDepth(15)
maxDepth(7)
```

Cả `15` và `7` đều là node lá:

```text
maxDepth(15) = 1
maxDepth(7)  = 1
```

Vì vậy:

```text
maxDepth(20)
= 1 + max(1, 1)
= 2
```

### Quay lại node 3

Ta đã có:

```text
maxDepth(9)  = 1
maxDepth(20) = 2
```

Do đó:

```text
maxDepth(3)
= 1 + max(1, 2)
= 3
```

Kết quả:

```text
3
```

---

## 6. Hình dung bằng cách "đi xuống rồi quay lên"

Đệ quy của bài này có thể hình dung như sau:

```text
                    3
                  /   \
                 9     20
                      /  \
                     15   7
```

Hàm phải đi xuống đến các node lá trước.

Ví dụ với nhánh:

```text
3 -> 20 -> 15
```

Khi đến `15`:

```text
maxDepth(15) = 1
```

Kết quả được trả ngược lên `20`:

```text
maxDepth(20) = 1 + max(1, 1)
             = 2
```

Sau đó kết quả tiếp tục được trả lên `3`:

```text
maxDepth(3) = 1 + max(1, 2)
            = 3
```

Đây là đặc điểm rất quan trọng của các bài toán cây sử dụng DFS đệ quy:

```text
Đi xuống -> giải bài toán con -> trả kết quả lên
```

---

## 7. Code C++

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }

        int leftDepth = maxDepth(root->left);
        int rightDepth = maxDepth(root->right);

        return 1 + max(leftDepth, rightDepth);
    }
};
```

---

## 8. Giải thích từng phần của code

### Kiểm tra cây rỗng

```cpp
if (root == nullptr) {
    return 0;
}
```

Nếu node hiện tại không tồn tại thì độ sâu của cây con tại node đó là `0`.

Đây cũng chính là **điểm dừng của đệ quy**.

Nếu không có điều kiện này, hàm sẽ tiếp tục truy cập:

```cpp
root->left
root->right
```

trên một con trỏ `nullptr`, dẫn đến lỗi.

---

### Tính độ sâu cây con trái

```cpp
int leftDepth = maxDepth(root->left);
```

Ta gọi lại chính hàm `maxDepth` cho cây con bên trái.

Kết quả trả về là độ sâu lớn nhất của cây con trái.

---

### Tính độ sâu cây con phải

```cpp
int rightDepth = maxDepth(root->right);
```

Tương tự, ta tính độ sâu lớn nhất của cây con phải.

---

### Chọn cây con sâu hơn

```cpp
max(leftDepth, rightDepth)
```

Đường đi dài nhất từ node hiện tại chắc chắn phải đi qua một trong hai hướng:

```text
left
```

hoặc:

```text
right
```

Do đó chỉ cần chọn độ sâu lớn hơn.

---

### Cộng thêm node hiện tại

```cpp
return 1 + max(leftDepth, rightDepth);
```

`1` đại diện cho chính node hiện tại.

Ví dụ:

```text
    20
   /  \
  15   7
```

Ta có:

```text
leftDepth = 1
rightDepth = 1
```

nên:

```text
return 1 + max(1, 1);
```

kết quả:

```text
2
```

---

# 9. Tại sao thuật toán đúng?

Ta chứng minh dựa trên cấu trúc của cây nhị phân.

## Trường hợp cơ sở

Nếu:

```text
root == nullptr
```

cây không có node nào.

Theo định nghĩa, độ sâu của cây rỗng là `0`.

Thuật toán trả về:

```text
0
```

Vì vậy trường hợp cơ sở là đúng.

## Trường hợp node hiện tại tồn tại

Giả sử `root` không rỗng.

Một đường đi dài nhất bắt đầu tại `root` sẽ phải đi xuống:

```text
cây con trái
```

hoặc:

```text
cây con phải
```

Không thể đồng thời đi vào cả hai cây con trên cùng một đường đi.

Do đó độ sâu lớn nhất của cây tại `root` phải bằng:

```text
1 + độ sâu lớn hơn giữa hai cây con
```

Thuật toán thực hiện chính xác:

```text
1 + max(leftDepth, rightDepth)
```

Vì vậy kết quả trả về chính là độ sâu lớn nhất của cây.

---

# 10. Độ phức tạp

Giả sử cây có `n` node.

## Time Complexity

Mỗi node được truy cập đúng một lần.

Vì vậy:

```text
O(n)
```

Trong đó `n` là số lượng node trong cây.

Không thể làm tốt hơn về mặt tiệm cận trong trường hợp tổng quát, bởi vì để biết chính xác độ sâu lớn nhất, ta cần xét cấu trúc của các node trong cây.

## Space Complexity

Thuật toán sử dụng đệ quy.

Bộ nhớ phụ thuộc vào chiều cao `h` của cây:

```text
O(h)
```

Trong đó `h` là chiều cao của cây.

### Trường hợp cây cân bằng

Ví dụ:

```text
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

Chiều cao xấp xỉ:

```text
h = log(n)
```

Do đó stack đệ quy sử dụng:

```text
O(log n)
```

### Trường hợp cây lệch hoàn toàn

Ví dụ:

```text
1
 \
  2
   \
    3
     \
      4
       \
        5
```

Khi đó:

```text
h = n
```

nên stack đệ quy có thể sử dụng:

```text
O(n)
```

---

# 11. Cách tiếp cận khác: BFS

Ngoài DFS đệ quy, bài toán còn có thể giải bằng **Breadth-First Search (BFS)**.

Ý tưởng:

Thay vì đi sâu xuống một nhánh, ta duyệt cây theo từng tầng.

Ví dụ:

```text
        3              Level 1
       / \
      9  20            Level 2
         / \
        15   7         Level 3
```

Ta có:

```text
Level 1: 3
Level 2: 9, 20
Level 3: 15, 7
```

Có tổng cộng 3 tầng.

Vì vậy:

```text
Maximum Depth = số lượng level
```

Ta có thể sử dụng `queue` để thực hiện BFS.

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }

        queue<TreeNode*> q;
        q.push(root);

        int depth = 0;

        while (!q.empty()) {
            int size = q.size();
            depth++;

            for (int i = 0; i < size; i++) {
                TreeNode* current = q.front();
                q.pop();

                if (current->left != nullptr) {
                    q.push(current->left);
                }

                if (current->right != nullptr) {
                    q.push(current->right);
                }
            }
        }

        return depth;
    }
};
```

---

# 12. So sánh DFS và BFS

| Tiêu chí | DFS đệ quy | BFS |
|---|---|---|
| Ý tưởng | Tính độ sâu từ dưới lên | Đếm số tầng |
| Cấu trúc | Recursion Stack | Queue |
| Time | O(n) | O(n) |
| Space | O(h) | O(w) |
| Code | Ngắn hơn | Dài hơn |
| Phù hợp bài này | Rất tự nhiên | Cũng phù hợp |

Trong đó:

- `h` là chiều cao của cây.
- `w` là số node lớn nhất xuất hiện trên một level.

Với bài toán **Maximum Depth of Binary Tree**, DFS đệ quy thường là cách biểu diễn trực tiếp nhất vì công thức truy hồi của bài toán rất rõ ràng.

---

# 13. Những trường hợp cần chú ý

## Trường hợp 1: Cây rỗng

```text
root = null
```

Kết quả:

```text
0
```

Code xử lý bởi:

```cpp
if (root == nullptr) {
    return 0;
}
```

---

## Trường hợp 2: Chỉ có một node

```text
    1
```

Kết quả:

```text
1
```

Bởi vì có đúng một node trên đường đi dài nhất.

---

## Trường hợp 3: Chỉ có cây con trái

```text
      1
     /
    2
   /
  3
```

Kết quả:

```text
3
```

---

## Trường hợp 4: Chỉ có cây con phải

```text
1
 \
  2
   \
    3
```

Kết quả:

```text
3
```

---

## Trường hợp 5: Hai nhánh có độ sâu khác nhau

```text
        1
       / \
      2   3
     /
    4
```

Ta có:

```text
Depth(2) = 2
Depth(3) = 1
```

Do đó:

```text
Depth(1)
= 1 + max(2, 1)
= 3
```

Điểm quan trọng là không phải cứ có hai cây con thì cộng cả hai độ sâu. Ta chỉ lấy nhánh sâu hơn vì một đường đi chỉ có thể đi theo một nhánh.

---

# 14. Lỗi thường gặp

## Lỗi 1: Quên trường hợp `nullptr`

Sai:

```cpp
int maxDepth(TreeNode* root) {
    return 1 + max(maxDepth(root->left),
                   maxDepth(root->right));
}
```

Nếu `root == nullptr`, chương trình sẽ cố truy cập:

```cpp
root->left
```

và gây lỗi.

Phải có:

```cpp
if (root == nullptr) {
    return 0;
}
```

---

## Lỗi 2: Quên cộng `1`

Ví dụ:

```cpp
return max(leftDepth, rightDepth);
```

Cách này không tính node hiện tại.

Đúng phải là:

```cpp
return 1 + max(leftDepth, rightDepth);
```

---

## Lỗi 3: Cộng cả hai cây con

Sai:

```cpp
return 1 + leftDepth + rightDepth;
```

Đây không phải là độ sâu của cây.

`leftDepth + rightDepth` đang mô tả tổng độ sâu của hai nhánh, trong khi ta cần **một đường đi dài nhất**.

Đúng:

```cpp
return 1 + max(leftDepth, rightDepth);
```

---

## Lỗi 4: Nhầm số node với số cạnh

Bài này định nghĩa depth bằng **số lượng node** trên đường đi.

Ví dụ:

```text
1
 \
  2
   \
    3
```

Có:

```text
3 node
```

nhưng chỉ có:

```text
2 cạnh
```

Vì vậy đáp án là:

```text
3
```

chứ không phải `2`.

---

# 15. Mẫu tư duy có thể áp dụng cho nhiều bài Binary Tree

Bài toán này rất quan trọng vì nó giới thiệu một mẫu tư duy phổ biến khi giải các bài toán cây.

Khi gặp một bài toán yêu cầu tính toán trên cây, hãy thử đặt câu hỏi:

```text
"Đáp án tại node hiện tại có thể được xây dựng
từ đáp án của hai cây con hay không?"
```

Nếu có, ta thường có thể sử dụng DFS đệ quy.

Với bài này:

```text
Kết quả tại node
=
kết quả cây con trái
+
kết quả cây con phải
+
xử lý node hiện tại
```

Cụ thể:

```text
Depth(root)
=
1 + max(
    Depth(root->left),
    Depth(root->right)
)
```

Đây là một dạng **Tree DP / Divide and Conquer trên cây** rất cơ bản.

---

# 16. Tóm tắt phương pháp tối ưu

Các bước cần nhớ:

```text
Bước 1:
Nếu root == nullptr
=> trả về 0

Bước 2:
Tính độ sâu cây con trái

Bước 3:
Tính độ sâu cây con phải

Bước 4:
Chọn độ sâu lớn hơn

Bước 5:
Cộng 1 cho node hiện tại
```

Công thức:

```text
maxDepth(root)
=
1 + max(
    maxDepth(root->left),
    maxDepth(root->right)
)
```

Code:

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }

        int leftDepth = maxDepth(root->left);
        int rightDepth = maxDepth(root->right);

        return 1 + max(leftDepth, rightDepth);
    }
};
```

Độ phức tạp:

```text
Time:  O(n)
Space: O(h)
```

Trong đó:

- `n` là số node.
- `h` là chiều cao của cây.

---

# 17. Kết luận

**Maximum Depth of Binary Tree** là một bài toán Binary Tree cơ bản nhưng rất quan trọng.

Điểm cốt lõi không nằm ở việc viết code dài mà nằm ở việc nhận ra rằng:

```text
Độ sâu của một node
=
1 + độ sâu lớn nhất của hai cây con
```

Từ đó ta xây dựng được lời giải DFS đệ quy rất ngắn gọn:

```cpp
if (root == nullptr) {
    return 0;
}

return 1 + max(
    maxDepth(root->left),
    maxDepth(root->right)
);
```

Đây là một trong những pattern quan trọng nhất cần ghi nhớ khi học Binary Tree:

```text
Base Case
    ↓
Solve Left Subtree
    ↓
Solve Right Subtree
    ↓
Combine Results
```

Pattern này sẽ xuất hiện trong rất nhiều bài toán cây khác, chẳng hạn như tính tổng node, kiểm tra cây cân bằng, đường kính cây, tìm giá trị lớn nhất, hoặc thực hiện các dạng Tree DP phức tạp hơn.
