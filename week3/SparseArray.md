## SparseArray
- Android에서 제공하는 HashMap과 유사하게 정수 키를 객체 값에 매핑하는 자료구조
- key는 Int로 고정되어 있으며 value를 원하는 객체 타입으로 지정할 수 있음

### SparseArray 장점
- SparseArray는 아래와 같은 장점이 있다고 공식문서에서 설명한다
- 메모리 효율성: AutoBoxing 작업을 거치지 않고 HashMap 내부적으로 사용하는 Entry같은 추가적인 클래스를 사용하지 않아 효율적임
- 성능: 중간 크기의 데이터 셋에서 더 나은 성능을 제공함
- Null 키값 사용 불가: Key로 null을 사용할 수 없음

### SparseArray 단점
- SparseArray의 요소 접근은 키 조회를 위해 이진 탐색을 (binarysearch) 사용하기 때문에 매우 큰 데이터 셋의 경우 HashMap보다 느림
- 정수 키만 사용 가능: 정수 키로 제한되어 다른 유형의 키가 필요한 사용 사례에는 적합하지 않음

### Example
- key: Int, value: Int인 자료구조에 1000개의 데이터를 저장하는 상황을 가정하고 SparseArray와 HashMap을 비교해본다
- SparseArray의 데이터 구조를 간략화하여 나타내면 아래와 같다
```.java
class SparseIntArray {
    int[] keys;
    int[] values;
    int size;
}

```.java
class HashMap<K, V> {
    Entry<K, V>[] table;
    Entry<K, V> forNull;
    int size;
    int modCount;
    int threshold;
    Set<K> keys
    Set<Entry<K, V>> entries;
    Collection<V> values;
}
```

- HashMap을 사용하면 훨씬 더 많은 메모리가 필요하다
