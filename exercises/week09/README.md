# Tuần 9: Queue & Ứng Dụng — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Queue và Priority Queue. Ứng dụng trong bài toán hàng chờ thực tế.

---

### Bài 1: Cài đặt Queue ⭐⭐
Cài đặt Queue bằng mảng vòng (circular array) và Linked List. Hỗ trợ enqueue, dequeue, peek, is_full, is_empty.
#include <iostream>

class CircularQueue {
private:
    static const int MAX = 5;
    int data[MAX];
    int front;
    int rear;
    int current_size;

public:
    CircularQueue() {
        front = 0;
        rear = -1;
        current_size = 0;
    }

    bool is_empty() const {
        return current_size == 0;
    }

    bool is_full() const {
        return current_size == MAX;
    }

    void enqueue(int value) {
        if (is_full()) {
            std::cout << "Queue da day! Khong the them " << value << "\n";
            return;
        }
        rear = (rear + 1) % MAX;
        data[rear] = value;
        current_size++;
        std::cout << "Da them " << value << " vao Queue\n";
    }

    int dequeue() {
        if (is_empty()) {
            std::cout << "Queue rong! Khong the lay phan tu.\n";
            return -1;
        }
        int value = data[front];
        front = (front + 1) % MAX;
        current_size--;
        return value;
    }

    int peek() const {
        if (is_empty()) return -1;
        return data[front];
    }
};
----------------------------------------------------
#include <iostream>

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LLQueue {
private:
    Node* front;
    Node* rear;

public:
    LLQueue() : front(nullptr), rear(nullptr) {}

    ~LLQueue() {
        while (!is_empty()) {
            dequeue();
        }
    }

    bool is_empty() const {
        return front == nullptr;
    }

    void enqueue(int value) {
        Node* newNode = new Node(value);
        if (is_empty()) {
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
        std::cout << "Da them " << value << " vao Linked List Queue\n";
    }

    int dequeue() {
        if (is_empty()) return -1;
        Node* temp = front;
        int value = temp->data;
        front = front->next;
        if (front == nullptr) rear = nullptr;
        delete temp;
        return value;
    }

    int peek() const {
        if (is_empty()) return -1;
        return front->data;
    }
};
### Bài 2: Deque (Double-ended Queue) ⭐⭐
Cài đặt Deque — thêm/xóa cả 2 đầu. Ứng dụng: kiểm tra palindrome.
#include <iostream>
#include <string>

class Deque {
private:
    static const int MAX_DEQUE = 100;
    char data[MAX_DEQUE];
    int front;
    int rear;
    int current_size;

public:
    Deque() {
        front = 0;
        rear = -1;
        current_size = 0;
    }

    bool is_empty() const { return current_size == 0; }
    bool is_full() const { return current_size == MAX_DEQUE; }
    int size() const { return current_size; }

    void insertFront(char c) {
        if (is_full()) return;
        front = (front - 1 + MAX_DEQUE) % MAX_DEQUE;
        data[front] = c;
        current_size++;
    }

    void insertRear(char c) {
        if (is_full()) return;
        rear = (rear + 1) % MAX_DEQUE;
        data[rear] = c;
        current_size++;
    }

    char deleteFront() {
        if (is_empty()) return '\0';
        char c = data[front];
        front = (front + 1) % MAX_DEQUE;
        current_size--;
        return c;
    }

    char deleteRear() {
        if (is_empty()) return '\0';
        char c = data[rear];
        rear = (rear - 1 + MAX_DEQUE) % MAX_DEQUE;
        current_size--;
        return c;
    }
};

bool checkPalindrome(const std::string& str) {
    Deque dq;
    for (char c : str) {
        dq.insertRear(c);
    }
    while (dq.size() > 1) {
        if (dq.deleteFront() != dq.deleteRear()) {
            return false;
        }
    }
    return true;
}
### Bài 3: Priority Queue ⭐⭐⭐
Cài đặt Priority Queue bằng mảng. Phần tử ưu tiên cao nhất được lấy ra trước. Ứng dụng: hàng chờ bệnh viện có phân cấp ưu tiên.
#include <iostream>

struct Element {
    int value;
    int priority;
};

class PriorityQueue {
private:
    static const int MAX_PQ = 50;
    Element data[MAX_PQ];
    int current_size;

public:
    PriorityQueue() : current_size(0) {}

    bool is_empty() const { return current_size == 0; }
    bool is_full() const { return current_size == MAX_PQ; }

    void enqueue(int val, int prio) {
        if (is_full()) return;
        
        int i = current_size - 1;
        while (i >= 0 && data[i].priority < prio) {
            data[i + 1] = data[i];
            i--;
        }
        data[i + 1].value = val;
        data[i + 1].priority = prio;
        current_size++;
    }

    Element dequeue() {
        if (is_empty()) return { -1, -1 };
        
        Element item = data[0];
        for (int i = 1; i < current_size; i++) {
            data[i - 1] = data[i];
        }
        current_size--;
        return item;
    }
};
### Bài 4: 🔥 Dự Án Mini — Hệ Thống Hàng Chờ Bệnh Viện ⭐⭐⭐
> **Cảm hứng:** [Fila de Hospital — DanielSantDev/Projects-with-Cpp](https://github.com/DanielSantDev/Projects-with-Cpp)

Mô phỏng hệ thống hàng chờ bệnh viện với **Priority Queue**:
```
=== 🏥 HỆ THỐNG HÀNG CHỜ BỆNH VIỆN ===
Mức ưu tiên: 🔴 Cấp cứu > 🟡 Khẩn > 🟢 Thường

Bệnh nhân mới: Nguyễn Văn A — 🔴 Cấp cứu
→ Chèn lên đầu hàng chờ!

Bệnh nhân mới: Trần Thị B — 🟢 Thường
→ Xếp vào cuối hàng

Gọi tiếp theo...
✅ Mời: Nguyễn Văn A (🔴 Cấp cứu) — Phòng khám 3

Hàng chờ hiện tại (5 người):
🟡 [1] Lê Văn C  — Khẩn   — Chờ: 8 phút
🟡 [2] Phạm D    — Khẩn   — Chờ: 3 phút
🟢 [3] Hoàng E   — Thường — Chờ: 15 phút
```
**Yêu cầu:** 3 mức ưu tiên, hiển thị thời gian chờ, thống kê cuối ca (tổng bệnh nhân, thời gian chờ TB).
#include <iostream>
#include <string>
#include <vector>
#include <iomanip>

enum PriorityLevel { THUONG = 1, KHAN = 2, CAP_CUU = 3 };

class Patient {
private:
    std::string name;
    PriorityLevel priority;
    int waitingTime;

public:
    Patient(std::string n, PriorityLevel p, int w) 
        : name(n), priority(p), waitingTime(w) {}

    std::string getName() const { return name; }
    PriorityLevel getPriority() const { return priority; }
    int getWaitingTime() const { return waitingTime; }

    std::string getPriorityStr() const {
        if (priority == CAP_CUU) return "🔴 Cap cuu";
        if (priority == KHAN) return "🟡 Khan";
        return "🟢 Thuong";
    }
};

class HospitalQueue {
private:
    std::vector<Patient> queue;
    int totalServed = 0;
    int totalWaitingTime = 0;

public:
    void addPatient(const std::string& name, PriorityLevel prio, int waitTime) {
        Patient newPatient(name, prio, waitTime);
        
        auto it = queue.begin();
        while (it != queue.end() && it->getPriority() >= prio) {
            it++;
        }
        queue.insert(it, newPatient);
        
        std::cout << "Benh nhan moi: " << name << " — " << newPatient.getPriorityStr() << "\n";
        std::cout << "-> Chen vao vi tri phu hop!\n\n";
    }

    void callNextPatient(int roomNumber) {
        if (queue.empty()) {
            std::cout << "Hang cho trong.\n\n";
            return;
        }

        Patient nextPatient = queue.front();
        totalServed++;
        totalWaitingTime += nextPatient.getWaitingTime();

        std::cout << "Goi tiep theo...\n";
        std::cout << "=> Moi: " << nextPatient.getName() 
                  << " (" << nextPatient.getPriorityStr() << ") — Phong kham " << roomNumber << "\n\n";

        queue.erase(queue.begin());
    }

    void displayQueue() const {
        std::cout << "=== HE THONG HANG CHO HIEN TAI (" << queue.size() << " nguoi) ===\n";
        if (queue.empty()) {
            std::cout << "Hang cho trong.\n";
        } else {
            for (size_t i = 0; i < queue.size(); ++i) {
                std::cout << "[" << i + 1 << "] " 
                          << std::left << std::setw(15) << queue[i].getName() << " — "
                          << std::left << std::setw(12) << queue[i].getPriorityStr() << " — "
                          << "Cho: " << queue[i].getWaitingTime() << " phut\n";
            }
        }
        std::cout << "========================================================\n\n";
    }

    void printEndShiftReport() const {
        std::cout << "=== THONG KE CUOI CA ===\n";
        std::cout << "Tong so benh nhan da kham: " << totalServed << "\n";
        if (totalServed > 0) {
            double avgWait = static_cast<double>(totalWaitingTime) / totalServed;
            std::cout << "Thoi gian cho trung binh: " << std::fixed << std::setprecision(2) << avgWait << " phut\n";
        } else {
            std::cout << "Thoi gian cho trung binh: 0.00 phut\n";
        }
        std::cout << "===========================\n";
    }
};

int main() {
    // Tối ưu I/O tốc độ cao
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(NULL);

    HospitalQueue hospital;

    // Test theo đúng kịch bản mẫu
    hospital.addPatient("Le Van C", KHAN, 8);
    hospital.addPatient("Pham D", KHAN, 3);
    hospital.addPatient("Hoang E", THUONG, 15);

    hospital.displayQueue();

    hospital.addPatient("Nguyen Van A", CAP_CUU, 0);
    hospital.addPatient("Tran Thi B", THUONG, 1);

    hospital.displayQueue();

    hospital.callNextPatient(3);

    hospital.displayQueue();

    hospital.printEndShiftReport();

    return 0;
}
---
📁 Tham khảo: `Chuong3_DanhSachLienKet/Chuong3_DanhSachLienKet.cpp`
