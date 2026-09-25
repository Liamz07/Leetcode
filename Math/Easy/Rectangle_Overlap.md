# Rectangle Overlap

## 1. Đề bài

Cho hai hình chữ nhật có các cạnh song song với trục tọa độ.

Mỗi hình chữ nhật được biểu diễn bởi một mảng:

```text
[ x1, y1, x2, y2 ]
```

Trong đó:

- `(x1, y1)` là tọa độ góc dưới bên trái.
- `(x2, y2)` là tọa độ góc trên bên phải.

Cần xác định xem hai hình chữ nhật có **phần diện tích chung lớn hơn 0** hay không.

Nếu có thì trả về `true`, ngược lại trả về `false`.

### Ví dụ

```text
rec1 = [0, 0, 2, 2]
rec2 = [1, 1, 3, 3]
```

Hai hình chữ nhật chồng lên nhau tại một vùng có diện tích:

```text
1 × 1 = 1
```

nên kết quả là:

```text
true
```

---

# 2. Hiểu chính xác "overlap"

Điểm rất quan trọng của bài này là:

> Hai hình chữ nhật phải có **diện tích phần giao lớn hơn 0**.

Điều đó có nghĩa là chỉ chạm nhau tại:

- một điểm;
- hoặc một cạnh;

thì **không được tính là overlap**.

Ví dụ:

```text
rec1 = [0, 0, 2, 2]
rec2 = [2, 0, 4, 2]
```

Hai hình chữ nhật có chung cạnh `x = 2`.

Nhưng phần diện tích chung bằng:

```text
0
```

nên kết quả là:

```text
false
```

Tương tự, nếu chỉ chạm nhau tại một điểm thì cũng phải trả về `false`.

---

# 3. Cách nhìn quan trọng: tách bài toán 2D thành 2 bài toán 1D

Đây là ý tưởng quan trọng nhất của bài.

Một hình chữ nhật có hai khoảng trên trục `x` và `y`.

Với:

```text
[x1, y1, x2, y2]
```

ta có:

- Khoảng theo trục `x`: `[x1, x2]`
- Khoảng theo trục `y`: `[y1, y2]`

Hai hình chữ nhật có phần diện tích chung lớn hơn `0` khi và chỉ khi:

1. Hai khoảng trên trục `x` có độ dài giao nhau lớn hơn `0`.
2. Hai khoảng trên trục `y` có độ dài giao nhau lớn hơn `0`.

Nói ngắn gọn:

```text
Overlap 2D
    ↓
Overlap theo x
    +
Overlap theo y
```

Đây là cách biến một bài hình học thành một bài xử lý khoảng rất đơn giản.

---

# 4. Khi nào hai khoảng có phần giao có độ dài > 0?

Xét hai khoảng:

```text
[a, b]
[c, d]
```

Độ dài phần giao là:

\[
\min(b,d)-\max(a,c)
\]

Nếu:

\[
\min(b,d)-\max(a,c)>0
\]

thì hai khoảng có phần giao có độ dài dương.

Tương đương:

\[
\min(b,d)>\max(a,c)
\]

---

# 5. Áp dụng vào hình chữ nhật

Với:

```text
rec1 = [x1, y1, x2, y2]
rec2 = [x3, y3, x4, y4]
```

Độ rộng phần giao là:

\[
\min(x2,x4)-\max(x1,x3)
\]

Độ cao phần giao là:

\[
\min(y2,y4)-\max(y1,y3)
\]

Hai hình chữ nhật overlap khi:

\[
\min(x2,x4)>\max(x1,x3)
\]

**và**

\[
\min(y2,y4)>\max(y1,y3)
\]

---

# 6. Vì sao phải dùng `>` chứ không phải `>=`?

Đây là một điểm rất dễ sai.

Giả sử:

```text
rec1 = [0, 0, 2, 2]
rec2 = [2, 0, 4, 2]
```

Theo trục `x`:

```text
rec1: [0, 2]
rec2: [2, 4]
```

Phần giao chỉ là:

```text
x = 2
```

Độ dài giao:

\[
2-2=0
\]

Nếu dùng:

```cpp
min(x2, x4) >= max(x1, x3)
```

thì:

```text
2 >= 2
```

là `true`.

Nhưng hai hình chữ nhật chỉ chạm nhau tại cạnh, không có diện tích chung.

Vì vậy phải dùng:

```cpp
min(x2, x4) > max(x1, x3)
```

để đảm bảo phần giao có **độ dài dương**.

Tương tự đối với trục `y`.

---

# 7. Cách 1: Kiểm tra trực tiếp phần giao

Đây là cách tiếp cận rất tự nhiên.

Ta tính:

```text
width = min(x2, x4) - max(x1, x3)
height = min(y2, y4) - max(y1, y3)
```

Sau đó:

```text
width > 0
và
height > 0
```

thì hai hình chữ nhật overlap.

## Code

```cpp
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        int rong = min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]);
        int cao = min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]);

        return rong > 0 && cao > 0;
    }
};
```

---

# 8. Phân tích từng dòng code

## Tính chiều rộng phần giao

```cpp
int rong = min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]);
```

Theo trục `x`:

- `rec1[0]`: cạnh trái của hình 1.
- `rec1[2]`: cạnh phải của hình 1.
- `rec2[0]`: cạnh trái của hình 2.
- `rec2[2]`: cạnh phải của hình 2.

Cạnh trái của phần giao phải là cạnh trái lớn hơn:

```cpp
max(rec1[0], rec2[0])
```

Cạnh phải của phần giao phải là cạnh phải nhỏ hơn:

```cpp
min(rec1[2], rec2[2])
```

Do đó:

\[
width = \text{right} - \text{left}
\]

hay:

```cpp
min(rec1[2], rec2[2]) - max(rec1[0], rec2[0])
```

---

## Tính chiều cao phần giao

Tương tự:

```cpp
int cao = min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]);
```

Cạnh dưới của phần giao:

```cpp
max(rec1[1], rec2[1])
```

Cạnh trên của phần giao:

```cpp
min(rec1[3], rec2[3])
```

Do đó:

\[
height = \text{top} - \text{bottom}
\]

---

## Kiểm tra overlap

```cpp
return rong > 0 && cao > 0;
```

Nếu:

```text
rong > 0
```

thì có giao nhau theo chiều ngang.

Nếu:

```text
cao > 0
```

thì có giao nhau theo chiều dọc.

Phải đồng thời thỏa mãn cả hai:

```text
rong > 0
    &&
cao > 0
```

thì phần giao mới có diện tích:

\[
S = rong \times cao > 0
\]

---

# 9. Mô phỏng testcase

## Test 1

```text
rec1 = [0, 0, 2, 2]
rec2 = [1, 1, 3, 3]
```

### Theo trục `x`

```text
min(2, 3) - max(0, 1)
= 2 - 1
= 1
```

### Theo trục `y`

```text
min(2, 3) - max(0, 1)
= 2 - 1
= 1
```

Vậy:

```text
rong = 1
cao = 1
```

Cả hai đều lớn hơn `0`.

Kết quả:

```text
true
```

---

# 10. Testcase chỉ chạm nhau tại cạnh

```text
rec1 = [0, 0, 2, 2]
rec2 = [2, 0, 4, 2]
```

Theo trục `x`:

```text
min(2, 4) - max(0, 2)
= 2 - 2
= 0
```

Theo trục `y`:

```text
min(2, 2) - max(0, 0)
= 2
```

Ta có:

```text
rong = 0
cao = 2
```

Điều kiện:

```cpp
rong > 0 && cao > 0
```

trở thành:

```text
false && true
```

→ `false`.

Điều này chính xác vì hai hình chỉ chung một cạnh.

---

# 11. Testcase chỉ chạm nhau tại một điểm

```text
rec1 = [0, 0, 2, 2]
rec2 = [2, 2, 4, 4]
```

Theo trục `x`:

```text
min(2, 4) - max(0, 2)
= 0
```

Theo trục `y`:

```text
min(2, 4) - max(0, 2)
= 0
```

Vậy:

```text
rong = 0
cao = 0
```

Kết quả:

```text
false
```

Đúng vì chỉ có một điểm chung.

---

# 12. Testcase không giao nhau

```text
rec1 = [0, 0, 1, 1]
rec2 = [2, 2, 3, 3]
```

Theo trục `x`:

```text
min(1, 3) - max(0, 2)
= 1 - 2
= -1
```

Theo trục `y`:

```text
min(1, 3) - max(0, 2)
= -1
```

Ở đây chiều rộng giao bị âm.

Điều này có nghĩa là hai khoảng hoàn toàn tách nhau.

Vì:

```text
rong > 0
```

là sai nên kết quả là:

```text
false
```

---

# 13. Testcase một hình nằm hoàn toàn trong hình còn lại

```text
rec1 = [0, 0, 5, 5]
rec2 = [1, 1, 2, 2]
```

Theo trục `x`:

```text
min(5, 2) - max(0, 1)
= 2 - 1
= 1
```

Theo trục `y`:

```text
min(5, 2) - max(0, 1)
= 1
```

Cả hai đều dương.

Kết quả:

```text
true
```

Điều này cũng hợp lý vì hình nhỏ nằm hoàn toàn bên trong hình lớn.

---

# 14. Cách nhìn ngược: Khi nào hai hình chữ nhật KHÔNG overlap?

Thay vì tìm điều kiện overlap, ta có thể tìm các trường hợp **không thể overlap**.

Hai hình chữ nhật không có diện tích giao nếu xảy ra ít nhất một trong các trường hợp:

### Hình 1 nằm hoàn toàn bên trái hình 2

```text
rec1[2] <= rec2[0]
```

### Hình 2 nằm hoàn toàn bên trái hình 1

```text
rec2[2] <= rec1[0]
```

### Hình 1 nằm hoàn toàn bên dưới hình 2

```text
rec1[3] <= rec2[1]
```

### Hình 2 nằm hoàn toàn bên dưới hình 1

```text
rec2[3] <= rec1[1]
```

Nếu bất kỳ điều kiện nào đúng:

```text
false
```

Ngược lại:

```text
true
```

Code:

```cpp
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        if (rec1[2] <= rec2[0]) return false;
        if (rec2[2] <= rec1[0]) return false;
        if (rec1[3] <= rec2[1]) return false;
        if (rec2[3] <= rec1[1]) return false;

        return true;
    }
};
```

Cách này cũng đúng và có độ phức tạp `O(1)`.

---

# 15. So sánh hai cách tiếp cận

| Cách | Ý tưởng | Time | Space |
|---|---|---:|---:|
| Tính phần giao | Tính `rong` và `cao` của phần giao | `O(1)` | `O(1)` |
| Kiểm tra không giao | Tìm các trường hợp hai hình nằm tách nhau | `O(1)` | `O(1)` |

Cả hai đều tối ưu về mặt độ phức tạp.

Tuy nhiên, cách **tính phần giao** có một ưu điểm lớn về mặt tư duy:

```text
Phần giao có diện tích > 0
⇔
chiều rộng giao > 0
và
chiều cao giao > 0
```

Nó bám trực tiếp vào định nghĩa của bài toán.

Vì vậy, đây là cách nên ưu tiên để hiểu và triển khai bài này.

---

# 16. Vì sao không cần tính diện tích trực tiếp?

Có thể viết:

```cpp
int rong = min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]);
int cao = min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]);

return rong * cao > 0;
```

Nhưng cách này không cần thiết.

Chỉ cần:

```cpp
return rong > 0 && cao > 0;
```

là đủ.

Hơn nữa, xét về mặt tư duy, điều kiện:

```text
rong > 0 && cao > 0
```

thể hiện rất rõ:

- Có giao theo chiều ngang.
- Có giao theo chiều dọc.

Hai điều kiện cùng đúng thì mới có vùng diện tích chung.

---

# 17. Tại sao không dùng công thức diện tích giao ngay từ đầu?

Nếu ta tính:

\[
S = width \times height
\]

thì trong một số trường hợp không giao nhau, `width` hoặc `height` có thể âm.

Ví dụ:

```text
width = -2
height = -3
```

Nếu nhân trực tiếp:

\[
(-2)\times(-3)=6
\]

ta lại nhận được một diện tích dương, mặc dù hai hình hoàn toàn không giao nhau.

Đây là lý do nếu tính diện tích thì phải xử lý:

```text
max(0, width)
max(0, height)
```

Ví dụ:

```cpp
int rong = max(0, min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]));
int cao = max(0, min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]));

return rong > 0 && cao > 0;
```

Nhưng thực tế ta không cần làm như vậy.

Chỉ cần kiểm tra:

```cpp
rong > 0 && cao > 0
```

là đơn giản hơn.

---

# 18. Độ phức tạp

Ta chỉ thực hiện một số phép:

- `min`
- `max`
- phép trừ
- phép so sánh

Không có vòng lặp.

Do đó:

### Time Complexity

\[
\boxed{O(1)}
\]

### Space Complexity

\[
\boxed{O(1)}
\]

Đây là độ phức tạp tối ưu cho bài toán vì số lượng dữ liệu đầu vào luôn cố định.

---

# 19. Lời giải C++ hoàn chỉnh

```cpp
class Solution {
public:
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        int rong = min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]);
        int cao = min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]);

        return rong > 0 && cao > 0;
    }
};
```

---

# 20. Luồng tư duy ngắn gọn

Khi gặp bài này, có thể suy nghĩ theo đúng chuỗi sau:

```text
Hai hình chữ nhật có diện tích giao nhau?
                ↓
Phải giao nhau theo cả trục X và trục Y
                ↓
Tính độ dài phần giao trên X
                ↓
min(x2, x4) - max(x1, x3)
                ↓
Phải > 0
                ↓
Tính độ dài phần giao trên Y
                ↓
min(y2, y4) - max(y1, y3)
                ↓
Phải > 0
                ↓
Cả hai cùng > 0
                ↓
true
```

Công thức quan trọng nhất cần nhớ:

\[
\boxed{
\text{độ dài giao}
=
\min(\text{hai đầu phải})
-
\max(\text{hai đầu trái})
}
\]

Sau đó áp dụng công thức này độc lập cho `x` và `y`.

---

# 21. Kết luận

Bản chất của bài `Rectangle Overlap` không cần đến thuật toán hình học phức tạp.

Ta chỉ cần nhận ra:

> **Hai hình chữ nhật có phần diện tích chung lớn hơn 0 khi và chỉ khi các khoảng chiếu của chúng lên cả hai trục `x` và `y` đều có phần giao có độ dài lớn hơn 0.**

Từ đó:

```cpp
int rong = min(rec1[2], rec2[2]) - max(rec1[0], rec2[0]);
int cao = min(rec1[3], rec2[3]) - max(rec1[1], rec2[1]);

return rong > 0 && cao > 0;
```

Đây là lời giải:

\[
\boxed{O(1)\text{ time},\ O(1)\text{ space}}
\]

và cũng là cách tiếp cận trực tiếp, ngắn gọn và dễ kiểm chứng nhất cho bài toán này.
