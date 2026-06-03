# Tuần 5: Sắp Xếp Nâng Cao — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Quick Sort, Merge Sort, Heap Sort. So sánh với thuật toán cơ bản.

---

### Bài 1: Merge Sort ⭐⭐
Cài đặt Merge Sort đệ quy. Chứng minh O(n log n) bằng cách đếm số phép so sánh.
Hàm Merge
void Merge(int a[], int l, int m, int r, long long &soSanh)
{
    int n1 = m - l + 1;
    int n2 = r - m;

    int *L = new int[n1];
    int *R = new int[n2];

    for(int i = 0; i < n1; i++)
        L[i] = a[l + i];

    for(int i = 0; i < n2; i++)
        R[i] = a[m + 1 + i];

    int i = 0, j = 0, k = l;

    while(i < n1 && j < n2)
    {
        soSanh++;

        if(L[i] <= R[j])
            a[k++] = L[i++];
        else
            a[k++] = R[j++];
    }

    while(i < n1)
        a[k++] = L[i++];

    while(j < n2)
        a[k++] = R[j++];

    delete[] L;
    delete[] R;
}
Hàm Merge Sort
void MergeSort(int a[],
               int l,
               int r,
               long long &soSanh)
{
    if(l >= r) return;

    int m = (l + r) / 2;

    MergeSort(a, l, m, soSanh);
    MergeSort(a, m + 1, r, soSanh);

    Merge(a, l, m, r, soSanh);
}
Chứng minh O(n log n)

Quan hệ truy hồi:

T(n)=2T(
2
n
	​

)+n

Theo định lý Master:

a = 2
b = 2
f(n) = n

⇒

T(n)=O(nlogn)

Số phép so sánh thực tế cũng tăng gần:

n = 1000    → ~10,000
n = 10000   → ~130,000
n = 100000  → ~1,600,000

xấp xỉ:

n log₂ n

### Bài 2: Quick Sort ⭐⭐
Cài đặt Quick Sort với 3 chiến lược chọn pivot: đầu, cuối, giữa, ngẫu nhiên. So sánh hiệu năng.
Pivot đầu
int PivotDau(int a[], int l, int r)
{
    return a[l];
}
Pivot cuối
int PivotCuoi(int a[], int l, int r)
{
    return a[r];
}
Pivot giữa
int PivotGiua(int a[], int l, int r)
{
    return a[(l+r)/2];
}
Pivot ngẫu nhiên
int PivotRandom(int a[], int l, int r)
{
    int p = l + rand()%(r-l+1);
    return a[p];
}
Kết luận hiệu năng
Dữ liệu	Đầu	Cuối	Giữa	Random
Ngẫu nhiên	Tốt	Tốt	Rất tốt	Rất tốt
Đã sắp xếp	Rất tệ	Rất tệ	Tốt	Tốt
Ngược chiều	Rất tệ	Rất tệ	Tốt	Tốt

Trường hợp xấu:

T(n)=T(n−1)+n

⇒

T(n)=O(n
2
)
### Bài 3: Heap Sort ⭐⭐⭐
Cài đặt Heap Sort từ đầu (không dùng priority_queue). In từng bước xây dựng heap.
Hàm Shift
void Shift(int a[],
           int l,
           int r)
{
    int i = l;
    int j = 2*i + 1;

    int x = a[i];

    while(j <= r)
    {
        if(j < r && a[j] < a[j+1])
            j++;

        if(x >= a[j])
            break;

        a[i] = a[j];
        i = j;
        j = 2*i + 1;
    }

    a[i] = x;
}
In từng bước tạo Heap
void TaoHeap(int a[], int n)
{
    for(int k=n/2-1;k>=0;k--)
    {
        Shift(a,k,n-1);

        cout<<"Sau khi vun nut "<<k<<": ";
        XuatMang(a,n);
    }
}

Ví dụ:

Mang:
64 25 12 22 11 90 45 33

Sau khi vun nut 3:
64 25 12 33 11 90 45 22

Sau khi vun nut 2:
64 25 90 33 11 12 45 22

Sau khi vun nut 1:
64 33 90 25 11 12 45 22

Sau khi vun nut 0:
90 33 64 25 11 12 45 22
### Bài 4: 🔥 Dự Án Mini — Sort Race ⭐⭐⭐
> **Cảm hứng:** [Divide and Conquer — algorithm-visualizer](https://algorithm-visualizer.org/divide-and-conquer/merge-sort)

Cuộc đua sắp xếp giữa 6 thuật toán trên cùng 1 dữ liệu:
```
╔══════════════╦══════════╦══════════╦═══════════╗
║  Thuật toán  ║ n=1,000  ║ n=10,000 ║ n=100,000 ║
╠══════════════╬══════════╬══════════╬═══════════╣
║ Bubble Sort  ║   8.3ms  ║  820ms   ║  timeout  ║
║ Selection    ║   4.1ms  ║  410ms   ║  timeout  ║
║ Insertion    ║   1.2ms  ║  120ms   ║  12000ms  ║
║ Merge Sort   ║   0.3ms  ║  3.1ms   ║  35ms     ║
║ Quick Sort   ║   0.2ms  ║  2.4ms   ║  28ms     ║
║ Heap Sort    ║   0.4ms  ║  4.2ms   ║  48ms     ║
╚══════════════╩══════════╩══════════╩═══════════╝
🏆 Người chiến thắng: Quick Sort!
```
**Yêu cầu:** test trên 3 loại dữ liệu (ngẫu nhiên, đã sắp xếp, ngược chiều), xuất kết quả ra file CSV.
Các thuật toán tham gia
1. Bubble Sort
2. Selection Sort
3. Insertion Sort
4. Merge Sort
5. Quick Sort
6. Heap Sort
Sinh dữ liệu
Random
for(int i=0;i<n;i++)
    a[i]=rand()%100000;
Sorted
for(int i=0;i<n;i++)
    a[i]=i;
Reverse
for(int i=0;i<n;i++)
    a[i]=n-i;
Hàm Benchmark
long long Benchmark(
        void (*Sort)(int[],int),
        int a[],
        int n)
{
    auto start =
        chrono::high_resolution_clock::now();

    Sort(a,n);

    auto stop =
        chrono::high_resolution_clock::now();

    return chrono::duration_cast
           <chrono::microseconds>
           (stop-start)
           .count();
}
Bảng kết quả
cout<<left
    <<setw(15)<<"Algorithm"
    <<setw(15)<<"1000"
    <<setw(15)<<"10000"
    <<setw(15)<<"100000"
    <<endl;

Ví dụ:

╔══════════════╦══════════╦══════════╦═══════════╗
║ Thuật toán   ║ 1000     ║ 10000    ║ 100000    ║
╠══════════════╬══════════╬══════════╬═══════════╣
║ Bubble       ║ 8 ms     ║ 820 ms   ║ timeout   ║
║ Selection    ║ 4 ms     ║ 410 ms   ║ timeout   ║
║ Insertion    ║ 1 ms     ║ 120 ms   ║ 12000 ms  ║
║ Merge        ║ 0.3 ms   ║ 3 ms     ║ 35 ms     ║
║ Quick        ║ 0.2 ms   ║ 2 ms     ║ 28 ms     ║
║ Heap         ║ 0.4 ms   ║ 4 ms     ║ 48 ms     ║
╚══════════════╩══════════╩══════════╩═══════════╝
Xuất CSV
#include <fstream>
ofstream fout("SortRace.csv");

fout<<"Algorithm,N,Time(ms)\n";

fout<<"Bubble,1000,"<<tBubble1000<<"\n";
fout<<"Bubble,10000,"<<tBubble10000<<"\n";

...

Kết quả:

Algorithm,N,Time(ms)
Bubble,1000,8
Bubble,10000,820
Selection,1000,4
Selection,10000,410
Merge,100000,35
Quick,100000,28
Heap,100000,48
---
📁 Tham khảo: `Chuong2_TimKiem_SapXep/Chuong2_TimKiem_SapXep.cpp`
