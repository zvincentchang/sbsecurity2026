# C++ Pointer 學習文件

## 目錄
1. [什麼是指標 (Pointer)](#什麼是指標)
2. [指標的基本概念](#指標的基本概念)
3. [指標的宣告與初始化](#指標的宣告與初始化)
4. [指標運算](#指標運算)
5. [指標與陣列](#指標與陣列)
6. [指標與函數](#指標與函數)
7. [指向指標的指標](#指向指標的指標)
8. [動態記憶體配置](#動態記憶體配置)
9. [函數指標](#函數指標)
10. [常見錯誤與最佳實踐](#常見錯誤與最佳實踐)

---

## 什麼是指標

**指標 (Pointer)** 是一種特殊的變數，它儲存的是**記憶體位址**，而非一般的數值。透過指標，我們可以：
- 直接存取和修改記憶體中的資料
- 實現動態記憶體配置
- 建立複雜的資料結構（如鏈結串列、樹）
- 提升程式效能（避免大量資料複製）
- 實現 Call by Reference（傳址呼叫）

### 為什麼需要指標？

```
變數            vs           指標
────────────                ────────────
直接儲存值                   儲存位址
int x = 10;                 int *ptr = &x;
```

---

## 指標的基本概念

### 核心觀念

1. **記憶體位址**：每個變數在記憶體中都有一個位址
2. **取址運算子 `&`**：取得變數的記憶體位址
3. **解參考運算子 `*`**：透過指標存取該位址的值

### 記憶體模型圖解

```
變數 x:
┌─────────────┬──────────┐
│ 位址        │   值     │
├─────────────┼──────────┤
│ 0x1000      │   70     │
└─────────────┴──────────┘

指標 ptr:
┌─────────────┬──────────┐
│ 位址        │   值     │
├─────────────┼──────────┤
│ 0x2000      │  0x1000  │  ← 儲存 x 的位址
└─────────────┴──────────┘

ptr 指向 x
```

---

## 指標的宣告與初始化

### 宣告語法

```cpp
資料型態 *指標名稱;
```

### 基本範例

```cpp
#include <iostream>
using namespace std;

int main() {
    int score = 70;
    int *ptr = &score;  // ptr 指向 score
    
    cout << "score 的值: " << score << endl;        // 70
    cout << "score 的位址: " << &score << endl;     // 例如: 0x7ffd123
    cout << "ptr 儲存的位址: " << ptr << endl;      // 0x7ffd123 (同上)
    cout << "ptr 指向的值: " << *ptr << endl;       // 70
    
    return 0;
}
```

### 不同的宣告方式

```cpp
int *p1;        // ✅ 推薦：* 靠近型態
int* p2;        // ✅ 也可以：* 靠近型態
int * p3;       // ✅ 也可以：* 居中
int *p4, *p5;   // ✅ 宣告多個指標

int* p6, p7;    // ⚠️ 注意！p6 是指標，p7 是整數
```

### 初始化指標

```cpp
int x = 10;

// 方法 1：宣告時初始化
int *ptr = &x;

// 方法 2：先宣告後賦值
int *ptr2;
ptr2 = &x;

// 方法 3：初始化為 nullptr (C++11)
int *ptr3 = nullptr;  // ✅ 推薦：安全的空指標

// 方法 4：初始化為 NULL (舊式)
int *ptr4 = NULL;     // ⚠️ 舊式寫法

// ❌ 危險：未初始化的指標（野指標）
int *ptr5;            // 指向不確定的位址
```

---

## 指標運算

### 1. 基本運算

```cpp
#include <iostream>
using namespace std;

int main() {
    int v = 3;
    int *ptr = &v;
    
    *ptr += 1;  // 相當於 v += 1;  現在 v = 4
    cout << *ptr << endl;  // 輸出: 4
    
    *ptr++;     // 先取值，再移動指標（通常不是我們要的）
    (*ptr)++;   // 先取值，再將值加 1（括號很重要！）
    
    return 0;
}
```

### 2. 指標算術運算

```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[5] = {10, 20, 30, 40, 50};
    int *p = arr;  // p 指向 arr[0]
    
    cout << *p << endl;        // 10
    cout << *(p + 1) << endl;  // 20 (移動一個 int)
    cout << *(p + 2) << endl;  // 30 (移動兩個 int)
    
    p++;                       // p 現在指向 arr[1]
    cout << *p << endl;        // 20
    
    p += 2;                    // p 現在指向 arr[3]
    cout << *p << endl;        // 40
    
    return 0;
}
```

### 指標運算規則

| 運算 | 說明 | 範例 |
|------|------|------|
| `ptr++` | 指標指向下一個元素 | `ptr = ptr + 1` |
| `ptr--` | 指標指向上一個元素 | `ptr = ptr - 1` |
| `ptr + n` | 移動 n 個元素 | `ptr + 3` |
| `ptr - n` | 往回移動 n 個元素 | `ptr - 2` |
| `ptr1 - ptr2` | 兩指標間的元素數量 | `5` |

**重要**：指標加減的單位是**資料型態的大小**，不是 byte！

```cpp
int arr[3] = {1, 2, 3};
int *p = arr;

// 假設 int 是 4 bytes
// p + 1 實際上是 p + (1 * sizeof(int)) = p + 4 bytes
```

---

## 指標與陣列

陣列名稱**就是**指向第一個元素的指標！

### 陣列與指標的等價性

```cpp
#include <iostream>
using namespace std;

int main() {
    int a[5] = {1, 3, 5, 7, 9};
    int *ptr = a;         // 等同於 int *ptr = &a[0];
    int *ptr2 = &a[0];    // 明確寫法
    
    int size = sizeof(a) / sizeof(int);
    
    for (int i = 0; i < size; i++) {
        cout << *(ptr + i) << " ";  // 等同於 a[i]
    }
    cout << endl;
    
    return 0;
}
```

### 四種存取陣列的方式

```cpp
int arr[5] = {10, 20, 30, 40, 50};
int *p = arr;

// 方法 1：陣列索引
cout << arr[2] << endl;        // 30

// 方法 2：指標算術
cout << *(arr + 2) << endl;    // 30

// 方法 3：指標索引
cout << p[2] << endl;          // 30

// 方法 4：移動指標後解參考
cout << *(p + 2) << endl;      // 30
```

### 指標遍歷陣列

```cpp
#include <iostream>
using namespace std;

int main() {
    int arry[3] = {4, 5, 6};
    int *ay = arry;  // 或 int *ay = &arry[0];
    
    for (int i = 0; i < 3; i++) {
        cout << *ay++ << endl;  // 先取值，再移動指標
    }
    // 迴圈結束後，ay 指向陣列外的位置
    
    return 0;
}
```

---

## 指標與函數

### 1. Call by Value vs Call by Reference

#### Call by Value（傳值呼叫）

```cpp
void increment(int x) {
    x++;  // 只修改副本，不影響原變數
}

int main() {
    int a = 5;
    increment(a);
    cout << a << endl;  // 還是 5
    return 0;
}
```

#### Call by Reference（使用指標）

```cpp
void increment(int *x) {
    (*x)++;  // 透過指標修改原變數
}

int main() {
    int a = 5;
    increment(&a);  // 傳遞位址
    cout << a << endl;  // 變成 6
    return 0;
}
```

#### Call by Reference（使用引用）

```cpp
#include <iostream>
using namespace std;

void increment(int &x) {  // 引用參數
    x++;  // 直接修改原變數
}

int main() {
    int a = 5;
    increment(a);  // 直接傳變數（編譯器自動處理）
    cout << a << endl;  // 變成 6
    return 0;
}
```

### 2. Swap 函數範例

```cpp
#include <iostream>
using namespace std;

// 使用引用
void swap(int &x, int &y) {
    int temp = x;
    x = y;
    y = temp;
}

int main() {
    int a = 1, b = 2;
    cout << "交換前: a=" << a << " b=" << b << endl;
    
    swap(a, b);
    
    cout << "交換後: a=" << a << " b=" << b << endl;
    return 0;
}
```

### 3. 陣列作為函數參數

```cpp
// 這三種寫法完全等價
void printArray(int arr[], int size);      // 寫法 1
void printArray(int *arr, int size);       // 寫法 2
void printArray(int arr[10], int size);    // 寫法 3（10 會被忽略）
```

**重要**：陣列傳入函數時，實際上是**傳指標**，所以必須另外傳遞大小！

```cpp
#include <iostream>
using namespace std;

void modifyArray(int *arr, int size) {
    for (int i = 0; i < size; i++) {
        arr[i] *= 2;  // 直接修改原陣列
    }
}

int main() {
    int numbers[5] = {1, 2, 3, 4, 5};
    modifyArray(numbers, 5);
    
    for (int i = 0; i < 5; i++) {
        cout << numbers[i] << " ";  // 輸出: 2 4 6 8 10
    }
    cout << endl;
    
    return 0;
}
```

---

## 指向指標的指標

指標也是變數，所以也可以有指向指標的指標！

### 二級指標概念

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 7;
    int *ptr = &a;      // ptr 指向 a
    int **ptrptr;       // ptrptr 是指向指標的指標
    ptrptr = &ptr;      // ptrptr 指向 ptr
    
    cout << "a 的值: " << a << endl;           // 7
    cout << "*ptr: " << *ptr << endl;          // 7
    cout << "**ptrptr: " << **ptrptr << endl;  // 7
    
    return 0;
}
```

### 記憶體圖解

```
變數 a:
┌────────┬─────┐
│ 0x1000 │  7  │
└────────┴─────┘
         ↑
指標 ptr:
┌────────┬────────┐
│ 0x2000 │ 0x1000 │  ← 指向 a
└────────┴────────┘
         ↑
指標的指標 ptrptr:
┌────────┬────────┐
│ 0x3000 │ 0x2000 │  ← 指向 ptr
└────────┴────────┘

**ptrptr = *(*ptrptr) = *(ptr) = a 的值 = 7
```

### 二級指標的應用

```cpp
void allocateMemory(int **ptr, int size) {
    *ptr = new int[size];  // 透過二級指標修改指標本身
}

int main() {
    int *arr = nullptr;
    allocateMemory(&arr, 5);  // 傳遞指標的位址
    
    // 現在 arr 指向動態配置的陣列
    for (int i = 0; i < 5; i++) {
        arr[i] = i * 10;
    }
    
    delete[] arr;
    return 0;
}
```

---

## 動態記憶體配置

使用 `new` 和 `delete` 在**堆積 (heap)** 上動態配置記憶體。

### 基本語法

```cpp
// 配置單一變數
int *ptr = new int;        // 配置一個 int
*ptr = 10;
delete ptr;                // 釋放記憶體

// 配置陣列
int *arr = new int[5];     // 配置 5 個 int
arr[0] = 1;
delete[] arr;              // 釋放陣列（注意 [] ！）

// 配置並初始化 (C++11)
int *p = new int(42);      // 配置並初始化為 42
int *a = new int[3]{1,2,3}; // 配置並初始化陣列
```

### 完整範例

```cpp
#include <iostream>
#include <string>
using namespace std;

string* createString() {
    string *s = new string("Hello");  // 在 heap 上配置
    return s;  // ✅ 可以安全回傳
}

int main() {
    string *st = createString();
    cout << *st << endl;  // 輸出: Hello
    
    delete st;  // 記得釋放記憶體
    st = nullptr;  // 好習慣：避免 dangling pointer
    
    return 0;
}
```

### Stack vs Heap

| 特性 | Stack（堆疊） | Heap（堆積） |
|------|--------------|-------------|
| 配置方式 | 自動 | 手動 (new/delete) |
| 速度 | 快 | 較慢 |
| 大小 | 有限（通常 1-8 MB） | 較大 |
| 生命週期 | 離開作用域自動釋放 | 手動管理 |
| 用途 | 局部變數 | 大型/動態資料 |

### 記憶體洩漏 (Memory Leak)

```cpp
// ❌ 錯誤：記憶體洩漏
void leak() {
    int *p = new int[100];
    // 忘記 delete[]
}  // p 被銷毀，但記憶體沒被釋放！

// ✅ 正確：配對使用 new 和 delete
void noLeak() {
    int *p = new int[100];
    // ... 使用 p ...
    delete[] p;  // 記得釋放
}
```

---

## 函數指標

函數指標可以儲存函數的位址，實現**回呼函數 (callback)**。

### 基本語法

```cpp
傳回值型態 (*指標名稱)(參數列表);
```

### 簡單範例

```cpp
#include <iostream>
using namespace std;

int add(int a, int b) {
    return a + b;
}

int multiply(int a, int b) {
    return a * b;
}

int main() {
    int (*fn)(int, int);  // 宣告函數指標
    
    fn = add;  // 指向 add 函數
    cout << fn(3, 4) << endl;  // 輸出: 7
    
    fn = multiply;  // 指向 multiply 函數
    cout << fn(3, 4) << endl;  // 輸出: 12
    
    return 0;
}
```

### 函數指標作為參數

```cpp
#include <iostream>
using namespace std;

int add(int x, int y) {
    return x + y;
}

int multiply(int x, int y) {
    return x * y;
}

// action 接受函數指標作為參數
int action(int x, int y, int (*fn)(int, int)) {
    return fn(x, y);
}

int main() {
    int a = 5, b = 6;
    
    int v1 = action(a, b, add);       // 傳入 add 函數
    int v2 = action(a, b, multiply);  // 傳入 multiply 函數
    
    cout << "v1=" << v1 << " v2=" << v2 << endl;
    // 輸出: v1=11 v2=30
    
    return 0;
}
```

### 使用 typedef 簡化

```cpp
// 定義函數指標型態
typedef int (*MathFunc)(int, int);

int action(int x, int y, MathFunc fn) {  // 更清晰！
    return fn(x, y);
}
```

---

## 常見錯誤與最佳實踐

### ⚠️ 常見錯誤

#### 1. 野指標 (Wild Pointer)

```cpp
int *p;           // ❌ 未初始化
*p = 10;          // 💥 危險！指向未知位置
```

#### 2. 懸空指標 (Dangling Pointer)

```cpp
int *p = new int(10);
delete p;
*p = 20;          // ❌ 存取已釋放的記憶體
```

#### 3. 記憶體洩漏

```cpp
int *p = new int[100];
p = new int[200];  // ❌ 第一次配置的記憶體洩漏了
```

#### 4. delete 與 delete[] 混用

```cpp
int *p = new int[10];
delete p;         // ❌ 應該用 delete[]
```

#### 5. 多次 delete

```cpp
int *p = new int(10);
delete p;
delete p;         // ❌ 重複刪除
```

#### 6. 回傳局部變數的位址

```cpp
int* dangerous() {
    int x = 10;
    return &x;    // ❌ x 在函數結束後被銷毀
}
```

### ✅ 最佳實踐

#### 1. 總是初始化指標

```cpp
int *p = nullptr;  // ✅ 初始化為 nullptr
```

#### 2. delete 後設為 nullptr

```cpp
delete p;
p = nullptr;      // ✅ 避免 dangling pointer
```

#### 3. 檢查指標是否為 nullptr

```cpp
if (p != nullptr) {
    *p = 10;      // ✅ 安全
}
```

#### 4. 使用智慧指標 (C++11)

```cpp
#include <memory>

std::unique_ptr<int> p(new int(10));  // 自動管理記憶體
// 不需要 delete
```

#### 5. 配對使用 new/delete

```cpp
int *p1 = new int;         // 配對 delete
int *p2 = new int[10];     // 配對 delete[]

delete p1;
delete[] p2;
```

#### 6. 避免裸指標，優先使用引用

```cpp
// ❌ 不推薦
void func(int *p) {
    if (p != nullptr) *p = 10;
}

// ✅ 推薦（如果參數不應為 null）
void func(int &x) {
    x = 10;  // 更安全、更簡潔
}
```

---

## 指標 vs 引用

| 特性 | 指標 | 引用 |
|------|------|------|
| 語法 | `int *p` | `int &r` |
| 可為 null | ✅ 可以 | ❌ 不行 |
| 可重新指向 | ✅ 可以 | ❌ 不行 |
| 需要解參考 | ✅ 需要 `*p` | ❌ 直接用 |
| 記憶體 | 佔用空間 | 不佔用（編譯器處理） |
| 用途 | 動態配置、複雜結構 | 函數參數、避免複製 |

### 使用建議

```cpp
// 使用指標的情況：
// 1. 需要動態配置記憶體
int *arr = new int[100];

// 2. 需要重新指向不同物件
int *p = &a;
p = &b;  // 改變指向

// 3. 可能為 null
int *p = nullptr;

// 使用引用的情況：
// 1. 函數參數（避免複製大物件）
void process(const vector<int> &data);

// 2. 運算子多載
class MyClass {
    MyClass& operator=(const MyClass &other);
};

// 3. 範圍迴圈
for (auto &item : container) {
    // 避免複製
}
```

---

## 實用範例

### 1. 動態二維陣列

```cpp
#include <iostream>
using namespace std;

int main() {
    int rows = 3, cols = 4;
    
    // 配置二維陣列
    int **matrix = new int*[rows];
    for (int i = 0; i < rows; i++) {
        matrix[i] = new int[cols];
    }
    
    // 初始化
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            matrix[i][j] = i * cols + j;
        }
    }
    
    // 列印
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            cout << matrix[i][j] << " ";
        }
        cout << endl;
    }
    
    // 釋放記憶體
    for (int i = 0; i < rows; i++) {
        delete[] matrix[i];
    }
    delete[] matrix;
    
    return 0;
}
```

### 2. 鏈結串列節點

```cpp
struct Node {
    int data;
    Node *next;
    
    Node(int val) : data(val), next(nullptr) {}
};

int main() {
    Node *head = new Node(1);
    head->next = new Node(2);
    head->next->next = new Node(3);
    
    // 遍歷
    Node *current = head;
    while (current != nullptr) {
        cout << current->data << " ";
        current = current->next;
    }
    
    // 釋放記憶體
    while (head != nullptr) {
        Node *temp = head;
        head = head->next;
        delete temp;
    }
    
    return 0;
}
```

---

## 練習題

### 基礎題
1. 撰寫程式使用指標交換兩個變數的值
2. 使用指標遍歷陣列並計算總和
3. 撰寫函數使用指標找出陣列中的最大值

### 進階題
1. 實作一個函數使用指標反轉陣列
2. 使用動態記憶體配置建立可變大小的陣列
3. 撰寫函數使用指標複製字串

### 挑戰題
1. 實作簡單的鏈結串列（插入、刪除、搜尋）
2. 使用函數指標實作簡單的計算機
3. 實作動態二維陣列並進行矩陣運算

---

## 重點回顧

### 核心概念
- 指標儲存**記憶體位址**
- `&` 取得位址，`*` 解參考取值
- 指標運算單位是**資料型態大小**
- 陣列名稱**等同於**指向第一個元素的指標

### 記憶體管理
- Stack: 自動管理，快速，空間有限
- Heap: 手動管理 (new/delete)，靈活，需小心
- 總是配對使用 `new`/`delete`
- 優先考慮智慧指標 (modern C++)

### 安全守則
1. ✅ 總是初始化指標
2. ✅ 檢查 nullptr
3. ✅ delete 後設為 nullptr
4. ✅ 避免回傳局部變數位址
5. ✅ 避免記憶體洩漏

---

## 下一步學習

- **智慧指標**：`unique_ptr`、`shared_ptr`、`weak_ptr`
- **STL 容器**：`vector`、`list`、`map` 等
- **資料結構**：鏈結串列、樹、圖
- **多型與虛擬函數**：使用指標實現多型
- **記憶體管理進階**：placement new、記憶體池

---

## 參考資源

- C++ Reference: https://en.cppreference.com/
- Effective C++ (書籍)
- C++ Primer (書籍)

---

**最後更新日期：2026-01-09**

**祝學習順利！指標是 C++ 的精髓，掌握它將大大提升您的程式設計能力！** 🚀
