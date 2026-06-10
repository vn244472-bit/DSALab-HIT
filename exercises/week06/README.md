# Tuần 6: Danh Sách Liên Kết Đơn — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Singly Linked List, thành thạo con trỏ, thêm/xóa/duyệt.

---

### Bài 1: Cài đặt Linked List ⭐⭐
Cài đặt đầy đủ: thêm đầu, thêm cuối, xóa đầu, xóa cuối, xóa theo giá trị, tìm kiếm, in danh sách.
#include <iostream>

using std::cout;
using std::endl;

// Định nghĩa cấu trúc Node
struct Node {
    int data;
    Node* next;

    // Constructor giúp tạo Node nhanh chóng
    Node(int value) : data(value), next(nullptr) {}
};

// Lớp quản lý Danh sách liên kết
class LinkedList {
private:
    Node* head;
    Node* tail;

public:
    // Constructor khởi tạo danh sách rỗng
    LinkedList() : head(nullptr), tail(nullptr) {}

    // Destructor tự động giải phóng bộ nhớ khi đối tượng bị hủy
    ~LinkedList() {
        clear();
    }

    // 1. Thêm vào đầu danh sách
    void insertHead(int value) {
        Node* newNode = new Node(value);
        if (head == nullptr) {
            head = tail = newNode;
        } else {
            newNode->next = head;
            head = newNode;
        }
    }

    // 2. Thêm vào cuối danh sách
    void insertTail(int value) {
        Node* newNode = new Node(value);
        if (head == nullptr) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            tail = newNode;
        }
    }

    // 3. Xóa Node đầu tiên
    void deleteHead() {
        if (head == nullptr) {
            cout << "Danh sách rỗng, không thể xóa!\n";
            return;
        }
        Node* temp = head;
        head = head->next;
        if (head == nullptr) {
            tail = nullptr;
        }
        delete temp; // Trong C++ dùng delete thay cho free
    }

    // 4. Xóa Node cuối cùng
    void deleteTail() {
        if (head == nullptr) {
            cout << "Danh sách rỗng, không thể xóa!\n";
            return;
        }
        if (head == tail) {
            delete head;
            head = tail = nullptr;
            return;
        }
        Node* temp = head;
        while (temp->next != tail) {
            temp = temp->next;
        }
        delete tail;
        tail = temp;
        tail->next = nullptr;
    }

    // 5. Xóa Node theo giá trị xuất hiện đầu tiên
    void deleteValue(int value) {
        if (head == nullptr) return;

        if (head->data == value) {
            deleteHead();
            return;
        }

        Node* curr = head;
        Node* prev = nullptr;

        while (curr != nullptr && curr->data != value) {
            prev = curr;
            curr = curr->next;
        }

        if (curr == nullptr) {
            cout << "Không tìm thấy giá trị " << value << " để xóa.\n";
            return;
        }

        prev->next = curr->next;
        if (curr == tail) {
            tail = prev;
        }
        delete curr;
    }

    // 6. Tìm kiếm một giá trị
    Node* search(int value) const {
        Node* temp = head;
        while (temp != nullptr) {
            if (temp->data == value) return temp;
            temp = temp->next;
        }
        return nullptr;
    }

    // 7. Duyệt và in danh sách
    void printList() const {
        Node* temp = head;
        if (temp == nullptr) {
            cout << "Danh sách rỗng.\n";
            return;
        }
        while (temp != nullptr) {
            cout << temp->data << " -> ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }

    // Hàm giải phóng sạch bộ nhớ
    void clear() {
        while (head != nullptr) {
            deleteHead();
        }
    }

    // Cấp quyền cho các hàm bên ngoài hoặc bài tập sau truy cập private nếu cần
    friend class Solution; 
};

### Bài 2: Đảo ngược danh sách ⭐⭐
Đảo ngược Linked List bằng 2 cách: iterative (3 con trỏ) và recursive. So sánh.
void reverseIterative() {
    Node* prev = nullptr;
    Node* curr = head;
    Node* next = nullptr;

    tail = head; // Đổi tail thành head cũ

    while (curr != nullptr) {
        next = curr->next;
        curr->next = prev; // Bẻ hướng con trỏ
        prev = curr;
        curr = next;
    }
    head = prev; // Cập nhật head mới
}
### Bài 3: Phát hiện vòng lặp ⭐⭐⭐
Cài đặt Floyd's Cycle Detection (slow/fast pointer). Tìm điểm bắt đầu vòng lặp.
Node* detectAndGetCycleStart() {
    Node* slow = head;
    Node* fast = head;
    bool hasCycle = false;

    // Bước 1: Phát hiện xem có vòng lặp (cycle) không
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            hasCycle = true;
            break;
        }
    }

    if (!hasCycle) return nullptr;

    // Bước 2: Tìm điểm bắt đầu của vòng lặp
    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    return slow; // Trả về node bắt đầu vòng lặp
}

### Bài 4: 🔥 Dự Án Mini — Lịch Sử Trình Duyệt ⭐⭐⭐
> **Cảm hứng:** BaiTapTongHop — Lịch sử trình duyệt (DSALab)

Mô phỏng lịch sử duyệt web bằng Singly Linked List:
```
=== TRÌNH DUYỆT WEB (Linked List) ===
> visit google.com
> visit facebook.com  
> visit youtube.com
> back
← Quay lại: facebook.com
> back
← Quay lại: google.com
> forward
→ Tiến tới: facebook.com
> history
📋 Lịch sử: google.com → facebook.com → youtube.com
       #include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::string;

struct WebNode {
    string url;
    WebNode* next;
    WebNode(string u) : url(u), next(nullptr) {}
};

class BrowserHistory {
private:
    WebNode* head;
    WebNode* current;
    int currentSize;
    const int MAX_HISTORY = 50;

    // Xóa tất cả các trang forward phía sau nút current
    void clearForwardHistory() {
        if (current == nullptr) return;
        Node* curr = current->next;
        current->next = nullptr;
        while (curr != nullptr) {
            Node* temp = curr;
            curr = curr->next;
            delete temp;
            currentSize--;
        }
    }

    // Xóa trang cũ nhất ở đầu danh sách để duy trì bộ nhớ <= 50
    void removeOldestHistory() {
        if (head == nullptr) return;
        WebNode* temp = head;
        head = head->next;
        delete temp;
        currentSize--;
    }

public:
    BrowserHistory() : head(nullptr), current(nullptr), currentSize(0) {}

    ~BrowserHistory() {
        clearHistory();
    }

    // 1. Visit một trang mới
    void visit(string url) {
        clearForwardHistory();

        if (currentSize >= MAX_HISTORY) {
            removeOldestHistory();
        }

        WebNode* newNode = new WebNode(url);
        if (head == nullptr) {
            head = newNode;
            current = newNode;
        } else {
            current->next = newNode;
            current = newNode;
        }
        currentSize++;
        cout << "> visit " << current->url << "\n";
    }

    // 2. Quay lại (Back)
    void back() {
        if (head == nullptr || current == head) {
            cout << "← Không thể back (Đang ở đầu lịch sử).\n";
            return;
        }
        WebNode* temp = head;
        while (temp->next != current) {
            temp = temp->next;
        }
        current = temp;
        cout << "← Quay lại: " << current->url << "\n";
    }

    // 3. Tiến tới (Forward)
    void forward() {
        if (current == nullptr || current->next == nullptr) {
            cout << "→ Không thể forward (Đang ở trang mới nhất).\n";
            return;
        }
        current = current->next;
        cout << "→ Tiến tới: " << current->url << "\n";
    }

    // 4. Xem lịch sử (History)
    void showHistory() const {
        if (head == nullptr) {
            cout << "📋 Lịch sử trống.\n";
            return;
        }

        cout << "📋 Lịch sử: ";
        WebNode* temp = head;
        while (temp != nullptr) {
            cout << temp->url;
            if (temp->next != nullptr) cout << " → ";
            temp = temp->next;
        }
        cout << "\n";

        // In mũi tên chỉ vị trí hiện tại
        temp = head;
        cout << "            "; // Khớp với chữ "📋 Lịch sử: "
        while (temp != current) {
            int len = temp->url.length() + 3; // Độ dài chữ + kị tự " → "
            for (int i = 0; i < len; i++) cout << " ";
            temp = temp->next;
        }
        cout << "↑ (đang ở đây)\n";
    }

    // 5. Xóa sạch lịch sử
    void clearHistory() {
        WebNode* curr = head;
        while (curr != nullptr) {
            WebNode* temp = curr;
            curr = curr->next;
            delete temp;
        }
        head = nullptr;
        current = nullptr;
        currentSize = 0;
    }
};

int main() {
    // Để hiển thị các icon emoji trên console mượt mà hơn (đặc biệt là trên Windows)
    std::ios_base::sync_with_stdio(false);
    
    BrowserHistory browser;
    
    cout << "=== TRÌNH DUYỆT WEB (Linked List - C++) ===\n";
    browser.visit("google.com");
    browser.visit("facebook.com");
    browser.visit("youtube.com");
    
    browser.showHistory();

    browser.back();
    browser.back();
    browser.forward();
    
    browser.visit("github.com");
    browser.showHistory();

    return 0;
}                         ↑ (đang ở đây)
```
**Yêu cầu:** hỗ trợ visit, back, forward, history, clear, tối đa 50 trang trong lịch sử.

---
📁 Tham khảo: `Chuong3_DanhSachLienKet/Chuong3_DanhSachLienKet.cpp`
