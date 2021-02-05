CopyOnWriteArrayList是JUC包下的一个集合容器，可以认为是线程安全版本的ArrayList，因为它同样继承了List接口。

``` java

    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock(); //上锁，只允许一个线程进入
        try {
            Object[] elements = getArray(); // 获得当前数组对象
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);//拷贝到一个新的数组中
            newElements[len] = e;//插入数据元素
            setArray(newElements);//将新的数组对象设置回去
            return true;
        } finally {
            lock.unlock();//释放锁
        }
    }

```

通过可重入锁实现线程安全的add方法，至于为什么要拷贝到一个新的数组里再add，可以认为是jdk规约的一种风格，为了和下面的安全失败的设计实现保持一致。





``` java

        CopyOnWriteArrayList<String> copyOnWriteArrayList = new CopyOnWriteArrayList<>();

        copyOnWriteArrayList.add("first");
        copyOnWriteArrayList.add("second");

        Iterator<String> iterator = copyOnWriteArrayList.iterator();

        copyOnWriteArrayList.add("third");

        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        //output:
        
        first
        second

```

上述代码说明在获得迭代器时获得的其实是当前版本一个快照的迭代器，之后添加了一个新的元素，但是快照的版本不会改变。

如果把上述代码里的集合容器修改为ArrayList，在遍历过程中会抛出ConcurrentModificationException异常，这是因为ArrayList内部成员私有类Itr里有一个modCount成员，该成员记录集合的修改次数，初始化迭代器时会用该成员初始化expectedModCount成员，以后通过迭代器遍历时每次都会把迭代器的modCount和集合的expectedModCount比较，出现不一致时就抛出异常。

以上这种机制，ArrayList的机制称为快速失败fail-fast, CopyOnWriteArrayList称为安全失败fail-safe。



利用CopyOnWriteArrayLis可以实现其他线程安全下的集合容器，例如同样是JUC包下的CopyOnWriteSet，通过内部代理对象CopyOnWriteArrayList维护数据的增删改，再通过set保证数据是唯一的。

