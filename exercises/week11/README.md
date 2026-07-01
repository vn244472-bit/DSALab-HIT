# Tuần 11: Binary Search Tree — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt đầy đủ BST: insert, search, delete (3 trường hợp), và các ứng dụng.

---

### Bài 1: BST đầy đủ ⭐⭐
Cài đặt BST với insert, search, delete (xử lý cả 3 trường hợp: lá, 1 con, 2 con).
#include <iostream>

struct Node {
    int key;
    Node* left;
    Node* right;
    Node(int val) : key(val), left(nullptr), right(nullptr) {}
};

class BST {
private:
    Node* root;

    Node* insert(Node* node, int key) {
        if (!node) return new Node(key);
        if (key < node->key) node->left = insert(node->left, key);
        else if (key > node->key) node->right = insert(node->right, key);
        return node;
    }

    bool search(Node* node, int key) {
        if (!node) return false;
        if (node->key == key) return true;
        if (key < node->key) return search(node->left, key);
        return search(node->right, key);
    }

    Node* findMin(Node* node) {
        while (node && node->left) node = node->left;
        return node;
    }

    Node* deleteNode(Node* node, int key) {
        if (!node) return nullptr;

        if (key < node->key) {
            node->left = deleteNode(node->left, key);
        } else if (key > node->key) {
            node->right = deleteNode(node->right, key);
        } else {
            // Trường hợp 1 & 2: Không con hoặc chỉ có 1 con
            if (!node->left) {
                Node* temp = node->right;
                delete node;
                return temp;
            } else if (!node->right) {
                Node* temp = node->left;
                delete node;
                return temp;
            }
            // Trường hợp 3: Có 2 con
            Node* temp = findMin(node->right); // Tìm nút nhỏ nhất bên phải
            node->key = temp->key;            // Ghi đè dữ liệu
            node->right = deleteNode(node->right, temp->key); // Xóa nút thế mạng
        }
        return node;
    }

    void clear(Node* node) {
        if (!node) return;
        clear(node->left);
        clear(node->right);
        delete node;
    }

public:
    BST() : root(nullptr) {}
    ~BST() { clear(root); }

    void insert(int key) { root = insert(root, key); }
    bool search(int key) { return search(root, key); }
    void remove(int key) { root = deleteNode(root, key); }
};
### Bài 2: Kiểm tra BST hợp lệ ⭐⭐
Cho một cây nhị phân bất kỳ — kiểm tra có phải BST không. Tìm phần tử lớn thứ K.
#include <iostream>
#include <climits>

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BSTChecker {
private:
    // Kiểm tra tính hợp lệ bằng khoảng giới hạn giá trị
    bool isValidBST(TreeNode* node, long long minVal, long long maxVal) {
        if (!node) return true;
        if (node->val <= minVal || node->val >= maxVal) return false;
        return isValidBST(node->left, minVal, node->val) && 
               isValidBST(node->right, node->val, maxVal);
    }

    // Duyệt Phải - Gốc - Trái để tìm phần tử lớn thứ K
    void findKthLargestUtil(TreeNode* root, int k, int& count, int& result) {
        if (!root || count >= k) return;

        findKthLargestUtil(root->right, k, count, result);

        count++;
        if (count == k) {
            result = root->val;
            return;
        }

        findKthLargestUtil(root->left, k, count, result);
    }

public:
    bool isValidBST(TreeNode* root) {
        return isValidBST(root, LLONG_MIN, LLONG_MAX);
    }

    int kthLargest(TreeNode* root, int k) {
        int count = 0;
        int result = -1;
        findKthLargestUtil(root, k, count, result);
        return result;
    }
};
### Bài 3: Cân bằng BST ⭐⭐⭐
Chuyển BST mất cân bằng thành BST cân bằng (AVL concept cơ bản — chỉ single rotation).
#include <iostream>
#include <algorithm>

struct AVLNode {
    int key;
    int height;
    AVLNode* left;
    AVLNode* right;
    AVLNode(int val) : key(val), height(1), left(nullptr), right(nullptr) {}
};

class AVLTree {
private:
    int height(AVLNode* n) { return n ? n->height : 0; }

    int getBalance(AVLNode* n) { return n ? height(n->left) - height(n->right) : 0; }

    // Xoay phải (Right Rotate) khi lệch Trái-Trái
    AVLNode* rightRotate(AVLNode* y) {
        AVLNode* x = y->left;
        AVLNode* T2 = x->right;

        x->right = y;
        y->left = T2;

        y->height = 1 + std::max(height(y->left), height(y->right));
        x->height = 1 + std::max(height(x->left), height(x->right));

        return x; // Gốc mới
    }

    // Xoay trái (Left Rotate) khi lệch Phải-Phải
    AVLNode* leftRotate(AVLNode* x) {
        AVLNode* y = x->right;
        AVLNode* T2 = y->left;

        y->left = x;
        x->right = T2;

        x->height = 1 + std::max(height(x->left), height(x->right));
        y->height = 1 + std::max(height(y->left), height(y->right));

        return y; // Gốc mới
    }

public:
    AVLNode* insert(AVLNode* node, int key) {
        if (!node) return new AVLNode(key);

        if (key < node->key) node->left = insert(node->left, key);
        else if (key > node->key) node->right = insert(node->right, key);
        else return node;

        node->height = 1 + std::max(height(node->left), height(node->right));
        int balance = getBalance(node);

        // Trường hợp lệch LL -> Xoay đơn Phải
        if (balance > 1 && key < node->left->key)
            return rightRotate(node);

        // Trường hợp lệch RR -> Xoay đơn Trái
        if (balance < -1 && key > node->right->key)
            return leftRotate(node);

        return node;
    }
};
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
#include <iostream>
#include <string>
#include <iomanip>
#include <algorithm>

struct WordNode {
    std::string word;
    std::string meaning;
    std::string example;
    std::string pronunciation;
    WordNode* left;
    WordNode* right;

    WordNode(std::string w, std::string m, std::string ex, std::string pro)
        : word(w), meaning(m), example(ex), pronunciation(pro), left(nullptr), right(nullptr) {}
};

class EnglishVietnameseDictionary {
private:
    WordNode* root;
    int totalWords;

    WordNode* insert(WordNode* node, std::string w, std::string m, std::string ex, std::string pro) {
        if (!node) {
            totalWords++;
            return new WordNode(w, m, ex, pro);
        }
        if (w < node->word) node->left = insert(node->left, w, m, ex, pro);
        else if (w > node->word) node->right = insert(node->right, w, m, ex, pro);
        else {
            // Nếu từ đã tồn tại, cập nhật lại nghĩa
            node->meaning = m;
            node->example = ex;
            node->pronunciation = pro;
        }
        return node;
    }

    void search(WordNode* node, std::string w, int steps) {
        if (!node) {
            std::cout << "❌ Khong tim thay tu '" << w << "' trong tu dien. (So sanh qua " << steps << " buoc)\n";
            return;
        }
        if (node->word == w) {
            std::cout << "✅ " << node->word << " = " << node->meaning << "\n";
            std::cout << "   Vi du: " << node->example << "\n";
            std::cout << "   Phat am: " << node->pronunciation << "\n";
            std::cout << "   (Tim thay sau " << steps + 1 << " buoc so sanh)\n";
            return;
        }
        if (w < node->word) search(node->left, w, steps + 1);
        else search(node->right, w, steps + 1);
    }

    void inOrder(WordNode* node) {
        if (!node) return;
        inOrder(node->left);
        std::cout << std::left << std::setw(15) << node->word 
                  << " -> " << node->meaning << " (" << node->pronunciation << ")\n";
        inOrder(node->right);
    }

    int getHeight(WordNode* node) {
        if (!node) return 0;
        return 1 + std::max(getHeight(node->left), getHeight(node->right));
    }

    void clear(WordNode* node) {
        if (!node) return;
        clear(node->left);
        clear(node->right);
        delete node;
    }

public:
    EnglishVietnameseDictionary() : root(nullptr), totalWords(0) {}
    ~EnglishWeeklyDictionary() { clear(root); }

    void addWord(std::string w, std::string m, std::string ex, std::string pro) {
        root = insert(root, w, m, ex, pro);
    }

    void lookup(std::string w) {
        search(root, w, 0);
    }

    void displayAll() {
        std::cout << "\n--- DUYET TU DIEN (A->Z) ---\n";
        if (totalWords == 0) std::cout << "(Tu dien dang trong)\n";
        else inOrder(root);
        std::cout << "----------------------------\n";
    }

    void showStatistics() {
        std::cout << "\n=== THONG KE TU DIEN ===\n";
        std::cout << "Tong so tu hien tai: " << totalWords << "\n";
        std::cout << "Chieu cao cay BST   : " << getHeight(root) << "\n";
        std::cout << "========================\n";
    }
};

int main() {
    EnglishVietnameseDictionary dict;

    // Du lieu mau ban dau
    dict.addWord("algorithm", "thuat toan", "\"Sorting algorithm\" = thuat toan sap xep", "/'aelgeridhem/");
    dict.addWord("binary", "nhi phan", "\"Binary tree\" = cay nhi phan", "/'baineri/");
    dict.addWord("array", "mang", "\"One-dimensional array\" = mang mot chieu", "/e'rei/");
    dict.addWord("data", "du lieu", "\"Database\" = co so du lieu", "/'deite/");

    int choice;
    std::string w, m, ex, pro;

    do {
        std::cout << "\n=== 1.📖 TU DIEN ANH-VIET (BST) ===\n";
        std::cout << "1. Them tu moi\n";
        std::cout << "2. Tra tu\n";
        std::cout << "3. Duyet tu dien (A->Z)\n";
        std::cout << "4. Thong ke he thong\n";
        std::cout << "5. Thoat\n";
        std::cout << "Nhap lua chon cua ban: ";
        std::cin >> choice;
        std::cin.ignore(); // Xoa bo nho dem chuoi con lai

        switch (choice) {
            case 1:
                std::cout << "Nhap tu tieng Anh: "; std::getline(std::cin, w);
                std::cout << "Nhap nghia tieng Viet: "; std::getline(std::cin, m);
                std::cout << "Nhap vi du su dung: "; std::getline(std::cin, ex);
                std::cout << "Nhap phien am quoc te: "; std::getline(std::cin, pro);
                dict.addWord(w, m, ex, pro);
                std::cout << "-> Them tu moi thanh cong!\n";
                break;
            case 2:
                std::cout << "Nhap tu can tra: "; std::getline(std::cin, w);
                dict.lookup(w);
                break;
            case 3:
                dict.displayAll();
                break;
            case 4:
                dict.showStatistics();
                break;
            case 5:
                std::cout << "Tam biet!\n";
                break;
            default:
                std::cout << "Lua chon sai! Vui long chon lai.\n";
        }
    } while (choice != 5);

    return 0;
}
---
📁 Tham khảo: `Chuong4_CauTrucCay/Chuong4_CauTrucCay.cpp`
