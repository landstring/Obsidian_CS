```C
#include <iostream>
using namespace std;

template <typename T>
struct node {
    T inf;
    int height;
    node<T>* left;
    node<T>* right;
    node()
};

template <typename T>
class AVLtree {
private:
    node<T>* root; //корень дерева 
    int size;

    int getHeight(node<T>* p) {
        if (p) {
            return p->height;
        }
        else {
            return 0;
        }
    }

    int balance_factor(node<T>* p) {
        return get_height(p->left) - get_height(p->right);
    }

    void fixHeight(node<T>* p) {
        int hl = getHeight(p->left);
        int hr = getHeight(p->right);
        p->height = max(hl, hr) + 1;
    }

    node<T>* rotateRight(node<T>* p) // правый поворот вокруг p
    {
        node<T>* q = p->left;
        p->left = q->right;
        q->right = p;
        fixheight(p);
        fixheight(q);
        return q;
    }

    node* rotateLeft(node* p) // левый поворот вокруг p
    {
        node* q = p->right;
        p->right = q->left;
        q->left = p;
        fixheight(p);
        fixheight(q);
        return q; //корень поддерева 
    }

    void balance(node<T>* p) {
        fixHeight(p);
        if (balance_factor(p) == 2) { //корень поддерева
            if (balance_factor(p->left) < 0) {
                p->right = rotateRight(p->right);
            }
            return rotateLeft(p);
        }
        if (balance_factor(p) == -2) {
            if (balance_factor(p->left) > 0) {
                p->right = rotateLeft(p->left);
            }
            return rotateRight(p);
        }
    }

public:
    AVLtree() {
        
    }
};

int main()
{
    std::cout << "Hello World!\n";
}
```