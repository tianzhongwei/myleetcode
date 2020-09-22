# 1 DFS
```C++
class Solution {
private:
    const dx[4] = {0,0,1,-1};
    const dy[4] = {1,-1,0,0};
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image,int sr,int sc,int newColor) {
        int color = image[sr][sc];
        if(color == newColor)   return image;

        dfs(image,sr,sc,newColor,color);
    }

    void dfs(vector<vector<int>>& image,int r,int c,int newColor,int color) {
        if(r >= image.size() || c >= image[0].size())   return ;

        if(image[r][c] != color)    return;

        image[r][c] = newColor;

        for(int i = 0 ; i < 4 ; ++i) {
            int newr = r + dy[i];
            int newc = c + dx[i];
            dfs(image,newr,newc,newColor,color);
        }
    }
};
```
# 2 BFS
```C++
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image,int sr,int sc,int newColor) {
        int color = image[sr][sc];
        if(color = newColor)    return image;

        queue<pair<int,int>> q;
        
        q.push({sr,sc});
        image[sr][sc] = newColor;

        while(!q.empty()) {
            auto cur = q.front();q.pop();

            for(int i = 0 ; i < 4 ; ++i) {
                int r = cur.first + dy[i];
                int c = cur.second + dx[i];
                if(r >= image.size() || c >= image[0].size())   continue;

                if(image[r][c] == color) {
                    image[r][c] = newColor;
                    q.push({r,c});
                }
            }
        }
        return image;
    }
};
```
# 3 Union-Find
```C++
class UnionFind {
private:
    int* parent;
    int* size;
    int cnt;
public:
    UnionFind() {}

    UnionFind(int n) {
        parent = new int[n];
        size = new int[n];
        for(int i = 0 ; i < n ; ++i) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int find(int x) {
        while(x != parent[x]) {
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }

    void union(int x,int y) {
        int rootX = find(x);
        int rootY = find(y);

        if(rootX == rootY)  return ;
        
        if(size[rootX] < size[rootY]) { // rootY 更重
            parent[rootX] = rootY;      // 将 rootX 接在 rootY 上
            size[rootY] += size[rootX];
        }
        else {
            parent[rootY] = rootX;
            size[rootX] += size[rootY];
        }
        cnt--;
    }

    bool connected(int x,int y) {
        return find(x) == find(y);
    }
};

class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image,int sr,int sc,int newColor) {
        int color = image[sr][sc];
        if(color == newColor)   return image;

        int rowNum = image.size();
        int colNum = image[0].size();
        UnoinFind u(rowNum * colNum);

        for(int i = 0 ; i < rowNum ; ++i) {
            for(int j = 0 ; j < colNum ; ++j) {
                if(image[i][j] != color) continue;
                int righ = j + 1;
                int down = i + 1;
                if(right < colNum && image[i][right] == color)
                    u.union(i * colNum + j,i * colNum + right);
                if(down < rowNum && image[down][j] == color)
                    u.union(i * colNum + j,down * colNum + j);
            }
        }

        for(int i = 0 ; i < rowNum ; ++i) 
            for(int j = 0 ; j < colNum ; ++j) 
                if(u.connected(i * colNum + j,sr * colNum + sc))
                    image[i][j] = newColor;

        return image;
    }
}

```