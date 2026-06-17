# Tuần 8: Stack & Ứng Dụng — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Stack bằng mảng và Linked List. Ứng dụng Stack trong bài toán thực tế.

---

### Bài 1: Cài đặt Stack ⭐⭐
Cài đặt Stack bằng mảng (array-based) và bằng Linked List. So sánh ưu nhược điểm.
#include <iostream>
using namespace std;

// Cau truc nut (Node) lay tu code goc cua ban
struct Node {
    int data;
    Node* next;
};

// Cac ham tien ich lay tu code goc cua ban
Node* TaoNode(int x) {
    Node* p = new Node;
    p->data = x;
    p->next = nullptr;
    return p;
}

// ==========================================
// CAI DAT STACK (LINKED LIST)
// ==========================================
struct Stack {
    Node* top;
};

void KhoiTaoStack(Stack& S) {
    S.top = nullptr;
}

bool StackRong(const Stack& S) {
    return S.top == nullptr;
}

void Push(Stack& S, int x) {
    Node* p = TaoNode(x);
    p->next = S.top;
    S.top = p;
}

int Pop(Stack& S) {
    if (StackRong(S)) {
        cout << "Stack rong!\n";
        return -1;
    }
    int val = S.top->data;
    Node* p = S.top;
    S.top = S.top->next;
    delete p;
    return val;
}

int PeekStack(const Stack& S) {
    if (StackRong(S)) return -1;
    return S.top->data;
}

void InStack(const Stack& S) {
    cout << "Stack (TOP->BOT): ";
    Node* cur = S.top;
    while (cur) {
        cout << cur->data << " ";
        cur = cur->next;
    }
    cout << "\n";
}

int main() {
    Stack S;
    KhoiTaoStack(S);

    cout << "--- DEMO BAI 1: CAI DAT STACK ---\n";
    cout << "Push cac gia tri: 10, 20, 30\n";
    Push(S, 10);
    Push(S, 20);
    Push(S, 30);
    InStack(S);

    cout << "Phan tu o dinh (Peek): " << PeekStack(S) << "\n";
    cout << "Pop phan tu: " << Pop(S) << "\n";
    InStack(S);

    return 0;
}
### Bài 2: Kiểm tra ngoặc hợp lệ ⭐⭐
Kiểm tra chuỗi có đóng mở ngoặc `()`, `[]`, `{}` hợp lệ không. Xử lý cả chuỗi code thực tế.
#include <iostream>
#include <string>
using namespace std;

struct Node {
    int data;
    Node *next;
};

Node* TaoNode(int x) {
    Node *p = new Node;
    p->data = x;
    p->next = nullptr;
    return p;
}

struct Stack {
    Node *top;
};

void KhoiTaoStack(Stack &S) { S.top = nullptr; }
bool StackRong(const Stack &S) { return S.top == nullptr; }
void Push(Stack &S, int x) {
    Node *p = TaoNode(x);
    p->next = S.top;
    S.top = p;
}
int Pop(Stack &S) {
    if (StackRong(S)) return -1;
    int val = S.top->data;
    Node *p = S.top;
    S.top = S.top->next;
    delete p;
    return val;
}

// ==========================================
// HAM KIEM TRA NGOAC HOP LE
// ==========================================
bool KiemTraNgoacHopLe(const string &s) {
    Stack S; 
    KhoiTaoStack(S);
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            Push(S, c);
        }
        else if (c == ')' || c == ']' || c == '}') {
            if (StackRong(S)) return false; // Thua ngoac dong
            int topChar = Pop(S);
            if ((c == ')' && topChar != '(') ||
                (c == ']' && topChar != '[') ||
                (c == '}' && topChar != '{')) {
                return false; // Sai cap ngoặc
            }
        }
    }
    return StackRong(S);
}

int main() {
    cout << "--- DEMO BAI 2: KIEM TRA NGOAC ---\n";
    string code1 = "for(int i=0; i<n; i++) { a[i] = {x: 1}; }";
    string code2 = "for(int i=0; i<n; i++) { a[i] = {x: 1); }";

    cout << "Code 1: \"" << code1 << "\" -> " << (KiemTraNgoacHopLe(code1) ? "Hop le" : "Khong hop le") << "\n";
    cout << "Code 2: \"" << code2 << "\" -> " << (KiemTraNgoacHopLe(code2) ? "Hop le" : "Khong hop le") << "\n";
    return 0;
}
### Bài 3: Chuyển đổi biểu thức ⭐⭐⭐
Chuyển biểu thức Infix → Postfix → Prefix. In từng bước.
#include <iostream>
#include <string>
#include <vector>
#include <cctype>
using namespace std;

struct Node {
    int data;
    Node *next;
};

Node* TaoNode(int x) {
    Node *p = new Node; p->data = x; p->next = nullptr; return p;
}

struct Stack {
    Node *top;
};

void KhoiTaoStack(Stack &S) { S.top = nullptr; }
bool StackRong(const Stack &S) { return S.top == nullptr; }
void Push(Stack &S, int x) {
    Node *p = TaoNode(x); p->next = S.top; S.top = p;
}
int Pop(Stack &S) {
    if (StackRong(S)) return -1;
    int val = S.top->data; Node *p = S.top; S.top = S.top->next; delete p; return val;
}
int PeekStack(const Stack &S) {
    if (StackRong(S)) return -1; return S.top->data;
}

// Các hàm bổ trợ toán tử
int uuTien(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

bool laToanTu(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
}

// ==========================================
// CHUYEN INFIX -> POSTFIX
// ==========================================
vector<string> infixToPostfix(string expr) {
    vector<string> postfix;
    Stack sToanTu; 
    KhoiTaoStack(sToanTu);
    int n = expr.length();

    for (int i = 0; i < n; i++) {
        if (isspace(expr[i])) continue;

        if (isdigit(expr[i]) || expr[i] == '.') {
            string num = "";
            while (i < n && (isdigit(expr[i]) || expr[i] == '.')) {
                num += expr[i];
                i++;
            }
            i--; 
            postfix.push_back(num);
        }
        else if (expr[i] == '(') {
            Push(sToanTu, expr[i]);
        }
        else if (expr[i] == ')') {
            while (!StackRong(sToanTu) && PeekStack(sToanTu) != '(') {
                string op(1, (char)Pop(sToanTu));
                postfix.push_back(op);
            }
            if (!StackRong(sToanTu)) Pop(sToanTu);
        }
        else if (laToanTu(expr[i])) {
            while (!StackRong(sToanTu) && uuTien((char)PeekStack(sToanTu)) >= uuTien(expr[i])) {
                if (expr[i] == '^' && (char)PeekStack(sToanTu) == '^') break;
                string op(1, (char)Pop(sToanTu));
                postfix.push_back(op);
            }
            Push(sToanTu, expr[i]);
        }
    }
    while (!StackRong(sToanTu)) {
        string op(1, (char)Pop(sToanTu));
        postfix.push_back(op);
    }
    return postfix;
}

int main() {
    cout << "--- DEMO BAI 3: INFIX TO POSTFIX ---\n";
    string bieuThuc = "(3 + 4.5) * 2 - 8 / 4 ^ 2";
    cout << "Infix: " << bieuThuc << "\n";

    vector<string> postfix = infixToPostfix(bieuThuc);
    cout << "Postfix: ";
    for (const string &t : postfix) cout << t << " ";
    cout << "\n";

    return 0;
}
### Bài 4: 🔥 Dự Án Mini — Máy Tính Biểu Thức ⭐⭐⭐
> **Cảm hứng:** [Pilha_Expressão_A — DanielSantDev/Projects-with-Cpp](https://github.com/DanielSantDev/Projects-with-Cpp)
#include <iostream>
#include <string>
#include <vector>
#include <cmath>
#include <iomanip>
using namespace std;

bool laToanTu(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
}

void inTrangThaiStack(const vector<double> &s) {
    cout << "    -> Stack: [";
    for (size_t i = 0; i < s.size(); i++) {
        cout << s[i];
        if (i < s.size() - 1) cout << ", ";
    }
    cout << "]\n";
}

// ==========================================
// TINH TOAN BIEU THUC POSTFIX
// ==========================================
double tinhPostfix(const vector<string> &postfix) {
    vector<double> sSoThuc; // Dung vector gia lap Stack so thuc de de hien thi trang thai
    
    for (const string &token : postfix) {
        if (token.length() == 1 && laToanTu(token[0])) {
            char op = token[0];
            if (sSoThuc.size() < 2) continue;
            
            double val2 = sSoThuc.back(); sSoThuc.pop_back();
            double val1 = sSoThuc.back(); sSoThuc.pop_back();
            double res = 0;
            
            if (op == '+') res = val1 + val2;
            else if (op == '-') res = val1 - val2;
            else if (op == '*') res = val1 * val2;
            else if (op == '/') res = val1 / val2;
            else if (op == '^') res = pow(val1, val2);
            
            cout << "  '" << op << "' -> Pop " << val2 << ", Pop " << val1 << " -> Push " << res;
            sSoThuc.push_back(res);
            inTrangThaiStack(sSoThuc);
        } 
        else {
            double val = stod(token);
            cout << "  Push " << val;
            sSoThuc.push_back(val);
            inTrangThaiStack(sSoThuc);
        }
    }
    return sSoThuc.empty() ? 0 : sSoThuc.back();
}

int main() {
    cout << "--- DEMO BAI 4: MO PHONG TINH TOAN ---\n";
    // Mang token postfix lay ket qua mau tu bai 3: (3 + 4.5) * 2 - 8 / 4 ^ 2
    vector<string> postfixInput = {"3", "4.5", "+", "2", "*", "8", "4", "2", "^", "/", "-"};
    
    cout << "Bieu thuc Postfix can tinh: ";
    for(const string &t : postfixInput) cout << t << " ";
    cout << "\n\nTien trinh xu ly:\n";

    double kq = tinhPostfix(postfixInput);
    cout << "\n=> Ket qua cuoi cung = " << setprecision(4) << kq << "\n";

    return 0;
}
Xây dựng máy tính tính biểu thức toán học bằng Stack:
```
=== 🧮 MÁY TÍNH BIỂU THỨC ===
Nhập biểu thức: (3 + 4) * 2 - 8 / 4

Bước 1 — Chuyển sang Postfix: 3 4 + 2 * 8 4 / -
Bước 2 — Tính toán:
  Push 3 → Stack: [3]
  Push 4 → Stack: [3, 4]
  '+' → Pop 4, Pop 3 → Push 7 → Stack: [7]
  Push 2 → Stack: [7, 2]
  '*' → Pop 2, Pop 7 → Push 14 → Stack: [14]
  ...

✅ Kết quả: (3 + 4) * 2 - 8 / 4 = 12
```
**Yêu cầu:** hỗ trợ +, -, *, /, ^, ngoặc đơn, số thập phân, hiển thị từng bước stack.

---
📁 Tham khảo: `Chuong3_DanhSachLienKet/Chuong3_DanhSachLienKet.cpp`
