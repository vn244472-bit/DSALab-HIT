# Tuần 11: Binary Search Tree — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt đầy đủ BST: insert, search, delete (3 trường hợp), và các ứng dụng.

---

### Bài 1: BST đầy đủ ⭐⭐
Cài đặt BST với insert, search, delete (xử lý cả 3 trường hợp: lá, 1 con, 2 con).
#include <stdio.h>
#include <stdlib.h>

// Định nghĩa cấu trúc nút của cây
typedef struct Node {
    int key;
    struct Node* left;
    struct Node* right;
} Node;

// Hàm tạo một nút mới
Node* createNode(int key) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode != NULL) {
        newNode->key = key;
        newNode->left = NULL;
        newNode->right = NULL;
    }
    return newNode;
}

// 1. Thao tác Chèn (Insert)
Node* insert(Node* root, int key) {
    if (root == NULL) return createNode(key);
    
    if (key < root->key) {
        root->left = insert(root->left, key);
    } else if (key > root->key) {
        root->right = insert(root->right, key);
    }
    return root;
}

// 2. Thao tác Tìm kiếm (Search)
Node* search(Node* root, int key) {
    if (root == NULL || root->key == key) return root;
    
    if (key < root->key) return search(root->left, key);
    return search(root->right, key);
}

// Hàm tìm nút có giá trị nhỏ nhất (phục vụ cho việc xóa nút có 2 con)
Node* minValueNode(Node* node) {
    Node* current = node;
    while (current && current->left != NULL) {
        current = current->left;
    }
    return current;
}

// 3. Thao tác Xóa (Delete) - Xử lý cả 3 trường hợp
Node* deleteNode(Node* root, int key) {
    if (root == NULL) return root;

    // Xác định vị trí nút cần xóa
    if (key < root->key) {
        root->left = deleteNode(root->left, key);
    } else if (key > root->key) {
        root->right = deleteNode(root->right, key);
    } else {
        // Đây chính là nút cần xóa!

        // Trường hợp 1 & 2: Nút lá hoặc chỉ có 1 con (trái hoặc phải)
        if (root->left == NULL) {
            Node* temp = root->right;
            free(root);
            return temp;
        } else if (root->right == NULL) {
            Node* temp = root->left;
            free(root);
            return temp;
        }

        // Trường hợp 3: Nút có 2 con
        // Tìm nút kế vị (nhỏ nhất bên cây con phải)
        Node* temp = minValueNode(root->right);
        
        // Sao chép giá trị của nút kế vị vào nút hiện tại
        root->key = temp->key;
        
        // Xóa nút kế vị cũ đi
        root->right = deleteNode(root->right, temp->key);
    }
    return root;
}

// Duyệt cây theo thứ tự giữa (In-order) để kiểm tra kết quả
void inorder(Node* root) {
    if (root != NULL) {
        inorder(root->left);
        printf("%d ", root->key);
        inorder(root->right);
    }
}

int main() {
    Node* root = NULL;
    root = insert(root, 50);
    insert(root, 30);
    insert(root, 20);
    insert(root, 40);
    insert(root, 70);
    insert(root, 60);
    insert(root, 80);

    printf_s("Cay ban dau (In-order): ");
    inorder(root);
    printf_s("\n");

    printf_s("Xoa nut la (20)...\n");
    root = deleteNode(root, 20);
    inorder(root);
    printf_s("\n");

    printf_s("Xoa nut co 1 con (30)...\n");
    root = deleteNode(root, 30);
    inorder(root);
    printf_s("\n");

    printf_s("Xoa nut co 2 con (50)...\n");
    root = deleteNode(root, 50);
    inorder(root);
    printf_s("\n");

    return 0;
}
### Bài 2: Kiểm tra BST hợp lệ ⭐⭐
Cho một cây nhị phân bất kỳ — kiểm tra có phải BST không. Tìm phần tử lớn thứ K.
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

typedef struct Node {
    int key;
    struct Node* left;
    struct Node* right;
} Node;

Node* createNode(int key) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode) {
        newNode->key = key;
        newNode->left = NULL;
        newNode->right = NULL;
    }
    return newNode;
}

// 1. Kiểm tra cây nhị phân có hợp lệ BST không
int isValidBSTUtil(Node* node, long long min, long long max) {
    if (node == NULL) return 1;

    // Nếu giá trị nút vi phạm khoảng cho phép
    if (node->key <= min || node->key >= max) return 0;

    // Kiểm tra các cây con bên dưới với khoảng ràng buộc mới
    return isValidBSTUtil(node->left, min, node->key) &&
           isValidBSTUtil(node->right, node->key, max);
}

int isValidBST(Node* root) {
    return isValidBSTUtil(root, LLONG_MIN, LLONG_MAX);
}

// 2. Tìm phần tử lớn thứ K sử dụng duyệt Reverse In-order
void findKthLargestUtil(Node* root, int k, int* count, int* result) {
    if (root == NULL || *count >= k) return;

    // Duyệt bên phải trước (lớn hơn)
    findKthLargestUtil(root->right, k, count, result);

    // Xử lý nút gốc hiện tại
    (*count)++;
    if (*count == k) {
        *result = root->key;
        return;
    }

    // Duyệt bên trái (nhỏ hơn)
    findKthLargestUtil(root->left, k, count, result);
}

int findKthLargest(Node* root, int k) {
    int count = 0;
    int result = -1;
    findKthLargestUtil(root, k, &count, &result);
    return result;
}

int main() {
    // Tạo một cây BST hợp lệ
    //        50
    //       /  \
    //      30   70
    //     /
    //    20
    Node* root = createNode(50);
    root->left = createNode(30);
    root->right = createNode(70);
    root->left->left = createNode(20);

    if (isValidBST(root)) {
        printf_s("Cay hien tai la BST hop le.\n");
    } else {
        printf_s("Cay hien tai KHONG phai la BST hop le.\n");
    }

    int k = 2;
    int kth = findKthLargest(root, k);
    printf_s("Phan tu lon thu %d la: %d\n", k, kth);

    // Thử tạo lỗi logic để biến cây thành KHÔNG hợp lệ
    root->left->right = createNode(60); // Lỗi: 60 nằm bên nhánh trái của 50 -> Sai quy tắc!
    if (isValidBST(root)) {
        printf_s("Sau khi sua: Van la BST hop le.\n");
    } else {
        printf_s("Sau khi sua: Cay da bi LOI (Khong con la BST hop le).\n");
    }

    return 0;
}
### Bài 3: Cân bằng BST ⭐⭐⭐
Chuyển BST mất cân bằng thành BST cân bằng (AVL concept cơ bản — chỉ single rotation).
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int key;
    int height;
    struct Node* left;
    struct Node* right;
} Node;

int max(int a, int b) { return (a > b) ? a : b; }

int height(Node* n) { return (n == NULL) ? 0 : n->height; }

Node* createNode(int key) {
    Node* node = (Node*)malloc(sizeof(Node));
    if (node) {
        node->key = key;
        node->left = NULL;
        node->right = NULL;
        node->height = 1; // Nút mới ban đầu có độ cao là 1
    }
    return node;
}

// Hàm xoay phải (Xử lý lệch Trái-Trái)
Node* rightRotate(Node* y) {
    Node* x = y->left;
    Node* T2 = x->right;

    // Thực hiện xoay
    x->right = y;
    y->left = T2;

    // Cập nhật lại độ cao
    y->height = max(height(y->left), height(y->right)) + 1;
    x->height = max(height(x->left), height(x->right)) + 1;

    return x; // Gốc mới
}

// Hàm xoay trái (Xử lý lệch Phải-Phải)
Node* leftRotate(Node* x) {
    Node* y = x->right;
    Node* T2 = y->left;

    // Thực hiện xoay
    y->left = x;
    x->right = T2;

    // Cập nhật lại độ cao
    x->height = max(height(x->left), height(x->right)) + 1;
    y->height = max(height(y->left), height(y->right)) + 1;

    return y; // Gốc mới
}

// Lấy hệ số cân bằng
int getBalance(Node* n) {
    return (n == NULL) ? 0 : height(n->left) - height(n->right);
}

// Chèn phần tử tự động cân bằng (Chỉ xử lý Single Rotation)
Node* insertAVLBasic(Node* node, int key) {
    if (node == NULL) return createNode(key);

    if (key < node->key)
        node->left = insertAVLBasic(node->left, key);
    else if (key > node->key)
        node->right = insertAVLBasic(node->right, key);
    else
        return node; // Không nhận trùng giá trị

    // Cập nhật độ cao của nút tổ tiên này
    node->height = 1 + max(height(node->left), height(node->right));

    // Kiểm tra trạng thái cân bằng
    int balance = getBalance(node);

    // Trường hợp Trái Trái (Lệch trái hoàn toàn) -> Xoay Phải đơn
    if (balance > 1 && key < node->left->key)
        return rightRotate(node);

    // Trường hợp Phải Phải (Lệch phải hoàn toàn) -> Xoay Trái đơn
    if (balance < -1 && key > node->right->key)
        return leftRotate(node);

    return node;
}

void preOrder(Node* root) {
    if (root != NULL) {
        printf("%d(H:%d) ", root->key, root->height);
        preOrder(root->left);
        preOrder(root->right);
    }
}

int main() {
    Node* root = NULL;

    // Giả lập chuỗi chèn lệch hẳn về bên phải: 10 -> 20 -> 30
    printf_s("Chen chuỗi gay mat can bang: 10, 20, 30...\n");
    root = insertAVLBasic(root, 10);
    root = insertAVLBasic(root, 20);
    root = insertAVLBasic(root, 30); // Tại đây xảy ra xoay trái tại nút 10

    printf_s("Cay sau khi tu dong can bang (Pre-order): ");
    preOrder(root);
    printf_s("\n(Nut 20 da len lam goc thay vi nut 10)\n");

    return 0;
}
### Bài 4: 🔥 Dự Án Mini — Từ Điển Anh–Việt BST ⭐⭐⭐
> **Cảm hứng:** BaiTapTongHop — Từ điển BST (DSALab)

Xây dựng từ điển Anh–Việt bằng BST, tìm kiếm siêu nhanh:
```
=== 📖 TỪ ĐIỂN ANH–VIỆT (BST) ===
1. Thêm từ mới
2. Tra từ
3. Xóa từ
4. Duyệt từ điển (A→Z)
5. Thống kê

Tra từ: "algorithm"
✅ algorithm = thuật toán
   Ví dụ: "Sorting algorithm" = thuật toán sắp xếp
   Phát âm: /ˈælɡərɪðəm/
   (Tìm thấy sau 4 bước so sánh)

Duyệt từ điển (In-order = thứ tự A→Z):
array → mảng
binary → nhị phân
data → dữ liệu
graph → đồ thị
...
Tổng: 150 từ | Chiều cao BST: 8
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STR 100

// Cấu trúc một từ trong từ điển
typedef struct WordNode {
    char word[MAX_STR];
    char meaning[MAX_STR];
    char pronunciation[MAX_STR];
    char example[MAX_STR];
    struct WordNode* left;
    struct WordNode* right;
} WordNode;

// Hàm tạo nút từ mới
WordNode* createWordNode(const char* w, const char* m, const char* p, const char* e) {
    WordNode* newNode = (WordNode*)malloc(sizeof(WordNode));
    if (newNode) {
        strcpy_s(newNode->word, MAX_STR, w);
        strcpy_s(newNode->meaning, MAX_STR, m);
        strcpy_s(newNode->pronunciation, MAX_STR, p);
        strcpy_s(newNode->example, MAX_STR, e);
        newNode->left = NULL;
        newNode->right = NULL;
    }
    return newNode;
}

// 1. Thêm từ mới vào từ điển
WordNode* insertWord(WordNode* root, const char* w, const char* m, const char* p, const char* e) {
    if (root == NULL) return createWordNode(w, m, p, e);

    int cmp = strcmp(w, root->word);
    if (cmp < 0) {
        root->left = insertWord(root->left, w, m, p, e);
    } else if (cmp > 0) {
        root->right = insertWord(root->right, w, m, p, e);
    } else {
        // Nếu từ đã tồn tại, cập nhật lại nghĩa
        strcpy_s(root->meaning, MAX_STR, m);
        strcpy_s(root->pronunciation, MAX_STR, p);
        strcpy_s(root->example, MAX_STR, e);
    }
    return root;
}

// 2. Tra từ và đếm số bước so sánh
WordNode* searchWord(WordNode* root, const char* w, int* steps) {
    if (root == NULL) return NULL;
    
    (*steps)++;
    int cmp = strcmp(w, root->word);
    
    if (cmp == 0) return root;
    if (cmp < 0) return searchWord(root->left, w, steps);
    return searchWord(root->right, w, steps);
}

// Tìm từ thế mạng nhỏ nhất bên nhánh phải
WordNode* minWordNode(WordNode* node) {
    WordNode* current = node;
    while (current && current->left != NULL) current = current->left;
    return current;
}

// 3. Xóa một từ khỏi từ điển
WordNode* deleteWord(WordNode* root, const char* w) {
    if (root == NULL) return root;

    int cmp = strcmp(w, root->word);
    if (cmp < 0) {
        root->left = deleteWord(root->left, w);
    } else if (cmp > 0) {
        root->right = deleteWord(root->right, w);
    } else {
        if (root->left == NULL) {
            WordNode* temp = root->right;
            free(root);
            return temp;
        } else if (root->right == NULL) {
            WordNode* temp = root->left;
            free(root);
            return temp;
        }
        WordNode* temp = minWordNode(root->right);
        strcpy_s(root->word, MAX_STR, temp->word);
        strcpy_s(root->meaning, MAX_STR, temp->meaning);
        strcpy_s(root->pronunciation, MAX_STR, temp->pronunciation);
        strcpy_s(root->example, MAX_STR, temp->example);
        
        root->right = deleteWord(root->right, temp->word);
    }
    return root;
}

// 4. Duyệt từ điển theo thứ tự A-Z (In-order)
void displayDictionary(WordNode* root) {
    if (root != NULL) {
        displayDictionary(root->left);
        printf_s("  %-15s -> %s (%s)\n", root->word, root->meaning, root->pronunciation);
        displayDictionary(root->right);
    }
}

// 5. Thống kê số lượng từ và tính chiều cao của cây
int countWords(WordNode* root) {
    if (root == NULL) return 0;
    return 1 + countWords(root->left) + countWords(root->right);
}

int maxDepth(WordNode* root) {
    if (root == NULL) return 0;
    int leftDepth = maxDepth(root->left);
    int rightDepth = maxDepth(root->right);
    return (leftDepth > rightDepth) ? (leftDepth + 1) : (rightDepth + 1);
}

// Giao diện Menu quản lý điều khiển từ điển
void menu() {
    WordNode* dictionary = NULL;
    int choice;
    char w[MAX_STR], m[MAX_STR], p[MAX_STR], e[MAX_STR];

    // Thêm dữ liệu mẫu sẵn để chạy demo thuận tiện
    dictionary = insertWord(dictionary, "algorithm", "thuat toan", "/'aelgaeridhaem/", "Sorting algorithm");
    dictionary = insertWord(dictionary, "binary", "nhi phan", "/'bainaeri/", "Binary tree");
    dictionary = insertWord(dictionary, "array", "mang", "/ae'rei/", "Static array");
    dictionary = insertWord(dictionary, "data", "du lieu", "/'deita/", "Data structure");

    do {
        printf_s("\n=== DISCOVERY BST DICTIONARY MENU ===\n");
        printf_s("1. Them tu moi\n");
        printf_s("2. Tra tu\n");
        printf_s("3. Xoa tu\n");
        printf_s("4. Duyet tu dien (A->Z)\n");
        printf_s("5. Thong ke he thong\n");
        printf_s("6. Thoat\n");
        printf_s("Lua chon cua ban: ");
        if (scanf_s("%d", &choice) != 1) break;
        getchar(); // Đọc bỏ ký tự newline thừa

        switch (choice) {
            case 1:
                printf_s("Nhap tu tieng Anh: ");
                gets_s(w, MAX_STR);
                printf_s("Nhap nghia tieng Viet: ");
                gets_s(m, MAX_STR);
                printf_s("Nhap phat am: ");
                gets_s(p, MAX_STR);
                printf_s("Nhap vi du: ");
                gets_s(e, MAX_STR);
                dictionary = insertWord(dictionary, w, m, p, e);
                printf_s("Da them hoac cap nhat tu thanh cong!\n");
                break;
            case 2:
                printf_s("Nhap tu can tra: ");
                gets_s(w, MAX_STR);
                int steps = 0;
                WordNode* res = searchWord(dictionary, w, &steps);
                if (res) {
                    printf_s("\x1b[32mhanh cong\x1b[0m: %s = %s\n", res->word, res->meaning);
                    printf_s("   Phat am: %s\n", res->pronunciation);
                    printf_s("   Vi du: %s\n", res->example);
                    printf_s("   (Tim thay sau %d buoc so sanh)\n", steps);
                } else {
                    printf_s("Khong tim thay tu '%s' trong tu dien. (Da xet %d buoc)\n", w, steps);
                }
                break;
            case 3:
                printf_s("Nhap tu muon xoa: ");
                gets_s(w, MAX_STR);
                dictionary = deleteWord(dictionary, w);
                printf_s("Da thuc hien lenh xoa.\n");
                break;
            case 4:
                printf_s("\nDanh sach tu dien tu A->Z:\n");
                displayDictionary(dictionary);
                break;
            case 5:
                printf_s("\n--- THONG KE DIEN TICH TUE ---\n");
                printf_s("Tong so tu vung hien tai: %d\n", countWords(dictionary));
                printf_s("Chieu cao hien tai cua BST: %d\n", maxDepth(dictionary));
                break;
        }
    } while (choice != 6);
}

int main() {
    menu();
    return 0;
}
---
📁 Tham khảo: `Chuong4_CauTrucCay/Chuong4_CauTrucCay.cpp`
