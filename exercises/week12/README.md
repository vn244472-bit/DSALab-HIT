# Tuần 12: Đệ Quy & Backtracking — Bài tập

## 🎯 Mục tiêu tuần này
Tư duy đệ quy, phân tích cây đệ quy, cài đặt Backtracking.

---


### Bài 1: Đệ quy cơ bản ⭐⭐
Cài đặt: Fibonacci (naive + memo), giai thừa, tháp Hà Nội. Vẽ cây đệ quy cho Fibonacci(5).
#include <iostream>
#include <vector>
using namespace std;


//================== Giai thua ==================
long long factorial(int n)
{
    if (n <= 1)
        return 1;
    return n * factorial(n - 1);
}


//================== Fibonacci Naive ==================
long long fibNaive(int n)
{
    if (n <= 1)
        return n;
    return fibNaive(n - 1) + fibNaive(n - 2);
}


//================== Fibonacci Memo ==================
vector<long long> memo(1000, -1);

long long fibMemo(int n)

{
   
    if (n <= 1)
        return n;

  
    if (memo[n] != -1)
        return memo[n];

    memo[n] = fibMemo(n - 1) + fibMemo(n - 2);

    return memo[n];
}


//================== Thap Ha Noi ==================
void hanoi(int n, char A, char B, char C)
{
    if (n == 1)
    {
        cout << A << " -> " << C << endl;
        return;
    }

    hanoi(n - 1, A, C, B);

    cout << A << " -> " << C << endl;

    hanoi(n - 1, B, A, C);
}


int main()

{
  
    int n = 5;

   
    cout << "Factorial = " << factorial(n) << endl;

  
    cout << "Fib Naive = " << fibNaive(n) << endl;

   
    cout << "Fib Memo = " << fibMemo(n) << endl;

   
    cout << "\nTower of Hanoi:\n";

   
    hanoi(3, 'A', 'B', 'C');

   
    return 0;
}

### Bài 2: Sinh tổ hợp ⭐⭐
Sinh tất cả tập con (Subsets) và hoán vị (Permutations) của mảng n phần tử bằng Backtracking.

#include <iostream>
#include <vector>
using namespace std;


vector<int> a = {1,2,3};


//////////////////// SUBSETS ////////////////////


vector<int> subset;

void generateSubset(int idx)
{
    if(idx==a.size())
    {
        cout<<"{ ";
        for(int x:subset)
            cout<<x<<" ";
        cout<<"}\n";
        return;
    }

    generateSubset(idx+1);

    subset.push_back(a[idx]);
    generateSubset(idx+1);
    subset.pop_back();
}

//////////////////// PERMUTATION ////////////////////

vector<int> perm;
bool used[100];

void permutation()
{
    if(perm.size()==a.size())
    {
        for(int x:perm)
            cout<<x<<" ";
        cout<<endl;
        return;
    }

    for(int i=0;i<a.size();i++)
    {
        if(!used[i])
        {
            used[i]=true;
            perm.push_back(a[i]);

            permutation();

            perm.pop_back();
            used[i]=false;
        }
    }
}

int main()
{
    cout<<"Subsets:\n";
    generateSubset(0);

    cout<<"\nPermutations:\n";
    permutation();
}
### Bài 3: Sudoku Solver ⭐⭐⭐
Giải Sudoku 9×9 bằng Backtracking. In từng bước thử.
#include <iostream>
using namespace std;

int board[9][9]={
{3,0,6,5,0,8,4,0,0},
{5,2,0,0,0,0,0,0,0},
{0,8,7,0,0,0,0,3,1},
{0,0,3,0,1,0,0,8,0},
{9,0,0,8,6,3,0,0,5},
{0,5,0,0,9,0,6,0,0},
{1,3,0,0,0,0,2,5,0},
{0,0,0,0,0,0,0,7,4},
{0,0,5,2,0,6,3,0,0}
};

bool isSafe(int r,int c,int num)
{
    for(int i=0;i<9;i++)
    {
        if(board[r][i]==num) return false;
        if(board[i][c]==num) return false;
    }

    int sr=r-r%3;
    int sc=c-c%3;

    for(int i=0;i<3;i++)
        for(int j=0;j<3;j++)
            if(board[sr+i][sc+j]==num)
                return false;

    return true;
}

bool solve()
{
    for(int r=0;r<9;r++)
    {
        for(int c=0;c<9;c++)
        {
            if(board[r][c]==0)
            {
                for(int num=1;num<=9;num++)
                {
                    if(isSafe(r,c,num))
                    {
                        cout<<"Try "<<num<<" at ("<<r<<","<<c<<")\n";

                        board[r][c]=num;

                        if(solve())
                            return true;

                        board[r][c]=0;
                    }
                }
                return false;
            }
        }
    }
    return true;
}

int main()
{
    solve();

    cout<<"\nSolved:\n";

    for(int i=0;i<9;i++)
    {
        for(int j=0;j<9;j++)
            cout<<board[i][j]<<" ";
        cout<<endl;
    }
}
### Bài 4: 🔥 Dự Án Mini — N-Queens Solver ⭐⭐⭐
> **Cảm hứng:** [Backtracking — algorithm-visualizer.org](https://algorithm-visualizer.org/backtracking/n-queens-problem)

Giải bài toán N-Queens với trực quan hóa từng bước:
```
=== ♛ N-QUEENS SOLVER ===
Nhập N: 8

Đang giải... Tìm thấy 92 lời giải!

Lời giải #1:
  . ♛ . . . . . .
  . . . . ♛ . . .
  . . . . . . . ♛
  . . ♛ . . . . .
  ♛ . . . . . . .
  . . . . . ♛ . .
  . . . ♛ . . . .
  . . . . . . ♛ .

[ENTER] Lời giải tiếp | [A] Xem tất cả | [V] Trực quan từng bước
```
**Yêu cầu:** hiển thị bàn cờ ASCII đẹp, đếm tổng số lời giải, mode trực quan từng bước đặt quân.
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;

int N;

vector<int> queen;
vector<vector<int>> solutions;

bool visual=false;

void printBoard(const vector<int>& q)
{
    cout<<endl;

    for(int i=0;i<N;i++)
    {
        for(int j=0;j<N;j++)
        {
            if(q[i]==j)
                cout<<" ♛";
            else
                cout<<" .";
        }
        cout<<endl;
    }

    cout<<endl;
}

bool safe(int row,int col)
{
    for(int i=0;i<row;i++)
    {
        if(queen[i]==col)
            return false;

        if(abs(queen[i]-col)==abs(i-row))
            return false;
    }

    return true;
}

void solve(int row)
{
    if(row==N)
    {
        solutions.push_back(queen);
        return;
    }

    for(int col=0;col<N;col++)
    {
        if(safe(row,col))
        {
            queen[row]=col;

            if(visual)
            {
                cout<<"Place queen ("<<row<<","<<col<<")\n";
                printBoard(queen);
                cin.get();
            }

            solve(row+1);

            queen[row]=-1;
        }
    }
}

int main()
{
    cout<<"=== N-QUEENS SOLVER ===\n";

    cout<<"Nhap N: ";
    cin>>N;

    queen.assign(N,-1);

    solve(0);

    cout<<"\nDang giai... Tim thay "
        <<solutions.size()
        <<" loi giai!\n";

    while(true)
    {
        cout<<"\n";
        cout<<"1. Xem tung loi giai\n";
        cout<<"2. Xem tat ca\n";
        cout<<"3. Truc quan tung buoc\n";
        cout<<"0. Thoat\n";

        int ch;
        cin>>ch;

        if(ch==0) break;

        if(ch==1)
        {
            for(int i=0;i<solutions.size();i++)
            {
                cout<<"\nLoi giai #"<<i+1<<endl;
                printBoard(solutions[i]);

                cout<<"Nhan ENTER...";
                cin.ignore();
                cin.get();
            }
        }

        if(ch==2)
        {
            for(int i=0;i<solutions.size();i++)
            {
                cout<<"\nLoi giai #"<<i+1<<endl;
                printBoard(solutions[i]);
            }
        }

        if(ch==3)
        {
            visual=true;
            queen.assign(N,-1);
            solutions.clear();

            cin.ignore();

            solve(0);

            visual=false;

            cout<<"\nDone!\n";
        }
    }
}
---
📁 Tham khảo: `Chuong4_CauTrucCay/Chuong4_CauTrucCay.cpp`
