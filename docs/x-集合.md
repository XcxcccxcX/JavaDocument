# 集合 

> 作者：xcx   
> 时间：2020/5/11  15：00 

## ArrayList    
### 类成员变量     
- 
    ```java
      /**
         * Default initial capacity. 
         * 默认初始容量 10
         */
        private static final int DEFAULT_CAPACITY = 10;
        // 空列表数据。初始化时如果没有指定大小，则将此值赋予elementData
        private static final Object[] EMPTY_ELEMENTDATA = {};
        // 默认空列表数据。如果没有指定大小，那么将此值赋予elementData
        private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
        // 列表数据
        transient Object[] elementData; 
        // 列表大小
        private int size;
    ```
### 构造方法
ArrayList 有三个构造方法
1. 空构造方法    
    ```java
      /**
       * Constructs an empty list with an initial capacity of ten.
       * 构造一个初始容量为10的空集合。
       */
      public ArrayList() {
          this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
      }
    ```
2. 指定大小
    ```java
      /**
       * Constructs an empty list with the specified initial capacity.
       * 构造一个初始容量的空集合
       *
       * @param  initialCapacity  the initial capacity of the list  集合的初始长度
       * @throws IllegalArgumentException if the specified initial capacity
       *         is negative 指定的初始容量为负数 抛出异常 IllegalArgumentException
       */
      public ArrayList(int initialCapacity) {
          if (initialCapacity > 0) {
              this.elementData = new Object[initialCapacity];
          } else if (initialCapacity == 0) {
              this.elementData = EMPTY_ELEMENTDATA;
          } else {
              throw new IllegalArgumentException("Illegal Capacity: "+
                                                 initialCapacity);
          }
      }
    ```
3. 指定初始集合   
    ```java 
       /**
       * Constructs a list containing the elements of the specified
       * collection, in the order they are returned by the collection's
       * iterator. 构造一个 
       *
       * @param c the collection whose elements are to be placed into this list 放到这个集合中的元素
       * @throws NullPointerException if the specified collection is null 如果指定的集合为空，则为NullPointerException
       */
      public ArrayList(Collection<? extends E> c) {
          elementData = c.toArray();
          if ((size = elementData.length) != 0) {
              // c.toArray might (incorrectly) not return Object[] (see 6260652)
              if (elementData.getClass() != Object[].class)
                  elementData = Arrays.copyOf(elementData, size, Object[].class);
          } else {
              // replace with empty array.
              this.elementData = EMPTY_ELEMENTDATA;
          }
      }
    ```
### 基本操作方法
1.  get方法 
    对 index 做有效性校验 如果参数合法，那么直接返回对应数组下标的数据。
    ```java 
        public E get(int index) {
            rangeCheck(index);

            return elementData(index);
        }

        private void rangeCheck(int index) {
        if (index >= size) 
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
        }
    ```

2. add方法
    add一共有两种实现方式，第一种是直接插入列表尾部，另一种是插入某个位置。
    ```java
    // 1. 直接插入列表尾部
    /**
     * Appends the specified element to the end of this list.
     * 将指定的元素追加到列表的末尾。
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
     public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    //2. 插入到某个位置
    /**
     * Inserts the specified element at the specified position in this
     * list. Shifts the element currently at that position (if any) and
     * any subsequent elements to the right (adds one to their indices).
     * 将指定的元素插入到列表的指定位置 
     * 将元素当前的位置(如果有的话)和后续的元素向右移动(在它们的索引中添加一个)。
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }
    ```
tips: 如果是直接插入尾部的话，那么只需调用 ensureCapacityInternal 方法做容量检测。如果空间足够，那么就插入，空间不够就扩容后插入。
      如果是插入的是某个位置，那么就需要将 index 及之后的所有元素后移一位，之后再将元素插入至 index 处。

3. remove方法   
    add一共有两种实现方式，第一种是删除某个index的元素，另一种是删除摸个指定元素。

    ```java
    // 1. 删除某个index位置上的元素
    /**
     * Removes the element at the specified position in this list.
     * 移除此list中指定位置的元素。
     * Shifts any subsequent elements to the left (subtracts one from their
     * indices).
     * 将后面的元素向左移一位
     *
     * @param index the index of the element to be removed
     * @return the element that was removed from the list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
    //上述代码的逻辑大致是这样的：首先做参数范围检查，接着将 index 位置后的所有元素都往前挪一位，最后减少列表大小。

    //2. 删除某个特定的元素。
     /**
     * Removes the first occurrence of the specified element from this list,
     * if it is present.  If the list does not contain the element, it is
     * unchanged.  More formally, removes the element with the lowest index
     * <tt>i</tt> such that
     * <tt>(o==null&nbsp;?&nbsp;get(i)==null&nbsp;:&nbsp;o.equals(get(i)))</tt>
     * (if such an element exists).  Returns <tt>true</tt> if this list
     * contained the specified element (or equivalently, if this list
     * changed as a result of the call).
     *
     * @param o element to be removed from this list, if present
     * @return <tt>true</tt> if this list contained the specified element
     */
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
    //上述代码的逻辑大致是：首先，遍历列表的所有元素，找到需要删除的元素索引，最后调用 fastRemove 方法删除该元素。

    /*
     * Private remove method that skips bounds checking and does not
     * return the value removed.
     * 用私有的方法 fastRemove 方法跳过边界检查，不返回删除值。
     */
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
    ```

4. 扩容
    扩容是 ArrayList 的核心方法，当插入的时候容量不足，便会触发扩容
    插入方法中调用了 ensureCapacityInternal方法 ensureCapacityInternal 方法直接调用 ensureExplicitCapacity 实现。
    ensureExplicitCapacity 方法首先判断容量是否足够，如果不够就调用 grow 方法扩容。

    grow 方法的大致逻辑为：将原有列表容量扩大为原来的 1.5 倍。如果还是不够，那么直接扩大为最小容量（minCapacity）
    ```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

     private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

     /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    ```
### 总结  
- 底层基于数组实现，读取速度快，修改速度慢（读取时间复杂度O(1)，修改时间复杂度O(N)）
- 非线程安全。
- ArrayList 每次默认扩容为原来的 1.5 倍。

## LinkedList
- linkedList是通过一个双向链表来实现的，它允许插入所有元素，包括null,它是线程不安全的

### 类成员变量
    ```java
    //链表大小
     transient int size = 0;

    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
     //首节点
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
     //尾结点
    transient Node<E> last;

    //Node节点
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
    ```

### 构造方法
    ```java
    //空参构造
    /**
     * Constructs an empty list.
     */
    public LinkedList() {
    }

    //集合参数 构造
    /**
     * Constructs a list containing the elements of the specified
     * collection, in the order they are returned by the collection's
     * iterator.
     *
     * @param  c the collection whose elements are to be placed into this list
     * @throws NullPointerException if the specified collection is null
     */
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }

    ```

### 核心操作方法
1. 查找
    ```java
    //查找集合中指定位置的数据
    //LinkedList 底层基于链表结构，无法向 ArrayList 那样随机访问指定位置的元素。LinkedList 查找过程要稍麻烦一些，需要从链表头结点（或尾节点）向后查找，时间复杂度为 O(N)
    /**
     * Returns the element at the specified position in this list.
     *
     * @param index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E get(int index) {
        //判定 索引是否合规
        checkElementIndex(index);
        return node(index).item;
    }

    //查找节点 通过遍历的方式定位目标位置的节点 获取到节点后，取出节点存储的值返回即可
    //通过比较 index 与节点数量 size/2 的大小，决定从 头结点 还是 尾节点 进行查找
    /**
     * Returns the (non-null) Node at the specified element index.
     */
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }

    //返回list 中的第一个节点
     /**
     * Returns the first element in this list.
     *
     * @return the first element in this list
     * @throws NoSuchElementException if this list is empty
     */
    public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }

    //返回list 中的最后一个节点
    /**
     * Returns the last element in this list.
     *
     * @return the last element in this list
     * @throws NoSuchElementException if this list is empty
     */
    public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }
    ```

2. 插入
    - 直接插入队列尾部
    ```java
    public boolean add(E e) {
        linkLast(e);
        return true;
    } 

    //添加到最后的位置 最后增加链表的大小 
    /**
     * Links e as last element.
     */
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
    ```
    - 插入指定位置
    ```java
    //插入到list 中的指定位置
    //如果我们插入的位置还是链表尾部，那么还是会调用 linkLast 方法。否则调用 node 方法取出插入位置的节点，再调用 linkBefore 方法插入
    /**
     * Inserts the specified element at the specified position in this list.
     * Shifts the element currently at that position (if any) and any
     * subsequent elements to the right (adds one to their indices).
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
    ```

3. 删除  
    删除节点有两个方法，第一个是移除特定的元素，第二个是移除某个位置的元素。
    - 移除特定的元素
    ```java
    // //遍历找到删除的节点，之后调用 unlink() 方法解除引用
    /**
     * Removes the first occurrence of the specified element from this list,
     * if it is present.  If this list does not contain the element, it is
     * unchanged.  More formally, removes the element with the lowest index
     * {@code i} such that
     * <tt>(o==null&nbsp;?&nbsp;get(i)==null&nbsp;:&nbsp;o.equals(get(i)))</tt>
     * (if such an element exists).  Returns {@code true} if this list
     * contained the specified element (or equivalently, if this list
     * changed as a result of the call).
     *
     * @param o element to be removed from this list, if present
     * @return {@code true} if this list contained the specified element
     */
    public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }

    //将待删除的节点 与前后节点解除关联 并将前后两个节点 关联起来
     /**
     * Unlinks non-null node x.
     */
    E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
   
    ```
### 总结    
- 底层基于链表实现，修改速度快，读取速度慢（读取时间复杂度O(N)，修改时间复杂度O(N)，因为要查找元素，所以修改也是O(N)）。
- 非线程安全。
- 与 ArrayList 不同，LinkedList 没有容量限制，所以也没有扩容机制。

## Vector   
    Vector 的底层实现以及结构与 ArrayList 完全相同 
    只是在某一些细节上会有所不同 线程安全  扩容大小

- 线程安全
    ArrayList 是线程不安全的，只能在单线程环境下使用。而 Vector 则是线程安全的
    Vector 的实现是在每一个可能发生线程安全的方法加上 synchronized 关键字 这样就使得任何时候只有一个线程能够进行读写，这样就保证了线程安全
    ```java
    public synchronized E get(int index) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);

    return elementData(index);
    }
    public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }
    ```

- 扩容机制
    ArrayList 类似，Vector 在插入元素时也会检查容量并扩容。在 Vector 中这个方法是：ensureCapacityHelper。
    与ArrayList 唯一的区别就是 Vector 的扩容大小 vector 扩容是 原来的两倍 ArrayList 则是扩大为原来的 1.5 倍。
    ```java
    /**
     * Appends the specified element to the end of this Vector.
     *
     * @param e element to be appended to this Vector
     * @return {@code true} (as specified by {@link Collection#add})
     * @since 1.2
     */
    public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }

    /**
     * This implements the unsynchronized semantics of ensureCapacity.
     * Synchronized methods in this class can internally call this
     * method for ensuring capacity without incurring the cost of an
     * extra synchronization.
     *
     * @see #ensureCapacity(int)
     */
    private void ensureCapacityHelper(int minCapacity) {
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

     private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        // 如果 capacityIncrement 大于 0，那么就按照 capacityIncrement 去扩容，否则扩大为原来的 2倍
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    ```
### 总结    
- Vector 与 ArrayList 在实现方式上是完全一致的  在某些方法有些许不同
- Vector 是线程安全的，而 ArrayList 是线程不安全的。Vector 直接使用 synchronize 关键字实现同步。
- Vector 默认扩容为原来的 2 倍，而 ArrayList 默认扩容为原来的 1.5 倍。
 


## Stack    
    Stack 是先进后出的栈结构，其并不直接实现具体的逻辑，而是通过继承 Vector 类，调用 Vector 类的方法实现。  
    Stack 类代码非常简单，其有 3 个核心方法：push、pop、peek

1. push
    ```java
    //直接调用 父类 Vector中的addElement 将元素插入数组的尾部
     /**
     * Pushes an item onto the top of this stack. This has exactly
     * the same effect as:
     * <blockquote><pre>
     * addElement(item)</pre></blockquote>
     *
     * @param   item   the item to be pushed onto this stack.
     * @return  the <code>item</code> argument.
     * @see     java.util.Vector#addElement
     */
    public E push(E item) {
        addElement(item);

        return item;
    }
    ``` 
    
2.  pop 
    ```java
    // pop 方法调用 Vector 的 removeElementAt 方法，删除了一个元素
    // 其删除的是数组最后一个元素，而不是第一个元素 len - 1
     /**
     * Removes the object at the top of this stack and returns that
     * object as the value of this function.
     *
     * @return  The object at the top of this stack (the last item
     *          of the <tt>Vector</tt> object).
     * @throws  EmptyStackException  if this stack is empty.
     */
    public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }
    ```

3. peek
    ```java
    // peek 方法直接返回列表最后一个元素。
     /**
     * Looks at the object at the top of this stack without removing it
     * from the stack.
     *
     * @return  the object at the top of this stack (the last item
     *          of the <tt>Vector</tt> object).
     * @throws  EmptyStackException  if this stack is empty.
     */
    public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }
    ```
### 总结
- 利用 Vector 实现了一个线程安全的栈结构
- 底层采用 Vector 实现，因此其也是采用数组实现，也是线程安全的
- 先进后出的栈结构

##  HashSet 
    HashSet是Set集合的哈希实现 实现了Set接口  并继承了AbstractSet抽象类      

    ```java
    public class HashSet<E>
        extends AbstractSet<E>
        implements Set<E>, Cloneable, java.io.Serializable
    ```

1. 类成员变量 
    ```java
    // HashSet内部使用的是HashMap存储
     private transient HashMap<E,Object> map;

    // 存储在value上的值
    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();
    ```

2. 构造方法
    ```java
    //空参构造
    /**
     * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
     * default initial capacity (16) and load factor (0.75).
     */
    public HashSet() {
        map = new HashMap<>();
    }

    //初始集合 构造
    /**
     * Constructs a new set containing the elements in the specified
     * collection.  The <tt>HashMap</tt> is created with default load factor
     * (0.75) and an initial capacity sufficient to contain the elements in
     * the specified collection.
     *
     * @param c the collection whose elements are to be placed into this set
     * @throws NullPointerException if the specified collection is null
     */
    public HashSet(Collection<? extends E> c) {
        map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
        addAll(c);
    }

    //明确初始容量和装载因子的构造器
    /**
     * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
     * the specified initial capacity and the specified load factor.
     *
     * @param      initialCapacity   the initial capacity of the hash map
     * @param      loadFactor        the load factor of the hash map
     * @throws     IllegalArgumentException if the initial capacity is less
     *             than zero, or if the load factor is nonpositive
     */
    public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    }

    //仅明确初始容量的构造器（装载因子默认0.75）
    /**
     * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
     * the specified initial capacity and default load factor (0.75).
     *
     * @param      initialCapacity   the initial capacity of the hash table
     * @throws     IllegalArgumentException if the initial capacity is less
     *             than zero
     */
    public HashSet(int initialCapacity) {
        map = new HashMap<>(initialCapacity);
    }

    /**
     * Constructs a new, empty linked hash set.  (This package private
     * constructor is only used by LinkedHashSet.) The backing
     * HashMap instance is a LinkedHashMap with the specified initial
     * capacity and the specified load factor.
     *
     * @param      initialCapacity   the initial capacity of the hash map
     * @param      loadFactor        the load factor of the hash map
     * @param      dummy             ignored (distinguishes this
     *             constructor from other int, float constructor.)
     * @throws     IllegalArgumentException if the initial capacity is less
     *             than zero, or if the load factor is nonpositive
     */
    HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
    ```

    3. 基本方法
    ```java
    add
    //add 方法直接调用了 HashMap 对象的 put 方法。如果 Set 集合插入成功，那么就返回 true，否则返回 false。
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }

    remove
    //remove 方法直接调用了 HashMap 对象的 remove 方法。如果删除成功，就返回 true，否则返回 false。
    public boolean remove(Object o) {
        return map.remove(o)==PRESENT;
    }

    ```  
### 总结
    HashSet 底层直接借用的 HashMap实现

## LinkedHashSet    
    linkedHashSet 是 Set的实现 并且继承了 HashSet 在此基础上维护了元素的插入顺序。  

    ```java
    public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
    ``` 
1. 构造方法 
    LinkedHashSet 调用的都是 HashSet 的 三个参数的构造方法 !! 
    ```java
    //LinkedHashSet 调用的都是 HashSet 的 三个参数的构造方法 !! 
    //方法如下   维护了元素的插入顺序原因 使用的是 LinkedHashMap  可见 LinkedHashMap 源码 ↓
    HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }

    -------------------------------------------------------------

    public LinkedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor, true);
    }

    /**
     * Constructs a new, empty linked hash set with the specified initial
     * capacity and the default load factor (0.75).
     *
     * @param   initialCapacity   the initial capacity of the LinkedHashSet
     * @throws  IllegalArgumentException if the initial capacity is less
     *              than zero
     */
    public LinkedHashSet(int initialCapacity) {
        super(initialCapacity, .75f, true);
    }

    /**
     * Constructs a new, empty linked hash set with the default initial
     * capacity (16) and load factor (0.75).
     */
    public LinkedHashSet() {
        super(16, .75f, true);
    }

    /**
     * Constructs a new linked hash set with the same elements as the
     * specified collection.  The linked hash set is created with an initial
     * capacity sufficient to hold the elements in the specified collection
     * and the default load factor (0.75).
     *
     * @param c  the collection whose elements are to be placed into
     *           this set
     * @throws NullPointerException if the specified collection is null
     */
    public LinkedHashSet(Collection<? extends E> c) {
        super(Math.max(2*c.size(), 11), .75f, true);
        addAll(c);
    }
    ```
### 总结
-  LinkedHashSet 是在 HashSet 的基础上，维护了元素的插入顺序。虽然 LinkedHashSet 使用了 HashSet 的实现，但其却调用了 LinkedHashMap 作为最终实现，从而实现了对插入元素顺序的维护


## TreeSet  
    TreeSet 是Set集合的红黑树实现 内部直接使用 TreeMap 对象实现

    ```java
    //TreeSet 实现了NavigableSet接口 
    public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable

    //NavigableSet接口继承了SortedSet接口
    public interface NavigableSet<E> extends SortedSet<E>

    //SortedSet接口继承了Set接口
    SortedSet<E> extends Set<E>
    ```
1. 类成员变量
    ```java
     /**
     * The backing map.
     */
     // 具体的实现类
    private transient NavigableMap<E,Object> m;

    // Map的value
    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();
    ```

2. 构造方法
    ```java

    //指定实现类型
     /**
     * Constructs a set backed by the specified navigable map.
     */
    TreeSet(NavigableMap<E,Object> m) {
        this.m = m;
    }

    //默认采用TreeMap实现
    /**
     * Constructs a new, empty tree set, sorted according to the
     * natural ordering of its elements.  All elements inserted into
     * the set must implement the {@link Comparable} interface.
     * Furthermore, all such elements must be <i>mutually
     * comparable</i>: {@code e1.compareTo(e2)} must not throw a
     * {@code ClassCastException} for any elements {@code e1} and
     * {@code e2} in the set.  If the user attempts to add an element
     * to the set that violates this constraint (for example, the user
     * attempts to add a string element to a set whose elements are
     * integers), the {@code add} call will throw a
     * {@code ClassCastException}.
     */
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }

    //指定TreeMap 的比较器
    /**
     * Constructs a new, empty tree set, sorted according to the specified
     * comparator.  All elements inserted into the set must be <i>mutually
     * comparable</i> by the specified comparator: {@code comparator.compare(e1,
     * e2)} must not throw a {@code ClassCastException} for any elements
     * {@code e1} and {@code e2} in the set.  If the user attempts to add
     * an element to the set that violates this constraint, the
     * {@code add} call will throw a {@code ClassCastException}.
     *
     * @param comparator the comparator that will be used to order this set.
     *        If {@code null}, the {@linkplain Comparable natural
     *        ordering} of the elements will be used.
     */
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }

    //指定初始集合
    /**
     * Constructs a new tree set containing the elements in the specified
     * collection, sorted according to the <i>natural ordering</i> of its
     * elements.  All elements inserted into the set must implement the
     * {@link Comparable} interface.  Furthermore, all such elements must be
     * <i>mutually comparable</i>: {@code e1.compareTo(e2)} must not throw a
     * {@code ClassCastException} for any elements {@code e1} and
     * {@code e2} in the set.
     *
     * @param c collection whose elements will comprise the new set
     * @throws ClassCastException if the elements in {@code c} are
     *         not {@link Comparable}, or are not mutually comparable
     * @throws NullPointerException if the specified collection is null
     */
    public TreeSet(Collection<? extends E> c) {
        this();
        addAll(c);
    }


    //指定初始集合 以及比较器
    /**
     * Constructs a new tree set containing the same elements and
     * using the same ordering as the specified sorted set.
     *
     * @param s sorted set whose elements will comprise the new set
     * @throws NullPointerException if the specified sorted set is null
     */
    public TreeSet(SortedSet<E> s) {
        this(s.comparator());
        addAll(s);
    }
    ```


## HashMap  
    HashMap 是Map 的实现 继承了 AbstractMap抽象类

    ```java
    public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable
    ```
