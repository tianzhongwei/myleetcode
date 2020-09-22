# 1 要求
1) 缓存要有顺序
2) 要能快速查找键是否存在
3) 如果满了，好删除最后一个数据
4) 每次访问都要把数据插入到队头
# 2 数据结构
1) 哈希表查找快，但是无固定顺序
2) 链表有顺序，插入快，但是查找慢
3) **哈希链表：双向链表 + 哈希表**，核心是借助哈希表赋予链表快速查找的特性。
可以快速查找某个 key 是否存在缓存(链表)中，同时可以快速删除、添加结点。
4) 双向链表的必要性：要删除待删除结点本身，还需要快速修改前驱结点
5) 存储键的必要性：当缓存容量已满，不仅要删除最后一个结点，还要把 `M` 中映射到该节点的 `key` 同时删除。
```C++
class LRUCache {
private:
    unordered_map<int,list<pair<int,int>>::iterator> M; // M<键:链表指针>

    list<pair<int,int>> cache;                  // 双向链表缓存

    int cap;                                    // 缓存容量
public:
    LRUCache(int capacity) : cap(capacity) {}   // 1 构造函数，初始化缓存容量

    int get(int key) {                          // 2 用关键字提取值
        if(M.find(key) == M.end())              // 1) 若不存在，则返回 -1
            return -1;
        
        int val = M[key]->second;               // 2) 若存在，则提取值

        put(key,val);                           // 再将键值对加入缓存
        
        return val;
    }

    void put(int key,int value) {               // 3 存入缓存

        pair<int,int> x = {key,value};          // 构造结点

        if(M.find(key) != M.end()) {            // 1) 若键已存在
            cache.erase(M[key]);                    // 删除旧的结点
            cache.emplace_front(x);                 // 将新的结点插到头部
            M[key] = cache.begin();                 // 哈希表更新
            return;
        }
        if(cap == cache.size()) {               // 2.1) 若键不存在，且缓存已满
            pair<int,int> last = cache.back();      // 提取双向缓存链表的尾结点 
            cache.pop_back();                       // 删除双向缓存链表的尾结点
            M.erase(last.first);                    // 从哈希表中删除链表尾结点
        }
        cache.emplace_front(x);                 // 直接将新结点添加到双向缓存链表的头部
        M[key] = cache.begin();                 // 更新哈希表
    }
};
```