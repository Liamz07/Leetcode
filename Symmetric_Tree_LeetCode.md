# Symmetric Tree - LeetCode

## 1. Bản chất bài toán

Cây nhị phân đối xứng nếu cây con bên trái và cây con bên phải là **ảnh phản chiếu** của nhau qua trục dọc ở giữa.

Ví dụ:

```text
        1
       / \
      2   2
     / \ / \
    3  4 4  3
```

Điểm quan trọng nhất: đây không phải là kiểm tra hai cây con giống hệt nhau. Ta phải đảo hướng khi so sánh:

```text
trái của A  <-> phải của B
phải của A  <-> trái của B
```

---

## 2. Ý tưởng tối ưu

Ta xây dựng hàm `isMirror(a, b)` để trả lời:

> Hai cây bắt đầu từ `a` và `b` có đối xứng với nhau không?

Khi đó bài toán ban đầu chỉ còn:

```cpp
isMirror(root->left, root->right)
```

### Quy tắc của `isMirror(a, b)`

**Trường hợp 1: cả hai đều NULL**

Hai phía đều hết node nên chúng đối xứng:

```cpp
return true;
```

**Trường hợp 2: chỉ một bên NULL**

Một phía có node, phía kia không có:

```cpp
return false;
```

**Trường hợp 3: giá trị khác nhau**

Hai node đối xứng phải có cùng giá trị:

```cpp
if (a->val != b->val)
    return false;
```

**Trường hợp 4: cùng giá trị**

Tiếp tục kiểm tra theo hướng phản chiếu:

```text
a->left  <-> b->right
a->right <-> b->left
```

Cả hai cặp đều phải đúng.

---

## 3. Vì sao phải đổi trái và phải?

Giả sử:

```text
        1
       / \
      A   B
     / \ / \
    L  R R' L'
```

Nếu cây đối xứng thì `L` phải tương ứng với `L'`, nhưng `L'` nằm ở **nhánh phải** của `B`.

Vì vậy:

```text
A.left  <-> B.right
A.right <-> B.left
```

Không được viết:

```text
A.left  <-> B.left
A.right <-> B.right
```

Cách thứ hai là kiểm tra hai cây giống nhau theo cùng hướng, không phải kiểm tra ảnh phản chiếu.

---

## 4. Ví dụ mô phỏng

Với:

```text
        1
       / \
      2   2
     / \ / \
    3  4 4  3
```

Ta bắt đầu:

```text
isMirror(2, 2)
```

Hai giá trị đều bằng `2`.

Sau đó kiểm tra:

```text
2 bên trái -> left = 3
2 bên phải -> right = 3
```

nên:

```text
isMirror(3, 3) -> true
```

Tiếp tục:

```text
isMirror(4, 4) -> true
```

Cả hai đều đúng nên cây đối xứng.

---

## 5. Ví dụ không đối xứng

```text
        1
       / \
      2   2
       \   \
        3   3
```

Ta có:

```text
isMirror(2, 2)
```

Khi so sánh cặp đối xứng:

```text
A.left  = NULL
B.right = 3
```

Một bên NULL, một bên có node nên trả về `false`.

---

## 6. Vì sao dùng đệ quy?

Cây nhị phân có cấu trúc đệ quy: mỗi node lại có hai cây con.

Bài toán lớn:

```text
Hai cây có đối xứng không?
```

được chia thành hai bài toán nhỏ:

```text
cây trái của A  và cây phải của B
cây phải của A  và cây trái của B
```

Do đó đệ quy rất tự nhiên.

---

## 7. Độ phức tạp

Giả sử cây có `n` node.

Mỗi node chỉ được xét tối đa một lần.

Vì vậy:

```text
Thời gian: O(n)
```

Đây là tối ưu vì trong trường hợp cần thiết phải kiểm tra toàn bộ cây.

Với đệ quy, bộ nhớ phụ thuộc vào chiều cao `h` của cây:

```text
Bộ nhớ: O(h)
```

- Cây cân bằng: `h = O(log n)`.
- Cây lệch hoàn toàn: `h = O(n)`.

---

## 8. Lời giải C++ tối ưu

```cpp
class Solution {
public:
    bool isMirror(TreeNode* a, TreeNode* b) {
        if (a == nullptr && b == nullptr)
            return true;

        if (a == nullptr || b == nullptr)
            return false;

        if (a->val != b->val)
            return false;

        return isMirror(a->left, b->right) &&
               isMirror(a->right, b->left);
    }

    bool isSymmetric(TreeNode* root) {
        if (root == nullptr)
            return true;

        return isMirror(root->left, root->right);
    }
};
```

---

## 9. Giải thích code

### `isSymmetric`

```cpp
return isMirror(root->left, root->right);
```

Root nằm trên trục đối xứng nên chỉ cần kiểm tra hai cây con hai bên.

### Kiểm tra NULL

```cpp
if (a == nullptr && b == nullptr)
    return true;
```

Cả hai đều không còn node nên hai phía khớp nhau.

```cpp
if (a == nullptr || b == nullptr)
    return false;
```

Chỉ một bên có node thì không thể đối xứng.

### Kiểm tra giá trị

```cpp
if (a->val != b->val)
    return false;
```

Hai node ở vị trí đối xứng phải có cùng giá trị.

### Bước quan trọng nhất

```cpp
return isMirror(a->left, b->right) &&
       isMirror(a->right, b->left);
```

Đây chính là toàn bộ quy luật phản chiếu:

```text
left  <-> right
right <-> left
```

`&&` đảm bảo cả hai cặp đều phải đối xứng.

---

## 10. So sánh với bài Same Tree

Đây là cách rất dễ nhớ sự khác nhau.

### Same Tree

Hai cây phải giống nhau theo cùng hướng:

```text
A.left  <-> B.left
A.right <-> B.right
```

### Symmetric Tree

Hai cây phải phản chiếu nhau:

```text
A.left  <-> B.right
A.right <-> B.left
```

Chỉ cần nhớ sự khác nhau này là có thể nhận ra hướng đệ quy của bài.

---

## 11. Những lỗi thường gặp

### Lỗi 1: So sánh cùng hướng

Sai:

```cpp
isMirror(a->left, b->left);
isMirror(a->right, b->right);
```

Đúng:

```cpp
isMirror(a->left, b->right);
isMirror(a->right, b->left);
```

### Lỗi 2: Chỉ so sánh giá trị

Hai cây có thể có cùng các giá trị nhưng cấu trúc khác nhau. Vì vậy phải kiểm tra cả vị trí trái/phải.

### Lỗi 3: Quên NULL

Không được truy cập `a->val` hoặc `b->val` trước khi chắc chắn node tồn tại.

### Lỗi 4: Chỉ kiểm tra một cặp con

Cả hai cặp đều phải được kiểm tra:

```text
a.left  <-> b.right
a.right <-> b.left
```

---

## 12. Vì sao không cần chuyển cây thành mảng?

Không nên chỉ lấy toàn bộ giá trị rồi so sánh vì cấu trúc cây rất quan trọng.

Ví dụ:

```text
    1              1
   / \            / \
  2   3          3   2
```

Ta cần biết `2` và `3` nằm ở vị trí nào. Hàm `isMirror` kiểm tra trực tiếp cả giá trị lẫn cấu trúc nên không cần tạo mảng phụ.

---

## 13. Có thể dùng BFS không?

Có. Ta có thể đưa hai node đối xứng vào queue cùng lúc.

Mỗi lần lấy `a` và `b`, kiểm tra chúng rồi đưa vào queue theo thứ tự:

```text
a.left  và b.right
a.right và b.left
```

BFS cũng có:

```text
Thời gian: O(n)
Bộ nhớ: O(n)
```

Nhưng đệ quy trực tiếp thể hiện bản chất ảnh phản chiếu và có code ngắn, dễ hiểu hơn.

---

## 14. Cách nhớ khi gặp lại bài

Chỉ cần nhớ câu:

> Hai phía đối xứng thì trái của bên này phải so với phải của bên kia, và phải của bên này phải so với trái của bên kia.

Sơ đồ:

```text
             root
            /    \
           A      B
          / \    / \
         L   R  L   R
```

Kiểm tra:

```text
A.left  <-> B.right
A.right <-> B.left
```

Tóm lại:

```text
Same Tree:
left  <-> left
right <-> right

Symmetric Tree:
left  <-> right
right <-> left
```

---

## 15. Kết luận

Bản chất của Symmetric Tree là kiểm tra **hai cây con có phải ảnh phản chiếu của nhau hay không**.

Thuật toán tối ưu:

```text
1. Lấy root->left và root->right.
2. So sánh hai node.
3. Nếu cả hai NULL -> true.
4. Nếu chỉ một NULL -> false.
5. Nếu giá trị khác -> false.
6. So sánh left của node thứ nhất với right của node thứ hai.
7. So sánh right của node thứ nhất với left của node thứ hai.
8. Cả hai đúng -> true.
```

Độ phức tạp:

```text
Thời gian: O(n)
Bộ nhớ: O(h)
```

Trong đó `n` là số node và `h` là chiều cao cây.

Điểm quan trọng nhất cần ghi nhớ:

```text
Symmetric = phản chiếu

left  <-> right
right <-> left
```
