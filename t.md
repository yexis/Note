代码一

```cpp
class Node {
public:
    // 个数
    int cnt;
    // 长度
    int len;
    array<int, 26> child;

    Node() {
        cnt = 0;
        len = 0;
        for (int i = 0; i < 26; i++) {
            child[i] = -1;
        }
    }
    Node(int _len) {
        cnt = 0;
        len = _len;
        for (int i = 0; i < 26; i++) {
            child[i] = -1;
        }
    }
};

// 动态开点 字典树
struct Trie {
    int K;
    vector<Node> tr;
    vector<multiset<int>> g;
    Trie(int _K): K(_K) {
        g.resize(K + 5);
    }
    int build(int d) {
        int idx = tr.size();
        Node node(d);
        tr.push_back(node);
        return idx;
    }
    void add(string& s) {
        int n = s.size();
        int now = 0;
        for (char c : s) {
            int idx = c - 'a';
            int nxt = tr[now].child[idx];
            if (nxt < 0) nxt = build(tr[now].len + 1);
            now = tr[now].child[idx] = nxt;
            tr[now].cnt++;
            if (tr[now].cnt > K + 1) {
                tr[now].cnt = K + 1;
            }
            g[tr[now].cnt].insert(tr[now].len);
        }
    }

    int del(string& s) {
        int n = s.size();
        int now = 0;
        for (char c : s) {
            int idx = c - 'a';
            now = tr[now].child[idx];
            g[tr[now].cnt].erase(g[tr[now].cnt].find(tr[now].len));
            tr[now].cnt--;
            g[tr[now].cnt].insert(tr[now].len);
        }

        int ans = ask();

        now = 0;
        for (char c : s) {
            int idx = c - 'a';
            now = tr[now].child[idx];
            g[tr[now].cnt].erase(g[tr[now].cnt].find(tr[now].len));
            tr[now].cnt++;
            g[tr[now].cnt].insert(tr[now].len);
        }

        return ans;
    }

    int ask() {
        int ans = 0;
        if (g[K].size()) {
            ans = max(ans, *g[K].rbegin());
        }
        if (g[K + 1].size()) {
            ans = max(ans, *g[K + 1].rbegin());
        }
        return ans;
    }
};

class Solution {
public:
    vector<int> longestCommonPrefix(vector<string>& ss, int K) {
        int n = ss.size();
        Trie tr(K);
        tr.build(0);

        for (auto& s : ss) {
            tr.add(s);
        }

        vector<int> res;
        for (string& s : ss) {
            int ans = tr.del(s);
            res.push_back(ans);
        }
        
        return res;
    }
};
```





代码二

```cpp
using pii = pair<int, int>;
class Node {
public:
    int idx;
    // 个数
    int val;
    // 长度
    int len;
    Node* child[26]{};

    Node() {
        idx = 0;
        val = 0;
        len = 0;
    }
};

class Trie {
public:
    int cnt;
    int K;
    Node* root;
    set<pii> st;
    Trie(int _K): K(_K), cnt(0) {
        root = new Node();
        root->idx = ++cnt;
    }
    
    void add(string& s, int ii) {
        int n = s.size();
        auto p = root;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            if (p->child[idx] == nullptr) {
                p->child[idx] = new Node();
                p->child[idx]->idx = ++cnt;
            }
            p = p->child[idx];
            p->val += 1;
            p->len = i + 1;
            if (p->val >= K) {
                st.insert(pii(p->len, p->idx));
            }
        }
    }

    int ask(string& s, int ii) {
        int ans = 0;
        int n = s.size();
        auto p = root;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            p = p->child[idx];
            if (p->val >= K) {
                st.erase(pii(p->len, p->idx));
            }
            if (p->val >= K + 1) {
                ans = max(ans, p->len);
            }
            p->val--;
        }
        if (st.size()) {
            ans = max(ans, st.rbegin()->first);
        }

        // 恢复
        p = root;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            p = p->child[idx];
            p->val++;
            if (p->val >= K) {
                st.insert(pii(p->len, p->idx));
            }
        }
        return ans;
    }
};

class Solution {
public:
    vector<int> longestCommonPrefix(vector<string>& ss, int K) {
        int n = ss.size();
        Trie tr(K);
        for (int i = 0; i < n; i++) {
            tr.add(ss[i], i);
        }

        // for (auto& e : tr.st) {
        //     cout << e.first << " " << e.second << "\n";
        // }
        vector<int> res(n);
        for (int i = 0; i < n; i++) {
            res[i] = tr.ask(ss[i], i);
        }
        
        return res;
    }
};
```

帮我分析一下这两段代码功能上有什么不同，为什么第一份代码超时，第二份代码通过题目，复杂度不是一样的吗？
