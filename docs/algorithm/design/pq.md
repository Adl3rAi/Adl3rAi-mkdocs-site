# Binary Heap designs Priority Queue

二叉堆是使用数组储存的二叉树

```java
int parent(int root) {
  return root / 2;
}
int left(int root) {
  return root * 2;
}
int right(int root) {
  return root * 2 + 1;
}
```

对于一个二叉堆而言，为了方便索引，`arr[0]`不储存数据，`arr[1]`为数组中的最大值（对于最大堆而言）

```java
public class MaxPQ <Key extends Comparable<Key>> {
  private Key[] pq;
  private int size = 0;
  public MaxPQ(int cap) {
    pq = (Key[]) new Comparable[cap+1];
  }
  public Key max() {
    return pq[1];
  }
  public void insert(Key e) {
    size++;
    pq[size] = e;
    swim(size);
  }
  public Key delMax() {
    Key max = pq[1];
    swap(1, size);
    pq[size] = null;
    size--;
    sink(1);
    return max;
  }
  private void swim(int x) {
    while(x > 1 && less(parent[x],x)) {
      swap(parent[x],x);
      x = parent(x);
    }
  }
  private void sink(int x) {
    while(left(x) <= size) {
      int max = left(x);
      if(right(x) <= size && less(max, right(x))) {
        max = right(x);
      }
      if(less(max, x)) break;
      swap(x,max);
      x = max;
    }
  }
  private void swap(int i, int j) {
    Key temp = pq[i];
    pq[i] = pq[j];
    pq[j] = temp;
  }
  private boolean less(int i, int j) {
    return pq[i].compareTo(pq[j]) < 0;
  }
}
```

