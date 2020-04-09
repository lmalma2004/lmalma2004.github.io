---
layout: post
title: Segment Tree
description: Segment Tree
date:   2020-04-09
categories:
---
## Segment Tree

#### 세그먼트 트리란?

저장된 자료들을 적절히 전처리해 그들에 대한 질의들을 빠르게 대답할 수 있도록 하는 자료구조



세그먼트트리의 기본적인 아이디어

주어진 배열의 구간들을 표현하는 이진트리를 만드는 것

(구간트리는 비교적 '꽉 찬' 이진 트리이다)

이때 구간 트리의 루트는 항상 배열의 전체 구간[0, n-1]을 표현하며, 한 트리의 왼쪽 자식과 오른쪽 자식은 각각 해당 구간의 왼쪽 반과 오른쪽 반을 표현한다. 길이가 1인 구간을 표현하는 노드들은 구간트리의 리프가 된다.



![세그먼트트리](C:\Users\lmalm\Desktop\세그먼트트리.png)

[길이 15인 배열을 표현하는 구간 트리가 저장하는 구간들]



맨 위가 루트가 표현하는 구간이고, 양쪽 아래는 루트의 두 자식 노드들이 표현하는 구간이다.

이때 구간 트리는 노드마다 해당 구간에 대한 계산 결과를 저장해 둔다.

이런 전처리 과정을 수행해 두면 어떤 구간[i, j]가 주어지더라도 이 구간을 구간 트리의 노드에 포함된 구간들의 합집합으로 표현할 수 있다.

예를 들어 [6, 12] 구간은 그림에서 세 구간 ( [6,7], [8, 11], [12,12] )의 합집합으로 표현할 수 있다.

이때 각 구간의 계산결과는 미리 계산해 두었으니 이 셋을 사용하여 원하는 답을 찾는다.



어떤 구간이 주어지건 간에 답을 찾기 위해 우리가 고려해야 하는 구간의 수는 O(logN) 이다.

따라서 O(N) 대신에 O(logN)시간에 이런 질문들에 대답할 수 있게 된다.



#### 구간 트리의 초기화

이제 배열 array[]가 주어질 때, 각 노드마다 해당 구간의 최소치를 계산하는 함수 init()을 구현할 수 있다.

```c++
//배열의 구간 최소 쿼리를 해결하기 위한 구간 트리의 구현
struct RMQ{
    //배열의 길이
    int n;
    //각 구간의 최소치
    vector<int> rangeMin;
    RMQ(const vector<int>& array){
        n = array.size();
        rangeMin.resize(n * 4);
        init(array, 0, n - 1, 1);
    }
    //node 노드가 array[left, right] 배열을 표현할 때
    //node를 루트로 하는 서브트리를 초기화하고, 이 구간의 최소치를 반환한다.
    int init(const vector<int>& array, int left, int right, int node){
        if(left == right)
            return rangeMin[node] = array[left];
        int mid = (left + right) / 2;
        int leftMin = init(array, left, mid, node * 2);
        int rightMin = init(array, mid + 1, right, node * 2 + 1);
        return rangeMin[node] = min(leftMin, rightMin);
    }
};
```



#### 구간 트리의 질의 처리

초기화를 했으면 이제 임의의 구간의 최소치를 구할 준비가 됐다.

이것을 구간 트리에서의 질의(query) 연산이라고 부른다. 

질의 연산은 구간 트리에서의 순회를 응용해 간단하게 구현할 수 있다.

```c++
//node가 표현하는 범위[nodeLeft, nodeRight]와 우리가 최소치를 찾기 원하는 구간[left, right]의
//교집합의 최소원소를 반환한다.
int query(left, right, node, nodeLeft, nodeRight);
```

루트인 1번 노드는 배열 전체 범위인 [0, n - 1]을 표현하기 때문에, 

[i, j] 구간의 최소치는 query(i, j, 1, 0, n-1)로 구할 수 있다.

query()는 우선 node가 표현하는 구간 [nodeLeft, nodeRight]와 최소치를 찾을 구간 [left, right]의

교집합을 구한 뒤, 그에 따라 서로 다른 값을 반환한다.



- 교집합이 공집합인 경우 : 두 구간은 서로 겹치지 않는다. 따라서 반환 값은 존재하지 않는다.

- 교집합이[nodeLeft, nodeRight]인 경우 : [left, right]가 노드가 표현하는 집합을 완전히 포함하는 경우

  이 노드에 미리 계산해 둔 최소치를 곧장 반환하면 된다.

- 이 외의 모든 경우 : 두 개의 자손 노드에 대해 query()를 재귀 호출한 뒤, 이 두 값 중 더 작은 값을 택한다.





```c++
struct RMQ{
    //..생략..
    //node가 표현하는 범위 array[nodeLeft, nodeRight]가 주어질 때
    //이 범위와 array[left..right]의 교집합의 최소치를 구한다.
    int query(int left, int right, int node, int nodeLeft, int nodeRight){
        //두 구간이 겹치지 않으면 아주 큰 값을 반환하여 무시한다.
        if(right < nodeLeft || nodeRight < left) return INT_MAX;
        //node가 표현하는 범위가 array[left..right]에 완전히 포함되는 경우
        if(left <= nodeLeft && nodeRight <= right)
            return rangeMin[node];
        //양쪽 구간을 나눠서 푼 뒤 결과를 합친다.
        int mid = (nodeLeft + nodeRight) / 2;
        return min(query(left, right, node * 2, nodeLeft, mid),
                  query(left, right, node * 2 + 1, mid + 1, nodeRight));
    }
    //query()를 외부에서 호출하기 위한 인터페이스
    int query(int left, int right){
        return query(left, right, 1, 0, n - 1);
    }
}
```





#### 구간 트리의 갱신

전처리를 통해 구간 트리를 생성한 다음에 원래 배열의 값이 바뀐다면 어떻게 해야할까?

처음부터 다시 구간 트리를 생성할 수도 있지만, 값이 하나 바뀔 때는 구간 트리를 빠른 시간에 갱신할 수 있다.

원래 배열의 index위치의 값이 newValue로 바뀌었다고 하자. 

이 위치를 포함하는 구간은 트리에 O(logN) (트리의 높이)개 있을 것이다.

따라서 이들만을 재계산하면 O(logN) 시간에 구간 트리를 갱신할 수 있다.

갱신 과정은 query()와 init()을 합친 것처럼 구현된다.

해당 노드가 표현하는 구간에 index가 포함되지 않는다면 그냥 무시하고, 포함된다면 재귀 호출해서

두 자손 구간의 최소치를 계산한 뒤 다시 최소치를 구해 주면 된다.

```c++
struct RMQ{
	//..생략..
    //array[index] = newValue로 바뀌었을 때 node를 루트로 하는
    //구간 트리를 갱신하고 노드가 표현하는 구간의 최소치를 반환한다.
    int update(int index, int newValue, int node, int nodeLeft, int nodeRight){
        //index가 노드가 표현하는 구간과 상관없는 경우엔 무시한다.
        if(index < nodeLeft || nodeRight < index)
            return rangeMin[node];
        //트리의 리프까지 내려온 경우
        if(nodeLeft == nodeRight) return rangeMin[node] = newValue;
        int mid = (nodeLeft + nodeRight) / 2;
        return rangeMin[node] = min(
        	update(index, newValue, node * 2, nodeLeft, mid),
        	update(index, newValue, node * 2 + 1, mid + 1, nodeRight));
    }
    //update()를 외부에서 호출하기 위한 인터페이스
    int update(int index, int newValue){
        return update(index, newValue, 1, 0, n - 1);
    }
};
```





###### *출처 : 프로그래밍 대회에서 배우는 알고리즘 문제해결전략 2권

