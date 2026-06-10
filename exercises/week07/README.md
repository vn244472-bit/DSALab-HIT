# Tuần 7: Danh Sách Liên Kết Đôi & Vòng — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Doubly Linked List và Circular Linked List.

---

### Bài 1: Doubly Linked List ⭐⭐
Cài đặt đầy đủ với con trỏ prev và next. Duyệt xuôi và ngược. So sánh với Singly.
#include <iostream>

using std::cout;
using std::endl;

struct DNode {
    int data;
    DNode* prev;
    DNode* next;
    DNode(int val) : data(val), prev(nullptr), next(nullptr) {}
};

class DoublyLinkedList {
private:
    DNode* head;
    DNode* tail;

public:
    DoublyLinkedList() : head(nullptr), tail(nullptr) {}
    
    ~DoublyLinkedList() {
        clear();
    }

    void insertHead(int value) {
        DNode* newNode = new DNode(value);
        if (head == nullptr) {
            head = tail = newNode;
        } else {
            newNode->next = head;
            head->prev = newNode;
            head = newNode;
        }
    }

    void insertTail(int value) {
        DNode* newNode = new DNode(value);
        if (tail == nullptr) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
    }

    // Duyệt xuôi từ Head -> Tail
    void printForward() const {
        DNode* temp = head;
        cout << "Duyệt xuôi: NULL <- ";
        while (temp != nullptr) {
            cout << temp->data;
            if (temp->next != nullptr) cout << " <-> ";
            temp = temp->next;
        }
        cout << " -> NULL\n";
    }

    // Duyệt ngược từ Tail -> Head
    void printBackward() const {
        DNode* temp = tail;
        cout << "Duyệt ngược: NULL <- ";
        while (temp != nullptr) {
            cout << temp->data;
            if (temp->prev != nullptr) cout << " <-> ";
            temp = temp->prev;
        }
        cout << " -> NULL\n";
    }

    void clear() {
        DNode* curr = head;
        while (curr != nullptr) {
            DNode* temp = curr;
            curr = curr->next;
            delete temp;
        }
        head = tail = nullptr;
    }
};
### Bài 2: Circular Linked List ⭐⭐
Cài đặt danh sách liên kết vòng. Duyệt đúng K vòng rồi dừng.
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class CircularLinkedList {
private:
    Node* tail; // Chỉ cần giữ tail, head = tail->next

public:
    CircularLinkedList() : tail(nullptr) {}

    ~CircularLinkedList() {
        if (tail == nullptr) return;
        Node* curr = tail->next;
        tail->next = nullptr; // Bẻ vòng lặp thành danh sách tuyến tính để xóa
        while (curr != nullptr) {
            Node* temp = curr;
            curr = curr->next;
            delete temp;
        }
    }

    void insert(int value) {
        Node* newNode = new Node(value);
        if (tail == nullptr) {
            tail = newNode;
            tail->next = tail; // Tự vòng lại chính nó
        } else {
            newNode->next = tail->next; // newNode->next = head
            tail->next = newNode;       // Chèn nối tiếp sau tail
            tail = newNode;             // Cập nhật tail mới
        }
    }

    // Duyệt chính xác K vòng rồi dừng
    void printKVongs(int k) const {
        if (tail == nullptr || k <= 0) return;
        
        Node* curr = tail->next; // Bắt đầu từ head
        int vongDaDuyet = 0;
        
        cout << "Duyệt " << k << " vòng: ";
        while (vongDaDuyet < k) {
            cout << curr->data << " -> ";
            
            // Nếu chuẩn bị quay lại head, tức là đã xong 1 vòng
            if (curr == tail) {
                vongDaDuyet++;
                cout << "[Hết vòng " << vongDaDuyet << "] ";
            }
            curr = curr->next;
        }
        cout << "Dừng!\n";
    }
};
### Bài 3: Bài toán Josephus ⭐⭐⭐
Dùng Circular Linked List giải bài toán Josephus: n người đứng thành vòng, đếm đến k thì loại. Ai là người cuối?

int solveJosephus(int n, int k) {
    if (n <= 0 || k <= 0) return -1;

    // 1. Khởi tạo vòng tròn n người
    Node* head = new Node(1);
    Node* curr = head;
    for (int i = 2; i <= n; ++i) {
        curr->next = new Node(i);
        curr = curr->next;
    }
    curr->next = head; // Kết nối tạo vòng tròn

    // 2. Tiến hành loại bỏ
    Node* prev = curr; // prev đứng sau curr
    curr = head;

    while (curr->next != curr) { // Khi vòng tròn còn nhiều hơn 1 người
        // Đếm k-1 bước (để dịch đến đúng người thứ k)
        for (int i = 1; i < k; ++i) {
            prev = curr;
            curr = curr->next;
        }
        
        // Loại bỏ người thứ k khỏi vòng
        prev->next = curr->next;
        cout << "Loại người số: " << curr->data << "\n";
        delete curr;
        
        // Cập nhật vị trí bắt đầu đếm mới
        curr = prev->next;
    }

    int survivor = curr->data;
    delete curr; // Dọn dẹp node cuối cùng
    return survivor;
}
### Bài 4: 🔥 Dự Án Mini — Music Playlist Player ⭐⭐⭐
> **Cảm hứng:** Circular Linked List + Doubly Linked List
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <random>

using std::string;
using std::cout;
using std::cin;

struct SongNode {
    string title;
    string artist;
    string duration;
    SongNode* prev;
    SongNode* next;
    SongNode(string t, string a, string d) 
        : title(t), artist(a), duration(d), prev(nullptr), next(nullptr) {}
};

class MusicPlayer {
private:
    SongNode* head;
    SongNode* current;
    int size;

public:
    MusicPlayer() : head(nullptr), current(nullptr), size(0) {}

    ~MusicPlayer() {
        if (!head) return;
        SongNode* curr = head;
        for (int i = 0; i < size; ++i) {
            SongNode* temp = curr;
            curr = curr->next;
            delete temp;
        }
    }

    void addSong(string title, string artist, string duration) {
        SongNode* newNode = new SongNode(title, artist, duration);
        if (!head) {
            head = newNode;
            head->next = head;
            head->prev = head;
            current = head;
        } else {
            SongNode* tail = head->prev;
            tail->next = newNode;
            newNode->prev = tail;
            newNode->next = head;
            head->prev = newNode;
        }
        size++;
    }

    void deleteCurrentSong() {
        if (!head) return;
        
        SongNode* toDelete = current;
        cout << "❌ Đã xóa bài hát: " << toDelete->title << "\n";

        if (size == 1) {
            head = current = nullptr;
        } else {
            SongNode* prevNode = toDelete->prev;
            SongNode* nextNode = toDelete->next;

            prevNode->next = nextNode;
            nextNode->prev = prevNode;

            if (toDelete == head) {
                head = nextNode;
            }
            current = nextNode; // Tự động chuyển sang bài tiếp theo
        }
        delete toDelete;
        size--;
    }

    void nextSong() {
        if (current) current = current->next;
    }

    void prevSong() {
        if (current) current = current->prev;
    }

    void shufflePlaylist() {
        if (size <= 1) return;
        
        // Thu thập dữ liệu vào vector để trộn
        std::vector<SongNode*> nodes;
        SongNode* temp = head;
        for (int i = 0; i < size; ++i) {
            nodes.push_back(temp);
            temp = temp->next;
        }

        // Tạo bộ sinh số ngẫu nhiên hiện đại của C++
        std::random_device rd;
        std::mt19937 g(rd());
        std::shuffle(nodes.begin(), nodes.end(), g);

        // Liên kết lại vòng tròn kép theo thứ tự mới
        head = nodes[0];
        for (int i = 0; i < size; ++i) {
            nodes[i]->next = nodes[(i + 1) % size];
            nodes[(i + 1) % size]->prev = nodes[i];
        }
        current = head;
        cout << "🎲 Đã trộn ngẫu nhiên danh sách phát!\n";
    }

    void displayUI() const {
        if (!head) {
            cout << "🎵 PLAYLIST TRỐNG. Vui lòng thêm bài hát!\n";
            return;
        }

        // Xác định số thứ tự của bài đang phát
        int currentIdx = 1;
        SongNode* temp = head;
        while (temp != current) {
            currentIdx++;
            temp = temp->next;
        }

        cout << "\n=== 🎵 MUSIC PLAYER ===\n";
        cout << "▶ Đang phát: [" << currentIdx << "/" << size << "] " 
             << current->title << " — " << current->artist << " (" << current->duration << ")\n";
        cout << "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 0:00 / " << current->duration << "\n\n";

        cout << "Playlist:\n";
        temp = head;
        for (int i = 1; i <= size; ++i) {
            if (temp == current) {
                cout << " ► " << i << ". " << temp->title << "        ← đang phát\n";
            } else {
                cout << "   " << i << ". " << temp->title << "\n";
            }
            temp = temp->next;
        }
        cout << "\n[N] Tiếp  [B] Trước  [S] Shuffle  [A] Thêm  [D] Xóa  [Q] Thoát\n";
    }
};

int main() {
    MusicPlayer player;
    player.addSong("Blinding Lights", "The Weeknd", "3:20");
    player.addSong("Stay", "The Kid LAROI & Justin Bieber", "2:21");
    player.addSong("Shape of You", "Ed Sheeran", "3:54");
    player.addSong("Levitating", "Dua Lipa", "3:23");
    player.addSong("Montero", "Lil Nas X", "2:18");

    char choice;
    do {
        player.displayUI();
        cout << "Nhập lựa chọn của bạn: ";
        cin >> choice;
        choice = toupper(choice);

        switch (choice) {
            case 'N': player.nextSong(); break;
            case 'B': player.prevSong(); break;
            case 'S': player.shufflePlaylist(); break;
            case 'D': player.deleteCurrentSong(); break;
            case 'A': {
                string t, a, d;
                cout << "Nhập tên bài hát: "; cin.ignore(); getline(cin, t);
                cout << "Nhập ca sĩ: "; getline(cin, a);
                cout << "Nhập thời lượng (mm:ss): "; cin >> d;
                player.addSong(t, a, d);
                break;
            }
        }
    } while (choice != 'Q');

    return 0;
}
Xây dựng trình phát nhạc playlist bằng Doubly Circular Linked List:
```
=== 🎵 MUSIC PLAYER ===
▶ Đang phát: [3/5] Shape of You — Ed Sheeran (3:54)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1:23 / 3:54

Playlist:
  1. Blinding Lights
  2. Stay
► 3. Shape of You        ← đang phát
  4. Levitating
  5. Montero

[P] Phát/Dừng  [N] Tiếp  [B] Trước  [S] Shuffle  [L] Loop  [A] Thêm  [D] Xóa
```

---
📁 Tham khảo: `Chuong3_DanhSachLienKet/Chuong3_DanhSachLienKet.cpp`
