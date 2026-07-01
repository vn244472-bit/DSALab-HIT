# Tuần 13: Graph Cơ Bản — Bài tập

## 🎯 Mục tiêu tuần này
Biểu diễn graph, cài đặt BFS và DFS, ứng dụng tìm đường đi.

---

### Bài 1: Biểu diễn Graph ⭐⭐
Cài đặt Graph bằng Adjacency List và Adjacency Matrix. Chuyển đổi giữa 2 dạng. In ra màn hình.
#include <iostream>
#include <vector>
using namespace std;

class Graph
{
private:
    int V;
    vector<vector<int>> matrix;
    vector<vector<int>> adj;

public:
    Graph(int n)
    {
        V = n;
        matrix.assign(V, vector<int>(V, 0));
        adj.assign(V, vector<int>());
    }

    void addEdge(int u, int v)
    {
        matrix[u][v] = 1;
        matrix[v][u] = 1;

        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    void printMatrix()
    {
        cout << "\nAdjacency Matrix\n";

        for (int i = 0; i < V; i++)
        {
            for (int j = 0; j < V; j++)
                cout << matrix[i][j] << " ";

            cout << endl;
        }
    }

    void printList()
    {
        cout << "\nAdjacency List\n";

        for (int i = 0; i < V; i++)
        {
            cout << i << ": ";

            for (int x : adj[i])
                cout << x << " ";

            cout << endl;
        }
    }

    void matrixToList()
    {
        adj.assign(V, vector<int>());

        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                if (matrix[i][j])
                    adj[i].push_back(j);
    }

    void listToMatrix()
    {
        matrix.assign(V, vector<int>(V, 0));

        for (int i = 0; i < V; i++)
            for (int x : adj[i])
                matrix[i][x] = 1;
    }
};

int main()
{
    Graph g(5);

    g.addEdge(0,1);
    g.addEdge(0,2);
    g.addEdge(1,3);
    g.addEdge(3,4);

    g.printMatrix();
    g.printList();
}
### Bài 2: BFS & DFS ⭐⭐
Cài đặt BFS (dùng Queue) và DFS (dùng Stack + đệ quy). Duyệt từ đỉnh cho trước, in thứ tự duyệt.
#include <iostream>
#include <vector>
#include <queue>
#include <stack>
using namespace std;

class Graph
{
public:
    int V;
    vector<vector<int>> adj;

    Graph(int n)
    {
        V = n;
        adj.resize(V);
    }

    void addEdge(int u,int v)
    {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    //---------------- BFS ----------------

    void BFS(int start)
    {
        vector<bool> visited(V,false);

        queue<int> q;

        q.push(start);

        visited[start]=true;

        cout<<"BFS: ";

        while(!q.empty())
        {
            int u=q.front();
            q.pop();

            cout<<u<<" ";

            for(int v:adj[u])
            {
                if(!visited[v])
                {
                    visited[v]=true;
                    q.push(v);
                }
            }
        }
        cout<<endl;
    }

    //---------------- DFS De quy ----------------

    void DFS_Rec(int u,vector<bool>& visited)
    {
        visited[u]=true;

        cout<<u<<" ";

        for(int v:adj[u])
            if(!visited[v])
                DFS_Rec(v,visited);
    }

    void DFS(int start)
    {
        vector<bool> visited(V,false);

        cout<<"DFS (Rec): ";

        DFS_Rec(start,visited);

        cout<<endl;
    }

    //---------------- DFS Stack ----------------

    void DFS_Stack(int start)
    {
        vector<bool> visited(V,false);

        stack<int> st;

        st.push(start);

        cout<<"DFS (Stack): ";

        while(!st.empty())
        {
            int u=st.top();
            st.pop();

            if(visited[u]) continue;

            visited[u]=true;

            cout<<u<<" ";

            for(int i=adj[u].size()-1;i>=0;i--)
                if(!visited[adj[u][i]])
                    st.push(adj[u][i]);
        }

        cout<<endl;
    }
};

int main()
{
    Graph g(6);

    g.addEdge(0,1);
    g.addEdge(0,2);
    g.addEdge(1,3);
    g.addEdge(2,4);
    g.addEdge(3,5);

    g.BFS(0);

    g.DFS(0);

    g.DFS_Stack(0);
}
### Bài 3: Phát hiện chu trình ⭐⭐⭐
Phát hiện chu trình trong graph có hướng và vô hướng. Tìm các thành phần liên thông.
#include <iostream>
#include <vector>
using namespace std;

class Graph
{
public:
    int V;
    vector<vector<int>> adj;

    Graph(int n)
    {
        V=n;
        adj.resize(V);
    }

    void addEdge(int u,int v)
    {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    bool dfs(int u,int parent,vector<bool>& visited)
    {
        visited[u]=true;

        for(int v:adj[u])
        {
            if(!visited[v])
            {
                if(dfs(v,u,visited))
                    return true;
            }
            else if(v!=parent)
                return true;
        }

        return false;
    }

    bool hasCycle()
    {
        vector<bool> visited(V,false);

        for(int i=0;i<V;i++)
        {
            if(!visited[i])
                if(dfs(i,-1,visited))
                    return true;
        }

        return false;
    }

    void connectedComponents()
    {
        vector<bool> visited(V,false);

        int cnt=0;

        for(int i=0;i<V;i++)
        {
            if(!visited[i])
            {
                cnt++;

                cout<<"Component "<<cnt<<": ";

                DFS(i,visited);

                cout<<endl;
            }
        }
    }

    void DFS(int u,vector<bool>& visited)
    {
        visited[u]=true;

        cout<<u<<" ";

        for(int v:adj[u])
            if(!visited[v])
                DFS(v,visited);
    }
};

int main()
{
    Graph g(6);

    g.addEdge(0,1);
    g.addEdge(1,2);
    g.addEdge(2,0);

    g.addEdge(4,5);

    if(g.hasCycle())
        cout<<"Graph co chu trinh\n";
    else
        cout<<"Khong co\n";

    g.connectedComponents();
}
-----------------------------------------
#include <iostream>
#include <vector>
using namespace std;

class Graph
{
public:
    int V;
    vector<vector<int>> adj;

    Graph(int n)
    {
        V=n;
        adj.resize(V);
    }

    void addEdge(int u,int v)
    {
        adj[u].push_back(v);
    }

    bool dfs(int u, vector<bool>& visited, vector<bool>& recStack)
    {
        visited[u]=true;
        recStack[u]=true;

        for(int v:adj[u])
        {
            if(!visited[v])
            {
                if(dfs(v,visited,recStack))
                    return true;
            }
            else if(recStack[v])
                return true;
        }

        recStack[u]=false;
        return false;
    }

    bool hasCycle()
    {
        vector<bool> visited(V,false);
        vector<bool> recStack(V,false);

        for(int i=0;i<V;i++)
            if(!visited[i])
                if(dfs(i,visited,recStack))
                    return true;

        return false;
    }
};

int main()
{
    Graph g(4);

    g.addEdge(0,1);
    g.addEdge(1,2);
    g.addEdge(2,3);
    g.addEdge(3,1);

    if(g.hasCycle())
        cout<<"Co chu trinh";
    else
        cout<<"Khong";
}
### Bài 4: 🔥 Dự Án Mini — Tìm Đường Ngắn Nhất (BFS) ⭐⭐⭐
> **Cảm hứng:** [BFS — algorithm-visualizer.org](https://algorithm-visualizer.org/simple-recursive/breadth-first-search)

Tìm đường đi ngắn nhất trong mê cung bằng BFS:
```
=== 🗺️ TÌM ĐƯỜNG TRONG MÊ CUNG (BFS) ===
Bản đồ (S=Xuất phát, E=Đích, #=Tường):

  S . . # . . .
  # # . # . # .
  . . . . . # .
  . # # # . . .
  . . . . # . E

Đang tìm đường... ✅ Tìm thấy!
Đường ngắn nhất: S → (0,1) → (0,2) → (1,2) → ... → E
Độ dài: 12 bước

Hiển thị đường đi:
  S ★ ★ # . . .
  # # ★ # . # .
  . . ★ ★ ★ # .
  . # # # ★ ★ .
  . . . . # ★ E
```
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

struct Node
{
    int x, y;
};

const int ROW = 5;
const int COL = 7;

char maze[ROW][COL] =
{
    {'S','.','.','#','.','.','.'},
    {'#','#','.','#','.','#','.'},
    {'.','.','.','.','.','#','.'},
    {'.','#','#','#','.','.','.'},
    {'.','.','.','.','#','.','E'}
};

int dx[4]={-1,1,0,0};
int dy[4]={0,0,-1,1};

bool inside(int x,int y)
{
    return x>=0&&x<ROW&&y>=0&&y<COL;
}

int main()
{
    Node start,end;

    for(int i=0;i<ROW;i++)
        for(int j=0;j<COL;j++)
        {
            if(maze[i][j]=='S') start={i,j};
            if(maze[i][j]=='E') end={i,j};
        }

    queue<Node> q;

    bool visited[ROW][COL]={false};

    Node parent[ROW][COL];

    q.push(start);

    visited[start.x][start.y]=true;

    bool found=false;

    while(!q.empty())
    {
        Node cur=q.front();
        q.pop();

        if(cur.x==end.x&&cur.y==end.y)
        {
            found=true;
            break;
        }

        for(int k=0;k<4;k++)
        {
            int nx=cur.x+dx[k];
            int ny=cur.y+dy[k];

            if(inside(nx,ny)&&
               !visited[nx][ny]&&
               maze[nx][ny]!='#')
            {
                visited[nx][ny]=true;

                parent[nx][ny]=cur;

                q.push({nx,ny});
            }
        }
    }

    if(!found)
    {
        cout<<"Khong tim thay duong!\n";
        return 0;
    }

    vector<Node> path;

    Node cur=end;

    while(!(cur.x==start.x&&cur.y==start.y))
    {
        path.push_back(cur);
        cur=parent[cur.x][cur.y];
    }

    path.push_back(start);

    reverse(path.begin(),path.end());

    cout<<"Tim thay!\n\n";

    cout<<"Duong di:\n";

    for(auto p:path)
        cout<<"("<<p.x<<","<<p.y<<") ";

    cout<<"\n\nDo dai: "<<path.size()-1<<" buoc\n";

    for(auto p:path)
    {
        if(maze[p.x][p.y]=='.')
            maze[p.x][p.y]='*';
    }

    cout<<"\nBan do:\n";

    for(int i=0;i<ROW;i++)
    {
        for(int j=0;j<COL;j++)
            cout<<maze[i][j]<<" ";

        cout<<endl;
    }
}
---
📁 Tham khảo: Kiến thức mới — tạo file `Graph/Graph.cpp`
