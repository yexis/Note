```cpp
class Node {
public:
    int val;
    vector<Node*> child;

    Node() {
        val = 0;
        child = vector<Node*>(26);
    }
};

class Trie {
public:
    Node* root;
    Trie() {
        root = new Node();
    }
    
    void add(string s) {
        int n = s.size();
        auto p = root;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            if (p->child[idx] == nullptr) {
                p->child[idx] = new Node();
            }
            p = p->child[idx];
            p->val += 1;
        }
    }
    
    int ask(string s) {
        int n = s.size();
        auto p = root;
        
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            if (p->child[idx] == nullptr)  {
                return ans;
            }
            p = p->child[idx];
            ans += p->val;
        }
        
        return ans;
    }
};
```

