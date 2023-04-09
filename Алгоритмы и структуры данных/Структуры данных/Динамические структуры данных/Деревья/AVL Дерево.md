
**АВЛ-дерево** (англ. _AVL-Tree_) — сбалансированное [[Двоичное дерево поиска]], в котором поддерживается следующее свойство: для каждой его вершины высота её двух поддеревьев различается не более чем на 1.

АВЛ-деревья названы по первым буквам фамилий их изобретателей, Г. М. Адельсона-Вельского и Е. М. Ландиса, которые впервые предложили использовать АВЛ-деревья в 1962 году.

[Визуализация работы дерева](https://www.cs.usfca.edu/~galles/visualization/AVLtree.html)

***Теорема:***

>AVL-дерево с n ключами имеет высоту $h = O(log(n))$

***Доказательство:***

Высоту поддерева с корнем в x будем обозначать h(x).

**Лемма:**

>Пусть $m_{h}$ - минимальное число вершин в AVL-дереве высоты h, тогда $m_{h} = F_{h + 2} - 1$, где $F_{h}$ - h-ое число Фибоначчи.

**Доказательство:**

Если $m_{h}$ - минимальное число вершин в AVL-дереве высоты h. Тогда как легко видеть, $m_{h + 2} = m_{h + 1} + m_{h} + 1$. Равенство $m_{h} = F_{h + 2} - 1$ докажем по индукции ([[Метод математической индукции]]). 

База индукции: $m_{1} = F_{3} - 1$ - верно, $m_{1} = 1$, $F_{3} = 2$.
Допустим $m_{h} = F_{h + 2} - 1$ - верно.
Тогда $m_{h + 1} = m_{h} + m_{h - 1} + 1 = F_{h + 2} - 1 + F_{h + 1} - 1 + 1 = F_{h + 3} - 1$ - верно. 
Таким образом доказано. 

$$F_{h} = \Omega(\varphi^{h}), \varphi = \frac{\sqrt{5} + 1}{2}$$
$n \ge \varphi^{h}$ 

Логарифмируем по основанию $\varphi$, получаем: 
$$log_{\varphi}n \ge h$$
Таким образом, получаем, что высота AVL-дерева из n вершин - $O(log n)$

***Балансировка***

**Балансировкой вершины** называется операция, которая в случае разницы высот левого и правого поддеревьев $|h(L) - h(R)| = 2$, изменяет связи предок-потомок в поддереве данной вершины так, чтобы восстановилось свойство дерева  $|h(L) - h(R)| \le 1$, иначе ничего не меняет. Для балансировки будем хранить для каждой вершины разницу между высотой её левого и правого поддерева $diff[i] = h(L) - h(R)$. 

Для балансировки используется 4 типа вращений: 

![[Pasted image 20230328151445.png]]


***Реализация***

```C
using namespace std;

template <typename T>
struct node {
    T inf;
    int height;
    node<T>* left;
    node<T>* right;
    node(T inf) {
        this->inf = inf;
        this->height = 1;
        this->left = this->right = nullptr;
    }
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
        if (p) {
            return getHeight(p->left) - getHeight(p->right);
        }
        else return 0;
    }

    node<T>* rotateRight(node<T>* p) // правый поворот вокруг p
    {
        bool f = false;
        if (p == this->root) {
            f = true;
        }
        node<T>* q = p->left;
        node<T>* x = q->right;
        q->right = p;
        p->left = x;
        p->height = max(getHeight(p->left), getHeight(p->right)) + 1;
        q->height = max(getHeight(q->left), getHeight(q->right)) + 1;
        if (f) {
            this->root = q;
        }
        return q;
    }

    node<T>* rotateLeft(node<T>* p) // левый поворот вокруг p
    {
        bool f = false;
        if (p == this->root) {
            f = true;
        }
        node<T>* q = p->right;
        node<T>* x = q->left;
        q->left = p;
        p->right = x;
        p->height = max(getHeight(p->left), getHeight(p->right)) + 1;
        q->height = max(getHeight(q->left), getHeight(q->right)) + 1;
        if (f) {
            this->root = q;
        }
        return q;
    }

    node<T>* minimum(node<T>* p) {
        node<T>* q = p;
        while (q->left) {
            q = q->left;
        }
        return q;
    }

    node<T>* insertHelp(node<T>* p, T inf) {
        if (!p) {
            this->size++;
            if (this->size == 1) {
                this->root = new node<T>(inf);
                return this->root;
            }
            else {
                return new node<T>(inf);
            }
        }
        else if (inf < p->inf) {
            p->left = insertHelp(p->left, inf);
            p = balance(p);
            return p;
        }
        else if (inf > p->inf) {
            p->right = insertHelp(p->right, inf);
            p = balance(p);
            return p;
        }
        else {
            return nullptr;
        }
    }

    node<T>* eraseHelp(node<T>* p, T inf) {
        node<T>* tmp;
        if (!p) {
            return p;
        }
        else if (inf < p->inf) {
            p->left = eraseHelp(p->left, inf);
        }
        else if (inf > p->inf) {
            p->right = eraseHelp(p->right, inf);
        }
        else {
            if (p->right && p->left) {
                tmp = minimum(p->right);
                p->inf = tmp->inf;
                p->right = eraseHelp(p->right, p->inf);
            }
            else {
                tmp = p;
                if (!p->left) {
                    p = p->right;
                }
                else if (!p->right) {
                    p = p->left;
                }
                if (tmp == this->root) {
                    this->root = p;
                }
                delete tmp;
                this->size--;
            }
        }

        if (!p) {
            return p;
        }

        return balance(p);
    }

    node<T>* balance(node<T>* p) {
        p->height = max(getHeight(p->left), getHeight(p->right)) + 1;
        if (balance_factor(p) == -2) {
            if (balance_factor(p->right) == 1) {
                p->right = rotateRight(p->right);
            }
            return rotateLeft(p);
        }
        if (balance_factor(p) == 2) {
            if (balance_factor(p->left) == -1) {
                p->left = rotateLeft(p->left);
            }
            return rotateRight(p);
        }
        return p;
    }


    void inorderHelp(node<T>* p) {
        if (p) {
            cout << p->inf << ' ';
            inorderHelp(p->left);
            inorderHelp(p->right);
        }
    }

public:
    AVLtree() {
        root = nullptr;
        size = 0;
    }

    void insert(T inf) {
        insertHelp(root, inf);
    }

    void erase(T inf) {
        eraseHelp(this->root, inf);
    }

    int getSize() {
        return this->size;
    }

    void print() {
        if (this->size > 0) {
            inorderHelp(root);
            cout << " Size: " << this->size << endl;
        }
        else {
            cout << "The AVL - tree is empty!" << endl;
        }
    }
};
```