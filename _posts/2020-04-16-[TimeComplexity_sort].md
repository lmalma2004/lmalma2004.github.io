---
layout: post
title: 시간복잡도 분석(정렬 편)
description: 선택/삽입/퀵/머지/힙
date:   2020-04-16
categories:
---
### 시간복잡도 분석 (정렬 편)

```c++
//선택 정렬과 삽입 정렬
void selectionSort(vector<int>& A){
    for(int i=0; i<A.size(); i++){
        int minIndex = i;
        for(int j=i+1; j<A.size(); j++)
            if(A[minIndex] > A[j])
                minIndex = j;
        swap(A[i], A[minIndex]);
    }
}
void insertionSort(vector<int>& A){
    for(int i=0; i<A.size(); i++){
        //A[0..i-1]에 A[i]를 끼워넣는다.
        int j = i;
        while(j > 0 && A[j-1] > A[j]){
            swap(A[j-1], A[j]);
            --j;
        }
    }
}
```

1. **선택정렬 / 최선, 최악 = O(N2)**

   A[]에 포함된 원소들과는 상관 없이 A[]의 크기 N에 의해서만 결정되기 때문에, 최악의 경우와 최선의 경우의 시간 복잡도가 같다.

   

2. **삽입정렬 / 최선 = O(N), 최악 = O(N2)**

   삽입 정렬은 전체 배열 중 정렬되어 있는 부분배열에 새 원소를 끼워넣는 일을 반복하는 방식으로 동작한다.

   이를 위해서는 A[i]앞에 있는 정렬된 부분 배열의 적절한 위치에 A[i]를 삽입 해야 하며, 이것이 이 알고리즘이 삽입 정렬이라고 불리는 이유다.

   삽입을 위해서 삽입 정렬은 A[i]에 있던 값을 하나씩 앞으로 옮기는데, j에 대한 while문이 이 과정을 구현한다.

   이 while문은 A[j]에 있는 숫자보다 작은 숫자를 만날 때까지 A[j]를 앞으로 옮겨 간다. 이때 while문의 수행 횟수는 처음에 A[i]에 위치한 값에 따라 달라진다는 것을 알 수 있다.

   

   따라서 처음부터 이미 정렬된 배열이 주어진다면, 이때 모든 원소는 처음부터 제자리에 있기 때문에 j에 대한 while문은 매번 즉시 종료하게 된다. 이 경우 while문의 사간 복잡도를 **O(1)**로 보아야 하기 때문에 삽입 정렬의 전체 시간 복잡도는 i에 대한 for문에 좌우된다. 따라서 전체 수행 시간은 선형시간, 즉 **O(N)**이 된다.

   

   반면 역순으로 정렬된 배열이 주어진다면, 이 경우 모든 while문은 j를 0까지 줄여가며 숫자들을 맨 앞으로 끌고 가야 한다. 때문에 while문의 시간복잡도는 **O(N)**이 되고, 전체 시간복잡도는 **O(N2)**이 된다.

3. **Merge Sort / O(NlgN)**

   참조 : https://gmlwjd9405.github.io/2018/05/08/algorithm-merge-sort.html

4. **퀵정렬 / 평균 = O(NlogN), 최악 = O(N2)**

   참조 : https://gmlwjd9405.github.io/2018/05/10/algorithm-quick-sort.html

5. **힙정렬 / O(NlogN)**

   참조 : https://gmlwjd9405.github.io/2018/05/10/algorithm-heap-sort.html

6. **Merge / Quick / Heap 정렬 비교**

   참조 : https://12216715011126.tistory.com/40