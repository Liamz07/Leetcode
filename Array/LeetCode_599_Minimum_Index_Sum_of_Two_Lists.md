# LeetCode 599 - Minimum Index Sum of Two Lists

## Đề bài

Cho hai mảng chuỗi `list1` và `list2`.

Hãy tìm tất cả các nhà hàng xuất hiện trong cả hai danh sách sao cho tổng chỉ số xuất hiện của chúng trong hai mảng là nhỏ nhất.

Trả về danh sách các nhà hàng thỏa mãn điều kiện trên.

---

## Ví dụ

### Ví dụ 1

```text
Input:
list1 = ["Shogun","Tapioca Express","Burger King","KFC"]
list2 = ["Piatti","The Grill at Torrey Pines","Hungry Hunter Steakhouse","Shogun"]

Output:
["Shogun"]
```

Giải thích:

```text
Shogun có tổng chỉ số = 0 + 3 = 3
```

Đây là giá trị nhỏ nhất.

---

## Phân tích bài toán

Ta cần:

1. Tìm các chuỗi xuất hiện trong cả hai danh sách.
2. Tính:

```cpp
index_in_list1 + index_in_list2
```

3. Tìm giá trị nhỏ nhất.
4. Trả về tất cả nhà hàng có tổng chỉ số nhỏ nhất.

---

## Ý tưởng tối ưu

### Quan sát

Nếu duyệt mọi cặp phần tử:

```cpp
for i
    for j
```

thì độ phức tạp:

```text
O(n × m)
```

không tối ưu.

Ta cần tìm vị trí của một nhà hàng trong `list1` thật nhanh.

---

### Sử dụng Hash Map

Lưu:

```cpp
restaurant -> index
```

của toàn bộ phần tử trong `list1`.

Ví dụ:

```cpp
list1

Shogun            -> 0
Tapioca Express   -> 1
Burger King       -> 2
KFC               -> 3
```

Tra cứu bằng:

```cpp
unordered_map<string,int>
```

mất trung bình:

```text
O(1)
```

---

## Các bước thực hiện

### Bước 1

Lưu toàn bộ phần tử của `list1` vào Hash Map.

```cpp
mp[list1[i]] = i;
```

---

### Bước 2

Duyệt `list2`.

Nếu phần tử hiện tại tồn tại trong Hash Map:

```cpp
mp.count(list2[i])
```

thì ta đã tìm được một nhà hàng chung.

---

### Bước 3

Tính:

```cpp
sum = mp[list2[i]] + i;
```

---

### Bước 4

So sánh với tổng nhỏ nhất hiện tại.

#### Trường hợp 1

Tìm được tổng nhỏ hơn:

```cpp
sum < minSum
```

Cập nhật:

```cpp
minSum = sum
```

và xóa kết quả cũ.

---

#### Trường hợp 2

Tìm được tổng bằng:

```cpp
sum == minSum
```

Thêm vào đáp án.

---

## Minh họa

```text
list1:
Shogun -> 0
Tapioca Express -> 1
Burger King -> 2
KFC -> 3

list2:
Piatti -> không tồn tại
The Grill -> không tồn tại
Hungry Hunter -> không tồn tại
Shogun -> tồn tại
```

Tổng chỉ số:

```text
0 + 3 = 3
```

Đáp án:

```text
["Shogun"]
```

---

## Đánh giá lời giải của bạn

Lời giải bạn gửi:

```cpp
class Solution {
public:
    vector<string> findRestaurant(vector<string>& list1,
                                  vector<string>& list2) {

        unordered_map<string, int> mp;

        int dem = -1;
        vector<string> kq;

        for (int i = 0; i < list1.size(); i++)
            mp[list1[i]] = i;

        for (int i = 0; i < list2.size(); i++) {

            if (mp.count(list2[i]) > 0) {

                if (dem == -1) {

                    dem = mp[list2[i]] + i;
                    kq.push_back(list2[i]);

                } else if (dem > mp[list2[i]] + i) {

                    dem = mp[list2[i]] + i;
                    kq.clear();
                    kq.push_back(list2[i]);

                } else if (dem == mp[list2[i]] + i) {

                    kq.push_back(list2[i]);
                }
            }
        }

        return kq;
    }
};
```

### Ưu điểm

- Sử dụng `unordered_map`.
- Tra cứu trung bình O(1).
- Chỉ duyệt mỗi danh sách một lần.
- Không dùng cấu trúc dữ liệu thừa.

=> Đây chính là hướng giải tối ưu của bài toán.

---

## Có thể cải thiện gì?

Có thể làm code gọn hơn bằng cách dùng:

```cpp
INT_MAX
```

thay cho biến đánh dấu `-1`.

Ví dụ:

```cpp
int minSum = INT_MAX;
```

Khi đó logic dễ đọc hơn.

Ngoài ra có thể lưu:

```cpp
int sum = mp[list2[i]] + i;
```

vào biến tạm để tránh tính lại nhiều lần.

Đây chỉ là cải thiện về độ rõ ràng của code, không cải thiện độ phức tạp.

---

## Phiên bản C++ được khuyến nghị

```cpp
class Solution {
public:
    vector<string> findRestaurant(vector<string>& list1,
                                  vector<string>& list2) {

        unordered_map<string, int> pos;

        for (int i = 0; i < list1.size(); i++)
            pos[list1[i]] = i;

        int minSum = INT_MAX;
        vector<string> ans;

        for (int i = 0; i < list2.size(); i++) {

            auto it = pos.find(list2[i]);

            if (it == pos.end())
                continue;

            int sum = it->second + i;

            if (sum < minSum) {

                minSum = sum;
                ans.clear();
                ans.push_back(list2[i]);

            } else if (sum == minSum) {

                ans.push_back(list2[i]);
            }
        }

        return ans;
    }
};
```

---

## Phân tích độ phức tạp

Gọi:

```text
n = list1.size()
m = list2.size()
```

### Thời gian

Xây Hash Map:

```text
O(n)
```

Duyệt list2:

```text
O(m)
```

Tổng:

```text
O(n + m)
```

---

### Bộ nhớ

Hash Map chứa toàn bộ phần tử của `list1`:

```text
O(n)
```

---

## Kết luận

Lời giải của bạn đã đạt độ phức tạp tối ưu:

```text
Time  : O(n + m)
Space : O(n)
```

Không tồn tại lời giải tổng quát tốt hơn về độ phức tạp thời gian vì ít nhất ta phải đọc toàn bộ dữ liệu đầu vào một lần.

Điểm có thể cải thiện chỉ là tính dễ đọc của mã nguồn (dùng `INT_MAX`, biến `sum`, `find()` thay cho `count()` + truy cập lại Hash Map).
