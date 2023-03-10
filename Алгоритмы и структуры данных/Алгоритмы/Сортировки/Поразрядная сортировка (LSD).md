[[Сортировка]]

Для данной сортировке необходимо знать два параметра — N (размер массива) и k — максимальное количество разрядов чисел. P — основание системы счисления. В нашем случае P = 10. Цифра k-ого разряда определяется по формуле: 

$$
\frac{a[i]}{P^{k}} \bmod P
$$
```C
void LSD_sort(vector<int>& v) {
    int k = 0; //определим k по максимальному числу в массиве
    int max = *max_element(v.begin(), v.end());
    while (max > 0) {
        max /= 10;
        k++;
    }
    for (int i = 0; i <= k; i++) { 
        vector<vector<int>> P(10);
        for (int j = 0; j < v.size(); j++) {
            P[v[j] / (int)pow(10, i) % 10].push_back(v[j]);
        }
        int p = 0;
        for (int p1 = 0; p1 < 10; p1++) {
            for (int p2 = 0; p2 < P[p1].size(); p2++) {
                v[p] = P[p1][p2];
                p++;
            }
        }
    }
}
```

Оценим сложность. Для поиска максимума необходимо сделать N шагов. Для оценки разряда — k шагов. Для разбиения по векторам, объединения и очистка векторов надо сделать по N шагов для каждой операции. Последние три цикла повторяются k раз. Следовательно, всего надо сделать N +k + 3kN шагов. Можно сказать, что сложность O(k * N). Для больших N и малых разрядов (а чаще всего k < N) можно сказать, что сложность линейная.