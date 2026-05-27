# Tuần 2: Mảng & Con Trỏ — Bài tập

## 🎯 Mục tiêu tuần này
Thành thạo mảng 1D/2D, con trỏ, cấp phát động trong C++.

---

### Bài 1: Mảng cơ bản ⭐
Nhập mảng n phần tử. Tính min, max, trung bình, tổng. Không dùng STL.
#include <iostream>
using namespace std;

int main() {

    int n;

    cout << "Nhap so phan tu: ";
    cin >> n;

    int a[100];

    // ==========================
    // NHAP MANG
    // ==========================

    for (int i = 0; i < n; i++) {

        cout << "a[" << i << "] = ";
        cin >> a[i];
    }

    // ==========================
    // TINH TOAN
    // ==========================

    int tong = 0;

    int min = a[0];
    int max = a[0];

    for (int i = 0; i < n; i++) {

        tong += a[i];

        if (a[i] < min)
            min = a[i];

        if (a[i] > max)
            max = a[i];
    }

    float tb = (float)tong / n;

    // ==========================
    // XUAT
    // ==========================

    cout << "\nTong = " << tong;
    cout << "\nMin = " << min;
    cout << "\nMax = " << max;
    cout << "\nTrung binh = " << tb;

    return 0;
}

### Bài 2: Mảng 2D ⭐⭐
Nhân 2 ma trận n×n. Tính định thức ma trận 3×3. Hiển thị đẹp.
#include <iostream>
#include <iomanip>

using namespace std;

int main() {

    int n;

    cout << "Nhap kich thuoc ma tran n: ";
    cin >> n;

    int a[10][10];
    int b[10][10];
    int c[10][10];

    // ==========================
    // NHAP A
    // ==========================

    cout << "\nNhap ma tran A:\n";

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> a[i][j];

    // ==========================
    // NHAP B
    // ==========================

    cout << "\nNhap ma tran B:\n";

    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> b[i][j];

    // ==========================
    // NHAN MA TRAN
    // ==========================

    for (int i = 0; i < n; i++) {

        for (int j = 0; j < n; j++) {

            c[i][j] = 0;

            for (int k = 0; k < n; k++) {

                c[i][j] += a[i][k] * b[k][j];
            }
        }
    }

    // ==========================
    // XUAT
    // ==========================

    cout << "\nMa tran ket qua:\n";

    for (int i = 0; i < n; i++) {

        for (int j = 0; j < n; j++) {

            cout << setw(6)
                << c[i][j];
        }

        cout << endl;
    }

    return 0;
}

### Bài 3: Con trỏ & cấp phát động ⭐⭐
Cài đặt mảng động tự resize (như `std::vector` đơn giản). Hỗ trợ push_back, pop_back, at(i).
#include <iostream>
using namespace std;

class DynamicArray {

private:

    int *data;

    int size;

    int capacity;

public:

    // ==========================
    // CONSTRUCTOR
    // ==========================

    DynamicArray() {

        capacity = 2;

        size = 0;

        data = new int[capacity];
    }

    // ==========================
    // RESIZE
    // ==========================

    void resize() {

        capacity *= 2;

        int *newData =
        new int[capacity];

        for(int i = 0; i < size; i++)
            newData[i] = data[i];

        delete[] data;

        data = newData;
    }

    // ==========================
    // PUSH BACK
    // ==========================

    void push_back(int value) {

        if(size == capacity)
            resize();

        data[size] = value;

        size++;
    }

    // ==========================
    // POP BACK
    // ==========================

    void pop_back() {

        if(size > 0)
            size--;
    }

    // ==========================
    // AT
    // ==========================

    int at(int index) {

        if(index >= 0 &&
           index < size)

            return data[index];

        return -1;
    }

    // ==========================
    // SHOW
    // ==========================

    void show() {

        for(int i = 0; i < size; i++)
            cout << data[i] << " ";

        cout << endl;
    }

    // ==========================
    // DESTRUCTOR
    // ==========================

    ~DynamicArray() {

        delete[] data;
    }
};

// ==============================
// MAIN
// ==============================

int main() {

    DynamicArray arr;

    arr.push_back(10);
    arr.push_back(20);
    arr.push_back(30);
    arr.push_back(40);

    arr.show();

    arr.pop_back();

    arr.show();

    cout << "arr[1] = "
         << arr.at(1);

    return 0;
}

### Bài 4: 🔥 Dự Án Mini — Student Score Manager ⭐⭐⭐
> **Cảm hứng:** BaiTapTongHop — Quản lý sinh viên (DSALab)

Xây dựng hệ thống quản lý điểm sinh viên bằng **mảng động**:
- Thêm / xóa / sửa sinh viên (tên, MSSV, điểm)
- Sắp xếp theo điểm (dùng Selection Sort hoặc Bubble Sort)
- Tìm kiếm theo tên hoặc MSSV (Linear Search)
- Thống kê: điểm cao nhất, thấp nhất, trung bình lớp
- Xuất danh sách ra file `diem_sinhvien.txt`

```
=== QUẢN LÝ ĐIỂM SINH VIÊN ===
1. Thêm sinh viên
2. Xóa sinh viên
3. Tìm kiếm
4. Xếp hạng lớp
5. Xuất báo cáo
0. Thoát
```
#include <iostream>
#include <fstream>
#include <iomanip>

using namespace std;

// ======================================
// STRUCT
// ======================================

struct Student {

    string name;

    string id;

    float score;
};

// ======================================
// GLOBAL
// ======================================

Student ds[100];

int n = 0;

// ======================================
// THEM
// ======================================

void AddStudent() {

    cout << "\nNhap ten: ";
    cin.ignore();
    getline(cin, ds[n].name);

    cout << "Nhap MSSV: ";
    getline(cin, ds[n].id);

    cout << "Nhap diem: ";
    cin >> ds[n].score;

    n++;
}

// ======================================
// XUAT
// ======================================

void ShowStudents() {

    cout << "\n===== DANH SACH =====\n";

    cout << left
         << setw(25) << "Ten"
         << setw(15) << "MSSV"
         << setw(10) << "Diem"
         << endl;

    for(int i = 0; i < n; i++) {

        cout << left
             << setw(25)
             << ds[i].name

             << setw(15)
             << ds[i].id

             << setw(10)
             << ds[i].score
             << endl;
    }
}

// ======================================
// TIM KIEM
// ======================================

void SearchStudent() {

    string key;

    cin.ignore();

    cout << "\nNhap ten hoac MSSV: ";

    getline(cin, key);

    bool found = false;

    for(int i = 0; i < n; i++) {

        if(ds[i].name == key
           || ds[i].id == key) {

            found = true;

            cout << ds[i].name
                 << " - "
                 << ds[i].id
                 << " - "
                 << ds[i].score
                 << endl;
        }
    }

    if(!found)
        cout << "Khong tim thay!\n";
}

// ======================================
// XOA
// ======================================

void DeleteStudent() {

    string id;

    cin.ignore();

    cout << "\nNhap MSSV can xoa: ";

    getline(cin, id);

    for(int i = 0; i < n; i++) {

        if(ds[i].id == id) {

            for(int j = i; j < n - 1; j++)
                ds[j] = ds[j + 1];

            n--;

            cout << "Da xoa!\n";

            return;
        }
    }

    cout << "Khong tim thay!\n";
}

// ======================================
// SORT
// ======================================

void SortByScore() {

    for(int i = 0; i < n - 1; i++) {

        int min = i;

        for(int j = i + 1; j < n; j++) {

            if(ds[j].score >
               ds[min].score)

                min = j;
        }

        swap(ds[i], ds[min]);
    }

    cout << "\nDa sap xep!\n";
}

// ======================================
// THONG KE
// ======================================

void Statistics() {

    if(n == 0)
        return;

    float max = ds[0].score;
    float min = ds[0].score;
    float sum = 0;

    for(int i = 0; i < n; i++) {

        if(ds[i].score > max)
            max = ds[i].score;

        if(ds[i].score < min)
            min = ds[i].score;

        sum += ds[i].score;
    }

    cout << "\nDiem cao nhat: "
         << max;

    cout << "\nDiem thap nhat: "
         << min;

    cout << "\nTrung binh lop: "
         << sum / n
         << endl;
}

// ======================================
// XUAT FILE
// ======================================

void ExportFile() {

    ofstream fout(
    "diem_sinhvien.txt");

    for(int i = 0; i < n; i++) {

        fout << ds[i].name
             << " | "
             << ds[i].id
             << " | "
             << ds[i].score
             << endl;
    }

    fout.close();

    cout << "\nDa xuat file!\n";
}

// ======================================
// MENU
// ======================================

int main() {

    int choice;

    do {

        cout << "\n=== QUAN LY DIEM SINH VIEN ===\n";

        cout << "1. Them sinh vien\n";
        cout << "2. Xoa sinh vien\n";
        cout << "3. Tim kiem\n";
        cout << "4. Xep hang lop\n";
        cout << "5. Thong ke\n";
        cout << "6. Xuat bao cao\n";
        cout << "7. Hien thi danh sach\n";
        cout << "0. Thoat\n";

        cout << "Nhap lua chon: ";
        cin >> choice;

        switch(choice) {

        case 1:
            AddStudent();
            break;

        case 2:
            DeleteStudent();
            break;

        case 3:
            SearchStudent();
            break;

        case 4:
            SortByScore();
            break;

        case 5:
            Statistics();
            break;

        case 6:
            ExportFile();
            break;

        case 7:
            ShowStudents();
            break;
        }

    } while(choice != 0);

    return 0;
}

---
📁 Tham khảo: `Chuong1_TongQuan/Chuong1_TongQuan.cpp`
