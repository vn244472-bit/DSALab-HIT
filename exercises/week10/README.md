# Tuần 10: Cây Nhị Phân — Bài tập

## 🎯 Mục tiêu tuần này
Hiểu cây nhị phân, cài đặt duyệt cây (Pre/In/Post-order và BFS Level-order).

---

### Bài 1: Xây dựng cây nhị phân ⭐⭐
Xây dựng cây từ mảng (biểu diễn heap). Tính chiều cao, đếm nút lá, nút trong.
#include <iostream>
#include <vector>
#include <algorithm>

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BinaryTree {
public:
    TreeNode* root;

    BinaryTree() : root(nullptr) {}

    // Hàm xóa cây để tránh rò rỉ bộ nhớ
    void clear(TreeNode* node) {
        if (!node) return;
        clear(node->left);
        clear(node->right);
        delete node;
    }

    ~BinaryTree() { clear(root); }

    // Xây dựng cây từ mảng theo dạng cấp độ (Level-order / Heap-like)
    TreeNode* buildFromLevelOrder(const std::vector<int>& arr, int i) {
        if (i >= arr.size() || arr[i] == -1) return nullptr; // Quy ước -1 là node trống

        TreeNode* temp = new TreeNode(arr[i]);
        temp->left = buildFromLevelOrder(arr, 2 * i + 1);
        temp->right = buildFromLevelOrder(arr, 2 * i + 2);
        return temp;
    }

    // 1. Tính chiều cao của cây (gốc là 1 hoặc độ sâu tối đa)
    int getHeight(TreeNode* node) {
        if (!node) return 0;
        return 1 + std::max(getHeight(node->left), getHeight(node->right));
    }

    // 2. Đếm số nút lá (nút không có con)
    int countLeaves(TreeNode* node) {
        if (!node) return 0;
        if (!node->left && !node->right) return 1;
        return countLeaves(node->left) + countLeaves(node->right);
    }

    // 3. Đếm số nút trong (Internal Nodes - nút có ít nhất một con)
    int countInternalNodes(TreeNode* node) {
        if (!node || (!node->left && !node->right)) return 0;
        return 1 + countInternalNodes(node->left) + countInternalNodes(node->right);
    }
};
### Bài 2: Duyệt cây đủ 4 cách ⭐⭐
Cài đặt Pre-order, In-order, Post-order (đệ quy + iterative), Level-order (BFS bằng Queue).
#include <iostream>
#include <vector>
#include <stack>
#include <queue>

class TreeTraversal {
public:
    // 1. PRE-ORDER (Gốc -> Trái -> Phải)
    void preOrderRecursive(TreeNode* root) {
        if (!root) return;
        std::cout << root->val << " ";
        preOrderRecursive(root->left);
        preOrderRecursive(root->right);
    }

    void preOrderIterative(TreeNode* root) {
        if (!root) return;
        std::stack<TreeNode*> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode* curr = s.top(); s.pop();
            std::cout << curr->val << " ";
            if (curr->right) s.push(curr->right); // Đẩy Phải vào trước để Trái xử lý trước
            if (curr->left) s.push(curr->left);
        }
    }

    // 2. IN-ORDER (Trái -> Gốc -> Phải)
    void inOrderRecursive(TreeNode* root) {
        if (!root) return;
        inOrderRecursive(root->left);
        std::cout << root->val << " ";
        inOrderRecursive(root->right);
    }

    void inOrderIterative(TreeNode* root) {
        std::stack<TreeNode*> s;
        TreeNode* curr = root;
        while (curr || !s.empty()) {
            while (curr) {
                s.push(curr);
                curr = curr->left;
            }
            curr = s.top(); s.pop();
            std::cout << curr->val << " ";
            curr = curr->right;
        }
    }

    // 3. POST-ORDER (Trái -> Phải -> Gốc)
    void postOrderRecursive(TreeNode* root) {
        if (!root) return;
        postOrderRecursive(root->left);
        postOrderRecursive(root->right);
        std::cout << root->val << " ";
    }

    void postOrderIterative(TreeNode* root) {
        if (!root) return;
        std::stack<TreeNode*> s1, s2;
        s1.push(root);
        while (!s1.empty()) {
            TreeNode* curr = s1.top(); s1.pop();
            s2.push(curr);
            if (curr->left) s1.push(curr->left);
            if (curr->right) s1.push(curr->right);
        }
        while (!s2.empty()) {
            std::cout << s2.top()->val << " ";
            s2.pop();
        }
    }

    // 4. LEVEL-ORDER (Duyệt theo tầng - BFS dùng Queue)
    void levelOrder(TreeNode* root) {
        if (!root) return;
        std::queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            TreeNode* curr = q.front(); q.pop();
            std::cout << curr->val << " ";
            if (curr->left) q.push(curr->left);
            if (curr->right) q.push(curr->right);
        }
    }
};
### Bài 3: Tính chất cây ⭐⭐⭐
Kiểm tra cây cân bằng (Balanced), cây đối xứng (Symmetric), tìm Lowest Common Ancestor.
#include <iostream>
#include <cmath>
#include <algorithm>

class TreeProperties {
private:
    int checkHeight(TreeNode* root) {
        if (!root) return 0;
        int leftH = checkHeight(root->left);
        if (leftH == -1) return -1;
        int rightH = checkHeight(root->right);
        if (rightH == -1) return -1;

        if (std::abs(leftH - rightH) > 1) return -1;
        return 1 + std::max(leftH, rightH);
    }

    bool isMirror(TreeNode* t1, TreeNode* t2) {
        if (!t1 && !t2) return true;
        if (!t1 || !t2) return false;
        return (t1->val == t2->val) 
            && isMirror(t1->left, t2->right) 
            && isMirror(t1->right, t2->left);
    }

public:
    // 1. Kiểm tra cây cân bằng (chiều cao con trái và phải không lệch quá 1)
    bool isBalanced(TreeNode* root) {
        return checkHeight(root) != -1;
    }

    // 2. Kiểm tra cây đối xứng qua trục giữa
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        return isMirror(root->left, root->right);
    }

    // 3. Tìm tổ tiên chung thấp nhất (LCA) của hai nút p và q
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;

        TreeNode* leftLCA = lowestCommonAncestor(root->left, p, q);
        TreeNode* rightLCA = lowestCommonAncestor(root->right, p, q);

        if (leftLCA && rightLCA) return root; // Nút hiện tại chính là LCA
        return leftLCA ? leftLCA : rightLCA;
    }
};
### Bài 4: 🔥 Dự Án Mini — BST Visualizer (Console) ⭐⭐⭐
> **Cảm hứng:** [algorithm-visualizer.org — BST Visualization](https://algorithm-visualizer.org)

Hiển thị cây nhị phân trực quan trong terminal:
```
=== 🌳 BINARY TREE VISUALIZER ===
Nhập: insert 50 30 70 20 40 60 80

        50
       /   \
     30     70
    / \    / \
  20  40  60  80

Duyệt In-order:  20 → 30 → 40 → 50 → 60 → 70 → 80
Duyệt Pre-order: 50 → 30 → 20 → 40 → 70 → 60 → 80
Chiều cao: 3 | Số nút: 7 | Cân bằng: ✅
```
**Yêu cầu:** in cây dạng ASCII đẹp, hiển thị kết quả 4 loại duyệt, tính các thông số cây.
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cmath>

struct BSTNode {
    int val;
    BSTNode* left;
    BSTNode* right;
    BSTNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BSTVisualizer {
private:
    BSTNode* root;

    BSTNode* insert(BSTNode* node, int val) {
        if (!node) return new BSTNode(val);
        if (val < node->val) node->left = insert(node->left, val);
        else if (val > node->val) node->right = insert(node->right, val);
        return node;
    }

    int getHeight(BSTNode* node) {
        if (!node) return 0;
        return 1 + std::max(getHeight(node->left), getHeight(node->right));
    }

    int countNodes(BSTNode* node) {
        if (!node) return 0;
        return 1 + countNodes(node->left) + countNodes(node->right);
    }

    int checkHeight(BSTNode* node) {
        if (!node) return 0;
        int lh = checkHeight(node->left);
        if (lh == -1) return -1;
        int rh = checkHeight(node->right);
        if (rh == -1) return -1;
        if (std::abs(lh - rh) > 1) return -1;
        return 1 + std::max(lh, rh);
    }

    void inOrder(BSTNode* node) {
        if (!node) return;
        inOrder(node->left);
        std::cout << node->val << " -> ";
    }

    void preOrder(BSTNode* node) {
        if (!node) return;
        std::cout << node->val << " -> ";
        preOrder(node->left);
        preOrder(node->right);
    }

    // Hàm phụ trợ hỗ trợ vẽ nhánh ASCII chuẩn vị trí
    void printLevel(std::vector<BSTNode*> nodes, int level, int maxLevel) {
        if (nodes.empty() || isAllNull(nodes)) return;

        int floor = maxLevel - level;
        int endgeLines = std::pow(2, std::max(floor - 1, 0));
        int firstSpaces = std::pow(2, floor) - 1;
        int betweenSpaces = std::pow(2, floor + 1) - 1;

        printSpaces(firstSpaces);

        std::vector<BSTNode*> newNodes;
        for (BSTNode* node : nodes) {
            if (node) {
                std::cout << node->val;
                newNodes.push_back(node->left);
                newNodes.push_back(node->right);
            } else {
                std::cout << "  ";
                newNodes.push_back(nullptr);
                newNodes.push_back(nullptr);
            }
            printSpaces(betweenSpaces);
        }
        std::cout << "\n";

        for (int i = 1; i <= endgeLines; i++) {
            for (size_t j = 0; j < nodes.size(); j++) {
                printSpaces(firstSpaces - i);
                if (nodes[j] == nullptr) {
                    printSpaces(endgeLines + endgeLines + i + 1);
                    continue;
                }

                if (nodes[j]->left) std::cout << "/";
                else printSpaces(1);

                printSpaces(i + i - 1);

                if (nodes[j]->right) std::cout << "\\";
                else printSpaces(1);

                printSpaces(firstSpaces + endgeLines - i);
            }
            std::cout << "\n";
        }

        printLevel(newNodes, level + 1, maxLevel);
    }

    void printSpaces(int count) {
        for (int i = 0; i < count; i++) std::cout << " ";
    }

    bool isAllNull(const std::vector<BSTNode*>& nodes) {
        for (BSTNode* node : nodes) {
            if (node != nullptr) return false;
        }
        return true;
    }

    void clear(BSTNode* node) {
        if (!node) return;
        clear(node->left);
        clear(node->right);
        delete node;
    }

public:
    BSTVisualizer() : root(nullptr) {}
    ~BSTVisualizer() { clear(root); }

    void insertValue(int val) {
        root = insert(root, val);
    }

    void showStatus() {
        std::cout << "=== 🌳 BINARY TREE VISUALIZER ===\n\n";
        if (!root) {
            std::cout << "(Cay dang rong!)\n";
            return;
        }
        
        // Vẽ cấu trúc ASCII của cây
        std::vector<BSTNode*> list;
        list.push_back(root);
        printLevel(list, 1, getHeight(root));

        std::cout << "\n----------------------------------------\n";
        std::cout << "Duyet In-order:  ";
        inOrder(root); std::cout << "END\n";

        std::cout << "Duyet Pre-order: ";
        preOrder(root); std::cout << "END\n";

        int h = getHeight(root);
        int total = countNodes(root);
        bool balanced = (checkHeight(root) != -1);

        std::cout << "Chieu cao: " << h 
                  << " | So nut: " << total 
                  << " | Can bang: " << (balanced ? "✅" : "❌") << "\n";
        std::cout << "=================================\n";
    }
};

int main() {
    BSTVisualizer tree;
    
    // Giả lập tập lệnh input như yêu cầu của đề bài: insert 50 30 70 20 40 60 80
    std::vector<int> inputs = {50, 30, 70, 20, 40, 60, 80};
    
    for (int val : inputs) {
        tree.insertValue(val);
    }

    // In toàn bộ cây trực quan kèm các thông số
    tree.showStatus();

    return 0;
}
---
📁 Tham khảo: `Chuong4_CauTrucCay/Chuong4_CauTrucCay.cpp`
